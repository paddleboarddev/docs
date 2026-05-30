# Settings Reference

PaddleBoard-specific settings (on top of all of Zed's). Add them to your `settings.json`.

## Scion

```json
{
  "paddleboard_scion": { "enabled": false }
}
```

Enables the [Scion](./scion.md) integration (default `false`). Installing the `scion` CLI
alone does not activate it — this toggle does.

## OpenTelemetry (Scion tracing)

```json
{
  "paddleboard_otel": {
    "enabled": false,
    "endpoint": "http://localhost:4317",
    "protocol": "grpc"
  }
}
```

Exports Scion agent-lifecycle traces over OTLP to a collector (Jaeger, Tempo, etc.). Can also
be enabled with `PADDLEBOARD_OTEL_ENABLED=1`.

## Sandbox

```json
{
  "paddleboard_sandbox": { "on_missing_runtime": "block" }
}
```

Controls what happens when the Podman/gVisor [sandbox](./sandbox.md) prerequisites are missing:
`block` (default), `fall_back_to_host`, or `warn_once`.

## Vertex AI

Configured under the Vertex provider settings — `project_id`, optional `location` (default
`global`), and optionally `credentials_path` (service-account JSON) or an Express key. See
[Configuring LLM Providers](./llm-providers.md).

## Telemetry

There is nothing to configure: telemetry is **hard-disabled** in PaddleBoard. Events are
dropped at the source and never reach the network.
