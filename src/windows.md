# Windows

**There is no Windows release.** PaddleBoard publishes signed builds for macOS and Linux
only. On Windows you build from source, and you should know what you're taking on before
you do.

## The honest status

Windows code is inherited from Zed and is still present — PaddleBoard hasn't removed it,
and `script/bundle-windows.ps1` exists. But:

| | Status |
|---|---|
| Published release asset | **None** |
| Built in CI | **No** — the compile gate covers Linux and macOS only |
| Tested in CI | **No** |
| Automatic updates | **Not available** |
| Managed Local Models | **Not available** |

Because nothing in CI compiles for Windows, Windows-only breakage can land on `main` and
stay there without any check going red. If you build on Windows and something is broken,
that is genuinely useful information — please
[open an issue](https://github.com/paddleboarddev/paddleboard/issues).

## Build from source

Prerequisites:

- **Rust**, via [rustup](https://rustup.rs)
- **Visual Studio** with the C++ toolchain
- the **Windows SDK**
- **cmake**

Then:

```powershell
cargo run --release
```

`script/bundle-windows.ps1` produces a distributable build. It is inherited from Zed and is
not exercised by PaddleBoard's CI.

## What won't work

Two PaddleBoard features are unavailable on Windows, and both fail as *absence* rather than
as an error — the surfaces simply won't offer you anything.

### Automatic updates

The updater maps your platform to a release asset. Windows has no mapping, so no update is
ever offered. "Check for Updates" cannot find a build for you. Update by pulling and
rebuilding.

### Managed Local Models

The bundled llama.cpp runtime ships for macOS (Apple silicon) and Linux (x86_64 and
aarch64) only. On Windows the Local Models section has nothing to offer, and PaddleBoard
reports the platform as unsupported rather than downloading a runtime that can't run.

Everything else in the AI stack is platform-independent — bring your own API keys, or point
PaddleBoard at any OpenAI-compatible endpoint, including one you run yourself on the same
machine.

## WSL

If you want a supported PaddleBoard on Windows hardware today, **WSL2 is the shortest path**:
install the Linux build inside WSL and run it there, where releases, automatic updates, and
local models all work normally. See [Linux](./linux.md).

## Roadmap

A Windows release is wanted but not scheduled. The blockers are the ordinary ones for a
small project — CI capacity to build and test it, and somewhere to get it signed so it
doesn't trip SmartScreen. If Windows support matters to you, say so on the issue tracker;
demand is what moves it up the list.
