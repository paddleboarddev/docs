# Getting Started

PaddleBoard is alpha and currently **builds from source** — there are no released binaries yet.

## Prerequisites

- **Rust** (via [rustup](https://rustup.rs)).
- **macOS:** Xcode + Xcode command-line tools, and `cmake`.
- **Linux:** run `script/linux` to install system dependencies.
- **Windows:** Visual Studio with the C++ toolchain, the Windows SDK, and `cmake`.

## Build and run

```sh
git clone https://github.com/paddleboarddev/paddleboard.git
cd paddleboard
cargo run -p paddleboard
```

The first build takes a while (it compiles the whole workspace). Subsequent runs are fast.

## First steps

1. **Add a model.** PaddleBoard ships with no model configured — open the agent panel and add
   your own API key for a provider. See [Configuring LLM Providers](./llm-providers.md).
2. **Open a project.** Common languages work immediately; others are one click away in
   [Language Support](./languages.md).
3. (Optional) **Save your git credentials** so pushes don't prompt — see [Git Login](./git-login.md).

## A note on macOS Gatekeeper

PaddleBoard is not yet code-signed or notarized with an Apple Developer ID. Building from
source yourself is unaffected. Once distributed binaries exist, macOS will warn that "Apple
cannot check it for malicious software" until notarization is in place — you'd allow it via
**System Settings → Privacy & Security**.
