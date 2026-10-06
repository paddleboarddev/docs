# Windows

**Windows ships as an unsigned preview.** Since 0.3.0 every release carries a
`PaddleBoard-x86_64-windows-preview.zip`, built in CI from the same source as the
signed macOS and Linux releases. It is a preview, not a release-grade build, and the
differences are listed here so you know what you are taking on before you unzip it.

## The honest status

| | Status |
|---|---|
| Published release asset | **Unsigned zip** (x86_64), with a SHA-256 beside it |
| Built in CI | **Yes** — the release workflow builds it on every tag |
| Tested in CI | **No** — the compile and test gates still cover Linux and macOS only |
| Code-signed | **No** — SmartScreen warns on first run |
| Installer | **None** — unzip and run |
| Automatic updates | **Not available** — download the next zip |
| Managed Local Models | **Not available** |
| arm64 | **Not yet** — build from source |

The compile gate does not run on Windows, so Windows-only breakage can still land on
`main` between releases. If something is broken in a preview build, that is genuinely
useful information: please
[open an issue](https://github.com/paddleboarddev/paddleboard/issues).

## Download the preview

1. Download `PaddleBoard-x86_64-windows-preview.zip` from the
   [latest release](https://github.com/paddleboarddev/paddleboard/releases/latest).
2. Optionally verify it against the `.zip.sha256` next to it:

   ```powershell
   (Get-FileHash .\PaddleBoard-x86_64-windows-preview.zip -Algorithm SHA256).Hash
   ```

3. Unzip it anywhere and run `PaddleBoard.exe`.
4. **SmartScreen will warn** that the publisher is unknown, because the build is not
   signed. Click *More info*, then *Run anyway*. You only see this once per download.

`bin\paddleboard.exe` is the command-line launcher. Add `bin\` to your `PATH` to use
`paddleboard <path>` from a terminal. `README-PREVIEW.txt` in the zip repeats the limits
on this page.

## Known issues

- **The window cannot be dragged by its title bar**
  ([#87](https://github.com/paddleboarddev/paddleboard/issues/87)). Resize and
  maximise work; moving the window does not. This is the top Windows bug.
- Everything under [What won't work](#what-wont-work) below.

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

`script/bundle-windows-preview.ps1` produces the same zip that CI publishes. The inherited
`script/bundle-windows.ps1` (installer, code signing) is Zed's and is not used by
PaddleBoard.

## What won't work

Two PaddleBoard features are unavailable on Windows, and both fail as *absence* rather than
as an error — the surfaces simply won't offer you anything.

### Automatic updates

The updater's Windows path expects an installer, and the preview is a zip, so no update is
ever offered. "Check for Updates" cannot find a build for you. Update by downloading the
next release's zip.

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

Logs live in `%LOCALAPPDATA%\PaddleBoard\logs`.

## WSL

If you want automatic updates and local models on Windows hardware today, **WSL2 is the
path**: install the Linux build inside WSL and run it there, where releases, automatic
updates, and local models all work normally. See [Linux](./linux.md).

## Roadmap

From preview to release means three things: code signing so SmartScreen stays quiet, an
installer so updates can be automatic, and Windows in the compile gate so breakage is caught
before a tag. None has a date. If Windows support matters to you, say so on the issue
tracker; demand is what moves it up the list.
