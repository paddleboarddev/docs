# The AI Dock

The **AI Dock** is one place to browse, install, and create the AI building blocks
PaddleBoard supports: agents, skills, and MCP servers.

Open it from the command palette → **`ai dock: Open`**.

## Tabs

- **Agents** — register a custom agent server by registry ID (**Add Agent**), or install a
  catalog agent: Claude, Codex, Copilot, Cursor, Antigravity, Zed Agent, and the framework
  agents (Google ADK, LangGraph, CrewAI, AutoGen, A2A). CLI-based agents show a **Set Up**
  button that opens a terminal with the install command rather than running it inside the
  app.
- **Skills** — install bundled slash-command skills with **Add to project** / **Add to
  user**, or **Create Skill** to write a new `.claude/commands/` markdown command. The
  bundled set is `/build`, `/update-tour`, `/clippy`, `/test`, `/check-drift`, `/build-mcp`,
  `/verify`, `/review`, and `/security-review`.
- **Personas** — browse and install the bundled [personas](./personas.md) (Senior Developer,
  Site Reliability Engineer, QA Engineer) into a project or your user directory.
- **MCP** — add and manage Model Context Protocol servers, with live status and streaming
  logs — or **Build an MCP** to have an agent generate one for a service that has none. See
  [Sandboxed Execution & MCP](./sandbox.md).
- **Usage** — the local, per-provider, per-model token history described in
  [Settings Reference](./settings-reference.md#usage-tracking). Nothing here leaves your
  machine.

The catalog is data-driven from `assets/ai_dock/catalog.json` in the repo — additions are
pull requests, not network fetches.
