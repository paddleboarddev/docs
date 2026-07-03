# Personas: Tell Your Agent Who to Be

Skills define what your agent can *do*. A **persona** defines who it should
*be* — its voice, values, and what it pushes back on. Describe the teammate you
want (a Senior Developer, an SRE, a QA Engineer) and PaddleBoard holds the
agent to that identity for the whole thread, with any LLM provider.

Personas apply to the native **PaddleBoard Agent**. External agents (Claude
Code, Codex, …) own their system prompts, so persona selection doesn't apply
there — on those threads a muted persona icon links back to the AI Dock's
Personas tab.

## Quick start

Drop a `PERSONA.md` at your project root:

```markdown
You are a Site Reliability Engineer. You think in blast radius and rollback
plans. For any deploy or migration, ask for the rollback plan before
discussing the rollout. Flag missing timeouts, retries without backoff, and
single points of failure whenever they appear.
```

That's it. Every new PaddleBoard Agent thread in that project adopts the
persona automatically — plain prose works, no frontmatter required.

## The persona library

For more than one persona, keep a library of `<name>.persona.md` files:

- `{project}/.claude/personas/` — project-scoped
- `~/.claude/personas/` — available in every project

Library files use flat YAML frontmatter plus a markdown body:

```markdown
---
name: qa-engineer
description: A meticulous QA engineer who hunts edge cases.
type: role
voice: terse, skeptical, asks for repro steps before agreeing
---

# Identity
...

# Behavioral rules
- Before proposing a fix, ask for exact reproduction steps.
- When asked to "ship it," name the tests that would have to pass first.
```

`name` and `description` are required for library personas — the description
is what the agent (and the picker) uses to choose between them. A
project-library persona shadows a user-library persona with the same name.

**Writing tip:** short, imperative behavioral rules ("ask for repro steps
before proposing a fix") hold up far better over a long conversation than
paragraphs of backstory.

## Switching personas

Three ways, in increasing order of laziness:

1. **The picker.** A persona pill sits in the agent panel's message toolbar,
   next to the profile selector. Click it to switch or clear the persona for
   the current thread. The persona is saved with the thread and survives
   restarts.
2. **Just ask.** The agent sees your persona catalog in its system prompt and
   carries an `adopt_persona` tool — say *"be my QA tester"* mid-conversation
   and it switches itself, applying the new identity from its very next
   response. *"Drop the persona"* returns it to the default voice. The agent
   only changes personas when you ask.
3. **Starter roles.** The AI Dock's **Personas** tab ships three ready-made
   roles — Senior Developer, Site Reliability Engineer, QA Engineer — with
   one-click **Add to project / Add to user** install, and lists every persona
   discovered in your project.

## Composing personas with `extends:`

A persona can inherit another's rules:

```markdown
---
name: strict-qa
description: QA engineer on the house base.
extends: house-base
---
```

The parent's body is included in the overlay ahead of the child's (chains are
followed; cycles are cut; a missing parent is skipped with a warning), so the
child's own rules always read last and win. Use it to keep a shared
"house style" base that every role builds on.

## Personas for sub-agents

Delegated work can wear its own identity: the agent can pass `persona` to its
`spawn_agent` tool — or you can ask it to ("have a QA-persona sub-agent review
this"). The sub-agent holds that identity for its whole session while your
main thread keeps its own voice. A review pass that genuinely thinks like a
reviewer beats an implementer grading their own work.

## How it works

The persona is injected into the agent's system prompt as an identity overlay,
snapshotted when selected so a thread's identity stays stable even if the file
changes on disk. Because it rides the system prompt, it works identically with
every configured LLM provider and stays byte-stable per thread, which keeps
provider prompt caches warm. A persona shapes *how* the agent responds — tone,
priorities, what it pushes back on — never its honesty or capabilities, and it
composes with (rather than replaces) your project rules.

## Settings

```json
{
  "paddleboard_personas": {
    // Default true. The feature is inert until a persona file exists.
    "enabled": true
  }
}
```
