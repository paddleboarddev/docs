# Sandboxed Execution & MCP

PaddleBoard can run agent-invoked code and MCP servers inside an isolated container instead
of directly on your host.

## Secure agent sandbox

Tool calls that execute code run in an isolated environment rather than on your host. Your
project is bind-mounted; the rest of the host filesystem is not exposed. Permissions still
flow through the normal approve / deny / always-allow UI.

There are two tiers.

**Native — zero-install, and the default on macOS.** Apple's `container` framework on
macOS 26+, otherwise a bundled libkrun microVM. Nothing to install; it ships with
PaddleBoard. On Linux it's libkrun over KVM.

**Podman + gVisor.** An ephemeral `ubuntu:latest` container via
[Podman](https://podman.io/) with the [`runsc`](https://gvisor.dev/) kernel runtime. This is
the default on Linux and Windows, and you install it yourself.

> ⚠️ **The native tier currently covers one-shot commands only.** Long-lived services,
> sandboxed MCP transports, and REPL kernels still require Podman + gVisor — with the native
> tier alone, those paths behave exactly as they would with no sandbox stack installed, and
> follow the policy below.

The status-bar **shield** shows which tier is active. Your choice is honored exactly:
`native` is used even when Podman is installed, and `podman` is never silently rerouted to
native when it's missing. See
[`preferred_backend`](./settings-reference.md#sandbox).

If the required prerequisites aren't available, `paddleboard_sandbox.on_missing_runtime`
decides what happens:

| Value | Behavior |
|---|---|
| `block` (default) | refuse to run and open the install modal; the agent gets a clear error rather than a hang |
| `fall_back_to_host` | run the command on the host, unsandboxed |
| `warn_once` | **proceed sandboxed**, logging a one-shot warning with install guidance |

Note that `warn_once` does **not** drop you to the host — it's the quiet variant of `block`,
not of `fall_back_to_host`. `fall_back_to_host` is the only setting that runs agent code
unsandboxed.

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
