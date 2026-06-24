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

## Build an MCP for any service

When a service has no MCP server, the **Build an MCP** button at the top of the AI Dock's
**MCP** tab generates one. You provide:

- a **service** (e.g. `Substack`),
- an optional **API-docs URL**,
- an optional **auth env-var name** (e.g. `SUBSTACK_API_KEY`), and
- a sentence describing **what it should do**.

PaddleBoard then seeds a visible agent thread that researches the service's API, writes a
Python (FastMCP) server, tests it in the sandbox, and installs it into the AI Dock. Because
the thread is visible, you can watch the build and course-correct.

The install runs **host-side** (via the `install_mcp_server` tool, which you approve): it
writes the server under PaddleBoard's data directory — on macOS
`~/Library/Application Support/PaddleBoard/mcp_servers/<id>/` — and registers it as a plain
`stdio` context server that runs with `uv run`. The generated server reads its API key from
the environment PaddleBoard was launched with, so **no secret is ever written to settings**:
just export the variable (e.g. `export SUBSTACK_API_KEY=…`) in the shell you launch
PaddleBoard from. Once installed, the server appears in the MCP tab and starts automatically.

> **Requirements.** [`uv`](https://docs.astral.sh/uv/) must be on your `PATH` — it provisions
> the server's Python dependencies on first launch. Open a project before building, since the
> agent needs an active workspace to work in.
