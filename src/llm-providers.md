# Configuring LLM Providers

PaddleBoard is **bring-your-own-key**: you connect your own model accounts. There is no
hosted PaddleBoard plan, and nothing is gated behind a PaddleBoard sign-in.

## Add a provider

1. Open the **agent panel**.
2. Open **agent settings → Configure Providers** (or the command palette → provider config).
3. Pick a provider, paste your API key, and save. Keys are stored in your **OS keychain** (never in settings or plaintext).

Most providers also read a standard environment variable (e.g. `ANTHROPIC_API_KEY`,
`OPENAI_API_KEY`) as a fallback.

## Supported providers

Includes Anthropic, OpenAI (and ChatGPT subscription auth), Google AI (Gemini), **Google
Vertex AI**, Amazon Bedrock, DeepSeek, Mistral, OpenRouter, Vercel AI Gateway, and local
runners (Ollama, LM Studio).

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
