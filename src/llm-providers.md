# Configuring LLM Providers

PaddleBoard is **bring-your-own-key**: you connect your own model accounts. There is no
hosted PaddleBoard plan, and nothing is gated behind a PaddleBoard sign-in.

## Add a provider

**During onboarding (easiest).** First launch includes an **AI Providers** section: expand a
provider, paste a key, and **Save** — or take the zero-key path with **Local Models** (below).

**Any time after that:**

1. Open the **agent panel**.
2. Open **agent settings → Configure Providers** (or the command palette → provider config).
3. Pick a provider, paste your API key, and save. Keys are stored in your **OS keychain** (never in settings or plaintext).

Most providers also read a standard environment variable (e.g. `ANTHROPIC_API_KEY`,
`OPENAI_API_KEY`) as a fallback.

## Switching providers

The **LLM provider panel** is a fast switcher — dock it wherever is convenient. It lists your
providers with an authenticated check, marks the current default, and offers **Use as Default**
to promote the selected one. It is deliberately just a switcher: deep configuration lives on
the settings LLM page, and browsing or installing lives in the [AI Dock](./ai-dock.md).

## Local Models — no API key needed

PaddleBoard can run a model **on your machine** with nothing to install: it ships a signed
`llama.cpp` server and manages the process for you. Open **Local Models** in the AI provider
settings (or during onboarding), turn on *Run locally, managed by PaddleBoard*, and pick a
model — Gemma 3 4B is a good default. It downloads once, binds to `127.0.0.1` only, and is
Metal-accelerated on Apple silicon. Afterwards it appears in the model picker like any other
provider. Power users can still point it at their own `llama.cpp` server.

## Supported providers

| | |
|---|---|
| **Hosted APIs** | Anthropic, OpenAI (and ChatGPT subscription auth), Google AI (Gemini), Google Vertex AI, Amazon Bedrock, DeepSeek, Mistral, xAI, OpenRouter, Vercel AI Gateway |
| **Subscription-backed** | GitHub Copilot Chat, OpenCode |
| **Local** | PaddleBoard's managed **Local Models**, Ollama, LM Studio |
| **Anything else** | the generic **OpenAI-compatible** provider — point it at any endpoint speaking the OpenAI API, including a LiteLLM proxy or a self-hosted gateway |

### Zed's hosted models

PaddleBoard is a fork of Zed, and Zed's hosted models are available here as one provider
among the others. Signing in is **entirely optional** — it is never a default, nothing is
gated behind it, and BYO keys and local models are first-class alternatives. It's offered
because some users want it and because using it sends business to the upstream project this
fork is built on.

If you do sign in and exhaust Zed's free prompts, the upgrade prompt you see is Zed's, and
the subscription is Zed's. PaddleBoard sells nothing.

## Google Vertex AI (Gemini Enterprise)

Vertex is a PaddleBoard addition for teams whose Gemini access runs through Google Cloud.
It supports three auth modes, in precedence order:

1. **Service-account key** — set `credentials_path` to a service-account JSON file.
2. **Express API key** — paste an AI Studio / Express key.
3. **gcloud (recommended, no stored secret)** — run `gcloud auth login` once; PaddleBoard
   borrows short-lived Application Default Credentials. Just set `project_id` (and optionally
   `location`, default `global`) and it works.

Configure it from the provider settings (project id, location, optional credentials path).
Newer models (e.g. Gemini 3) live on the `global` location, which is the default.

## Privacy

Telemetry is hard-disabled in PaddleBoard — your prompts and usage never leave your machine
except for the calls you make directly to your chosen provider.
