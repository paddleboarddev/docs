# Search & Status Bar Extras

Quality-of-life additions on top of Zed's editor.

## Search as you type

Project search runs automatically as you type — results update a beat (~200ms) after you
pause, no Enter required. This matches VSCode's behavior and one of upstream Zed's
most-requested changes ([zed#9318](https://github.com/zed-industries/zed/issues/9318)).

Prefer the classic press-Enter behavior? Turn it off:

```json
{
  "search": { "search_on_type": false }
}
```

## Agent context gauge

While an agent thread is running, a percentage appears in the status bar showing how much of
the model's **context window** the active thread has used:

- The color follows the agent panel's thresholds — muted, **yellow** as you approach the
  limit, **red** past it.
- **Hover** for the token breakdown: used / total, input vs. output.
- **Click** to jump to the agent panel.
- It hides itself when no thread has token usage.

The gauge is **purely local**: it displays counts the agent thread already tracks on your
machine. Nothing is collected or reported — PaddleBoard's telemetry stays
[hard-disabled](./settings-reference.md#telemetry).

## The rest of the status bar

PaddleBoard adds several other status bar items, each documented with the feature it belongs
to:

| Item | What it does |
|---|---|
| **Shield** | which [sandbox](./sandbox.md) tier is active; click to switch backends |
| **MCP** | [MCP server](./sandbox.md) status, with streaming logs |
| **Sailboat** | [Set Sail](./set-sail.md) deploy status |
| **Placid** | toggles Placid mode |
| **Update** | appears only while an update is downloading or installing, then as **Restart to update** |

Any of them — and the context gauge above — can be hidden with
[`paddleboard_ui`](./settings-reference.md#chrome-visibility). Hiding an item doesn't disable
the feature; everything stays reachable from the command palette.
