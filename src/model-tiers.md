# Model Tiers: Local First, Escalate on Failure

Most of an agent session is routine: a field added here, a docstring there, a test
made to pass. A local model handles that for free. The hard 20% needs Claude or
Gemini. **Model tiers** let the cheap model do the work and bring in a stronger one
only when the work actually fails.

What decides "failed" is your project's own check, a command like `cargo test`, not
a guess about how hard the task looked.

## How it works

1. You name your tiers, cheapest first, from models you already have configured.
2. You give the project a **check** command.
3. After any agent turn that edits files, PaddleBoard runs the check in the agent's
   terminal. It appears in the thread as **Check: `your command`**, with the same
   sandbox and the same permission prompt as any command the agent runs.
4. **Pass:** the turn ends.
5. **First failure:** the output goes back to the same model, which gets one more try.
   The check runs again when it says it's done, even if it changed nothing.
6. **Second failure:** the run stops with a **Blocker card** in the thread. It offers
   the stronger tiers. Choose one and the thread switches to that model and continues
   with the full history plus a **Blocker Summary**: the check command, its output,
   the files changed, and which models tried.

The thread's current model decides which tier it is on. Start on your local tier
with the model picker as usual (`cmd-alt-/` on macOS), or set it as your default model.

## Setting it up

Tiers usually live in your user settings, because they describe your models. The
check usually lives in the project's `.zed/settings.json`, because it describes the
project. Both use the same `paddleboard_tiers` key, and project values win.

User settings:

```json
{
  "paddleboard_tiers": {
    "enabled": true,
    "tiers": [
      { "name": "Local",   "model": { "provider": "ollama",    "model": "gemma3:12b" } },
      { "name": "Fast",    "model": { "provider": "google",    "model": "gemini-3.5-flash" } },
      { "name": "Precise", "model": { "provider": "anthropic", "model": "claude-sonnet-5" } }
    ]
  }
}
```

Project `.zed/settings.json`:

```json
{
  "paddleboard_tiers": {
    "check": { "command": "cargo test -p my_crate", "timeout_seconds": 300 }
  }
}
```

The `provider` and `model` values are the same ones the model picker and
`agent.default_model` use. Any provider PaddleBoard supports works in a tier,
including managed Local Models, Ollama, LM Studio and any OpenAI-compatible server.

## Settings

| Setting | Default | What it does |
|---|---|---|
| `enabled` | `false` | Turns tiers on. Nothing runs without a `check` too. |
| `tiers` | `[]` | The tiers, cheapest first. Each has a `name` and a `model`. |
| `check.command` | none | The one-line command that decides pass or fail. |
| `check.timeout_seconds` | `300` | A check that runs longer counts as failed. |
| `retries_per_tier` | `1` | Fix attempts on the same model before it stops. `1` is two strikes. |
| `escalation` | `"ask"` | `"ask"` stops and offers the next tier. `"auto"` moves up on its own. `"off"` stops and reports. |
| `auto_ceiling` | none | With `"auto"`, the highest tier it may reach without asking, by name. |

## Things to know

- **Escalation costs money, so it asks by default.** Moving from a local model to a
  paid one is the moment code leaves your machine. With `"auto"`, set
  `auto_ceiling` to keep it below your most expensive tier.
- **The check runs in the sandbox** when sandboxing is on, which has no network by
  default. Use a check that works offline, such as `cargo test --offline`, or one
  that doesn't fetch dependencies.
- **Pick a check that is fast and specific.** It runs after every editing turn. The
  tests for the crate you are working on beat the whole workspace's suite.
- **Turns that only read or answer don't run the check.** Only edits do.
- **Subagents don't run it.** The thread that spawned them runs its own check.
- **A tier whose model isn't configured** still appears on the Blocker card, but it
  can't be chosen until you add its provider.
- **Your manual choice wins.** Pick a model yourself at any time; tiers never switch
  it back.

## Small local models

A 4B to 12B local model does best with a narrow job. If you see it skipping steps,
give the local tier its own agent profile with fewer tools, and keep the check
specific so its failures are easy to read.
