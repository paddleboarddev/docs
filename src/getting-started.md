# Getting Started

PaddleBoard is in active development, but you don't have to build it — signed releases are
published for macOS and Linux.

## Install a release (recommended)

Grab the latest build from
[GitHub Releases](https://github.com/paddleboarddev/paddleboard/releases):

| Platform | Asset |
|---|---|
| macOS (Apple Silicon) | `PaddleBoard-aarch64.dmg` — code-signed and notarized |
| Linux (x86_64) | `paddleboard-linux-x86_64.tar.gz` |
| Windows | Build from source for now — see below |

The macOS build is signed and notarized with an Apple Developer ID, so it opens without
Gatekeeper warnings.

## Build from source

Needed on Windows, and useful if you want to hack on PaddleBoard itself.

### Prerequisites

- **Rust** (via [rustup](https://rustup.rs)).
- **macOS:** Xcode + Xcode command-line tools, and `cmake`.
- **Linux:** run `script/linux` to install system dependencies.
- **Windows:** Visual Studio with the C++ toolchain, the Windows SDK, and `cmake`.

### Build and run

```sh
git clone https://github.com/paddleboarddev/paddleboard.git
cd paddleboard
cargo run -p paddleboard
```

The first build takes a while (it compiles the whole workspace). Subsequent runs are fast.

## First launch

The first time you open PaddleBoard you land on an onboarding page. Three things there matter:

- **Theme.** PaddleBoard's own **PaddleBoard Dark / Light** is the default and is listed first —
  a deep navy palette matching [paddleboard.dev](https://paddleboard.dev). Every bundled Zed theme
  (One, Ayu, Gruvbox) is still available if you prefer one.
- **AI Providers.** Connect a model without leaving onboarding. **Local Models** is the zero-key
  path — pick a model and PaddleBoard downloads and runs it on your machine. Or expand any
  provider (OpenAI, Anthropic, Google, …), paste an API key, and **Save**; keys go to your OS
  keychain. See [Configuring LLM Providers](./llm-providers.md) for the full set of options.
- **Finish Setup** takes you to the Welcome page, which offers **Take the Tour** — a short,
  rendered six-stop walkthrough. Reopen it any time with `Cmd-Shift-P` →
  `workspace: Open Paddle Board Tour`.

## First steps

1. **Make sure a model is connected.** If you skipped that during onboarding, use the LLM
   provider panel or the settings LLM page — see
   [Configuring LLM Providers](./llm-providers.md).
2. **Open a project.** Common languages work immediately; others are one click away in
   [Language Support](./languages.md).
3. (Optional) **Save your git credentials** so pushes don't prompt — see [Git Login](./git-login.md).
