# Agent Instructions: Rules for Every Project, and for One

Two plain Markdown files decide what the **PaddleBoard Agent** is told before you
type anything:

- A **personal `AGENTS.md`** in PaddleBoard's config folder. It applies to every
  project you open: how you like commits written, which tools you prefer, what
  never to do.
- A **project rules file** at the root of a project. It applies to that project
  only: how to build and test it, its conventions, what to leave alone.

Both are optional. With neither, the agent gets PaddleBoard's own system prompt
and nothing else.

Each can also have an optional **`PADDLEBOARD.md`** beside it, for instructions
that only make sense in PaddleBoard. It is read **in addition to** the other file,
never instead of it; see [PaddleBoard-only instructions](#paddleboard-only-instructions-paddleboardmd).

These files apply to the native PaddleBoard Agent. External agents (Claude Code,
Codex, …) read their own instruction files, such as `~/.claude/CLAUDE.md`.

## Your personal `AGENTS.md`

| Platform | Path |
| --- | --- |
| macOS | `~/.config/paddleboard/AGENTS.md` |
| Linux | `$XDG_CONFIG_HOME/paddleboard/AGENTS.md`, usually `~/.config/paddleboard/AGENTS.md` |
| Windows | `%APPDATA%\PaddleBoard\AGENTS.md` |

To open it, run **agent: open global AGENTS.md rules** from the command palette,
or use the agent panel's menu: **Rules → Open Global Rules (AGENTS.md)**.

Write it as you would brief a colleague:

```markdown
# How I work

- Commit messages: imperative subject under 72 characters, a body that says why.
- Python: use `uv`, never raw `pip`.
- Never push to a branch you didn't create without asking first.
- When a task is ambiguous, ask one question rather than guessing.
```

- **It is live.** PaddleBoard watches the file, so an edit applies to the next
  message without a restart.
- **Empty means off.** A missing, empty or whitespace-only file adds nothing.
- **It is read in full** and goes into the system prompt of every PaddleBoard
  Agent thread, under "Personal `AGENTS.md`".

### Sharing one file with your other agents

If you already keep agent-neutral instructions in one place for Claude Code,
Gemini or another tool, point PaddleBoard at the same file instead of copying it:

```sh
ln -s ~/path/to/your/AGENTS.md ~/.config/paddleboard/AGENTS.md
```

One file then feeds every agent you use. If edits through the link don't show up
on the next message, restart PaddleBoard to reload it.

## Project rules files

At the root of each folder in your project, PaddleBoard looks for these names
**in this order** and reads **the first one it finds**:

1. `.rules`
2. `.cursorrules`
3. `.windsurfrules`
4. `.clinerules`
5. `.github/copilot-instructions.md`
6. `AGENT.md`
7. `AGENTS.md`
8. `CLAUDE.md`
9. `GEMINI.md`

This means a project that already has instructions for another tool works with
PaddleBoard as it is. It also means **only one file is read per folder**: a repo
with both `AGENTS.md` and `CLAUDE.md` gives the agent `AGENTS.md` alone. If you
want PaddleBoard to read a particular one, make it the earliest name on the list,
or have the others point to it (for example, a `CLAUDE.md` symlinked to
`AGENTS.md`).

In a project with several root folders, each folder contributes its own rules
file.

## PaddleBoard-only instructions: `PADDLEBOARD.md`

`AGENTS.md` works best kept agent-neutral, so the same file can brief Claude Code,
Gemini and PaddleBoard alike. Anything that only applies to PaddleBoard's own
agent goes in a `PADDLEBOARD.md` instead. It **adds to** the `AGENTS.md` or rules
file at the same level and never replaces it, so you can share one `AGENTS.md`
everywhere and still tell PaddleBoard something extra.

| Level | Where | Read with |
| --- | --- | --- |
| Personal | `PADDLEBOARD.md` in the same config folder as your personal `AGENTS.md` (`~/.config/paddleboard/PADDLEBOARD.md` on macOS) | Your personal `AGENTS.md` |
| Project | `PADDLEBOARD.md` at the root of a project folder | That folder's rules file |

To open the personal one, run **agent: open global PADDLEBOARD.md rules** from the
command palette, or use the agent panel's menu: **Rules → Open Global Rules
(PADDLEBOARD.md)**. The entry is there whenever the Rules section is, so you can
create the file from it.

```markdown
# In PaddleBoard

- Use the Spotter check before calling a task done.
- Prefer the built-in git panel over shelling out to git for commits.
```

- **Both are optional, and empty means off,** as with `AGENTS.md`.
- **Both are live.** Edits apply to the next message without a restart.
- **The project one is not on the first-match list above.** A project with
  `AGENTS.md` and `PADDLEBOARD.md` gives the agent both.

## Which wins

The agent reads up to four layers, most general first:

1. Your personal `AGENTS.md`
2. Your personal `PADDLEBOARD.md`
3. The project's rules file
4. The project's `PADDLEBOARD.md`

The agent is told that **each layer may override the ones before it**. Project
rules beat personal ones, so a project that needs `npm` can say so even when your
personal file prefers `pnpm`; and at each level, `PADDLEBOARD.md` beats the
`AGENTS.md` or rules file it sits beside.

## How this differs from personas and skills

| | Answers | Where |
| --- | --- | --- |
| **Instructions** (this page) | What to do and how: conventions, tools, limits | Personal `AGENTS.md` and `PADDLEBOARD.md`, project rules file and `PADDLEBOARD.md` |
| **[Personas](./personas.md)** | Who the agent is: voice, values, what it pushes back on | `PERSONA.md`, `*.persona.md` |
| **Skills** | Extra abilities the agent can call on | Skills folders (agent panel menu → **Manage Skills…**) |

All three can be used together. A persona doesn't replace your instructions, and
your instructions don't need to describe a personality.
