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

## Which wins

Your personal `AGENTS.md` comes first, and the project's rules come after it. The
agent is told that **project rules may override personal ones**, so a project
that needs `npm` can say so even when your personal file prefers `pnpm`.

## How this differs from personas and skills

| | Answers | Where |
| --- | --- | --- |
| **Instructions** (this page) | What to do and how: conventions, tools, limits | Personal `AGENTS.md`, project rules file |
| **[Personas](./personas.md)** | Who the agent is: voice, values, what it pushes back on | `PERSONA.md`, `*.persona.md` |
| **Skills** | Extra abilities the agent can call on | Skills folders (agent panel menu → **Manage Skills…**) |

All three can be used together. A persona doesn't replace your instructions, and
your instructions don't need to describe a personality.
