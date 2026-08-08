# Settings Reference

PaddleBoard-specific settings, on top of all of Zed's. Add them to your `settings.json`
(`Cmd-,`, or `Cmd-Shift-P` → `zed: open settings file`).

Every value shown below is the default, so a block you paste unchanged does nothing. Only
the settings PaddleBoard adds are listed here — for the inherited ones, Zed's documentation
still applies.

## Search

```json
{
  "search": { "search_on_type": true }
}
```

Project search runs as you type, debounced, instead of waiting for Enter. Set `false` for
the classic behavior. See [Search & Status Bar Extras](./editor-extras.md).

## Updates

```json
{
  "paddleboard_auto_update": { "include_prereleases": false }
}
```

Whether in-app updates may install prerelease builds. PaddleBoard's pipeline publishes every
release as a prerelease and promotes it afterwards, so leaving this off follows promoted
releases only. Turn it on to ride beta builds as they're cut.

## Chrome visibility

```json
{
  "paddleboard_ui": {
    "browser_button": true,
    "llm_picker_button": true,
    "orchestration_button": true,
    "manifest_button": true,
    "sandbox_status": true,
    "mcp_status": true,
    "usage_status": true,
    "set_sail_status": true,
    "placid_status": true,
    "update_status": true
  }
}
```

Every piece of chrome PaddleBoard adds can be hidden. The first four are dock panel buttons;
the rest are status bar items. Anything you hide stays reachable from the command palette —
these settings control visibility, not the feature.

`update_status` is worth knowing about: it only appears while an update is downloading or
installing, and then as a **Restart to update** button until you restart.

## Personas

```json
{
  "paddleboard_personas": { "enabled": true }
}
```

The [persona system](./personas.md). On by default, and inert until a `PERSONA.md` exists,
so it costs nothing until you use it.

## Semantic search (local RAG)

```json
{
  "paddleboard_rag": {
    "enabled": false,
    "store_backend": "local",
    "store_url_env": null,
    "store_table_prefix": null,
    "store_ssl": true
  }
}
```

When `enabled`, agents get a `semantic_search` tool that indexes the current project on
demand with the built-in local embedding model (EmbeddingGemma) and answers
natural-language queries entirely on-device.

`store_backend` selects where the vectors live: `"local"` for the built-in on-device sqlite
store, or `"pgvector"` for a bring-your-own Postgres. The pgvector tier sends your vectors
and chunk text to your own database — embeddings are still computed on-device.

`store_url_env` is the **name** of the environment variable holding the libpq connection
string, not the connection string itself. PaddleBoard reads it from the environment at run
time so credentials never land in settings. `store_table_prefix` lets several projects share
one database. `store_ssl` should stay `true` unless you're on a trusted local link such as
the Cloud SQL Auth Proxy.

## Usage tracking

```json
{
  "paddleboard_usage": {
    "enabled": true,
    "granularity": "daily",
    "directory": null,
    "auto_commit": false
  }
}
```

Records per-provider, per-model token counts to a local flatfile so you can see how your
usage is distributed over time. **All of it stays on your machine** — this is not telemetry
and nothing is ever reported anywhere.

`granularity` is `"daily"` (one rolled-up total per day, per provider, per model) or
`"session"` (additionally broken down by agent session). `directory` defaults to
PaddleBoard's data directory and supports a leading `~`; point it inside a git repository of
your own and set `auto_commit` to have PaddleBoard `git add` + `git commit` after each flush.

## Sandbox

```json
{
  "paddleboard_sandbox": {
    "on_missing_runtime": "block",
    "prereq_check_enabled": true
  }
}
```

`on_missing_runtime` controls what happens when a sandboxed tool tries to launch but the
host prerequisites aren't satisfied:

| Value | Behavior |
|---|---|
| `block` (default) | refuse to launch and surface the install modal; the agent gets a clear error rather than a hang |
| `fall_back_to_host` | run the command on the host, unsandboxed |
| `warn_once` | proceed sandboxed, with a one-shot notification carrying install guidance |

`prereq_check_enabled` turns off host probing entirely; the gate then always allows tools to
proceed.

There's also `preferred_backend`, normally set from the shield in the status bar rather than
by hand:

```json
{
  "paddleboard_sandbox": { "preferred_backend": "native" }
}
```

`"native"` is the zero-install tier — Apple `container` on macOS 26+, otherwise the bundled
libkrun microVM, or libkrun over KVM on Linux. `"podman"` is the Podman + gVisor tier. Your
choice is honored exactly: `native` is used even when Podman is installed, and `podman` is
never silently rerouted to native when it's missing. Left unset, it defaults to `native` on
macOS and `podman` on Linux and Windows. See [Sandboxed Execution & MCP](./sandbox.md).

## Scion

```json
{
  "paddleboard_scion": { "enabled": false }
}
```

Enables the [Scion](./scion.md) integration. Installing the `scion` CLI alone does not
activate it — this toggle does. When on (and `scion` is on your `PATH`), PaddleBoard polls
the local Scion daemon, shows the Scion section in the orchestration panel, and exposes the
`spawn_scion_agent` tool to agents.

## OpenTelemetry (Scion tracing)

```json
{
  "paddleboard_otel": {
    "enabled": false,
    "endpoint": "http://localhost:4317",
    "protocol": "grpc",
    "service_name": "paddleboard"
  }
}
```

Exports Scion agent-lifecycle traces over OTLP to a collector (Jaeger, Tempo, etc.).
`protocol` is `"grpc"` (port 4317) or `"http"` (port 4318).

Two environment variables override this: `PADDLEBOARD_OTEL_ENABLED=1` turns it on, and
`OTEL_EXPORTER_OTLP_ENDPOINT` replaces `endpoint`.

## Vertex AI

Configured under the Vertex provider settings — `project_id`, optional `location` (default
`global`), and optionally `credentials_path` (service-account JSON) or an Express key. See
[Configuring LLM Providers](./llm-providers.md).

## Telemetry

There is nothing to configure: telemetry is **hard-disabled** in PaddleBoard. Events are
dropped at the source and never reach the network. Note that [usage
tracking](#usage-tracking) is a different thing — it's local-only and never transmitted.
