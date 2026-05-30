# Agent Frameworks

PaddleBoard has first-class support for four Python agent frameworks: **Google ADK**,
**LangGraph**, **CrewAI**, and **AutoGen**. When you open a project that declares one of
them (in `pyproject.toml` / `requirements.txt`), PaddleBoard detects it and offers to run it.

## Running a framework

Each framework registers command-palette actions, e.g. `adk: Run Agent`, `crewai: Run Agent`.

- **Dev-server frameworks** (ADK, LangGraph, AutoGen Studio) launch a local server and
  forward its port into the embedded browser. For example `autogen: Run Agent` runs
  `autogenstudio ui --port 8081` and streams startup logs to a tab.
- **One-shot frameworks** (CrewAI) run to completion and stream output to a tab — no port
  forwarding (`crewai: Run Agent` → `crewai run`).

## Scaffolding

Frameworks with a project generator expose a **Scaffold** action (e.g. `adk: Scaffold Agent`,
`crewai: Scaffold Agent`) that opens a small modal and runs the create command in a terminal.

## Installing the CLIs

The frameworks themselves are external tools — install them in your environment (e.g.
`pip install crewai`, `pip install autogenstudio`). PaddleBoard drives them; it doesn't bundle them.
