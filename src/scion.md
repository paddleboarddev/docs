# Scion: Isolated Parallel Agents

[Scion](https://github.com/GoogleCloudPlatform/scion) runs "deep agents" as isolated,
concurrent processes — each in its own container and git worktree. PaddleBoard integrates
with a local Scion daemon so you can launch and watch those agents from the editor, and even
delegate subtasks to them from your own agent.

## Enable it (opt-in)

Scion is **off by default**. Turn it on in your settings:

```json
{
  "paddleboard_scion": { "enabled": true }
}
```

Then install the Scion CLI and initialize it:

```sh
go install github.com/GoogleCloudPlatform/scion/cmd/scion@latest
scion init --machine   # one-time, sets up the container runtime
scion init             # in your project
```

Scion needs a container runtime (Docker / Podman / Apple Container).

## Use it

- **`scion: Start Agent`** (command palette) opens a modal — set a task, name, and template.
- The **Orchestration Panel** shows a **Scion Agents** section with each agent's phase
  (provisioning → running → stopped) and activity (working, thinking, waiting…). Right-click a
  row for **View Logs** (live-streaming), **Sync Changes** (pull its worktree changes into
  your project), or **Stop Agent**.
- **Delegation:** when enabled, your agent gains a `spawn_scion_agent` tool — it can hand a
  subtask to a container + worktree-isolated Scion agent (instead of an in-process sub-agent
  that shares your workspace), wait for it, and return the result.

## Telemetry (optional)

Scion can export agent-lifecycle traces over OpenTelemetry. See the
[Settings Reference](./settings-reference.md) for `paddleboard_otel`.
