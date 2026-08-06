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

## Troubleshooting

PaddleBoard links here from two Windows failures.

### Could not start ReadDirectoryChangesW

PaddleBoard watches project files with `ReadDirectoryChangesW`, which **network
filesystems and WSL paths do not reliably support**. Opening a project from a UNC share, a
mapped network drive, or a `\\wsl$\...` path can fail at startup with
`ReadDirectoryChangesW initialization failed`.

Open the project from a **local NTFS path** instead. If the files genuinely live in WSL,
run the **Linux build inside WSL** rather than reaching into WSL from Windows — see below.

### Software-emulated graphics

PaddleBoard renders through DirectX on Windows. Without a usable GPU driver the system
falls back to software emulation, which is too slow to edit in, so PaddleBoard warns
instead of letting it look like an unexplained stutter.

Install your GPU vendor's driver. To proceed on software rendering anyway — a VM, or a
remote session — set:

```powershell
$env:PADDLEBOARD_ALLOW_EMULATED_GPU=1
```

## WSL

If you want a supported PaddleBoard on Windows hardware today, **WSL2 is the shortest path**:
install the Linux build inside WSL and run it there, where releases, automatic updates, and
local models all work normally. See [Linux](./linux.md).

## Roadmap

A Windows release is wanted but not scheduled. The blockers are the ordinary ones for a
small project — CI capacity to build and test it, and somewhere to get it signed so it
doesn't trip SmartScreen. If Windows support matters to you, say so on the issue tracker;
demand is what moves it up the list.
