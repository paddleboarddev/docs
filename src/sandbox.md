# Sandboxed Execution & MCP

PaddleBoard can run agent-invoked code and MCP servers inside an isolated container instead
of directly on your host.

## Secure agent sandbox

Tool calls that execute code run inside an ephemeral `ubuntu:latest` container via
[Podman](https://podman.io/) + the [`runsc`](https://gvisor.dev/) (gVisor) kernel runtime.
Your project is bind-mounted; the rest of the host filesystem is not exposed. Permissions
still flow through the normal approve / deny / always-allow UI.

A status-bar shield shows live prereq status. If Podman/gVisor aren't available, behavior is
governed by `paddleboard_sandbox.on_missing_runtime`:

- `block` (default) — opens the install modal.
- `fall_back_to_host` — run on the host instead.
- `warn_once` — warn, then run on the host.

## Sandboxed MCP servers

A `sandboxed_stdio` context-server transport runs MCP servers inside Podman + gVisor too,
proxying stdin/stdout transparently so the JSON-RPC framing is unchanged. It's opt-in per
server; the plain `stdio` transport still works for servers that don't need isolation.

Manage servers from the **MCP** tab of the [AI Dock](./ai-dock.md), or the dedicated
**MCP Servers** settings page (command palette → `zed: Mcp Servers`), which lists configured
servers and surfaces status and logs without hand-editing JSON.
