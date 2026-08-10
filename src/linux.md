# Linux

PaddleBoard ships a Linux build as a `.tar.gz`. This page covers what's in it, what the
system needs, and the two places Linux behaves differently from macOS.

## Install

Download `paddleboard-linux-x86_64.tar.gz` from
[Releases](https://github.com/paddleboarddev/paddleboard/releases) and unpack it. The
archive contains no installer, and there is no `curl | sh` bootstrap — PaddleBoard runs no
hosted release server, so the tarball on the Releases page is the whole distribution.

To try it without installing anything, run it in place:

```sh
tar -xzf paddleboard-linux-x86_64.tar.gz
./paddleboard.app/bin/paddleboard
```

To install it for your user, unpack into `~/.local/` and link it onto your `PATH`:

```sh
tar -xzf paddleboard-linux-x86_64.tar.gz -C ~/.local/
mkdir -p ~/.local/bin ~/.local/share/applications
ln -sf ~/.local/paddleboard.app/bin/paddleboard ~/.local/bin/paddleboard
```

That's enough to run `paddleboard` from a terminal, provided `~/.local/bin` is on your
`PATH`. To also get PaddleBoard into your application launcher, install the desktop entry
and point it at absolute paths:

```sh
desktop=~/.local/share/applications/dev.paddleboard.PaddleBoard.desktop
cp ~/.local/paddleboard.app/share/applications/dev.paddleboard.PaddleBoard.desktop "$desktop"
sed -i "s|Exec=paddleboard|Exec=$HOME/.local/paddleboard.app/bin/paddleboard|g" "$desktop"
sed -i "s|Icon=paddleboard|Icon=$HOME/.local/paddleboard.app/share/icons/hicolor/512x512/apps/paddleboard.png|g" "$desktop"
```

The archive is an ordinary prefix tree, so it can live anywhere you prefer:

| Path | Contents |
|---|---|
| `bin/paddleboard` | the launcher and command-line interface — this is what you run |
| `libexec/paddleboard-editor` | the editor itself, started by the launcher |
| `libexec/paddleboard-krun-helper` | helper for the microVM sandbox tier |
| `libexec/llama/` | the bundled llama.cpp runtime for managed local models |
| `lib/` | bundled shared libraries the binary needs |
| `share/applications/` | the `.desktop` entry |
| `share/icons/hicolor/{512x512,1024x1024}/apps/` | application icons |
| `licenses.md` | third-party licenses |

> `script/install.sh` in the source repo is **not** an installer for this archive — it
> installs a tarball you built yourself, via `script/install-linux`, and exits with an
> error if you run it without one.

## x86_64 only

The published build is **x86_64**. There is no aarch64 (ARM) Linux release.

This matters beyond just "no download": the updater maps your platform to a release asset,
and **aarch64 Linux has no mapping**, so an ARM machine running a self-built PaddleBoard is
never offered an update. It won't error — it simply never finds anything. On ARM, build
from source and pull updates by rebuilding.

## glibc requirement

The published binary requires **glibc 2.35 or newer** — the same floor as the `ubuntu-22.04`
image the release is built on. The editor asks `libm.so.6` for `GLIBC_2.35`. On anything
older it fails at load time with a message about a `GLIBC_2.3x` version not being found.

| Distribution | glibc | Runs |
|---|---|---|
| Ubuntu 22.04 and newer | 2.35+ | yes — verified |
| Debian 12 (bookworm) and newer | 2.36+ | yes |
| RHEL / Rocky / AlmaLinux 9 | 2.34 | no — verified to fail |
| Ubuntu 20.04 | 2.31 | no — verified to fail |
| Debian 11 (bullseye) | 2.31 | no |
| RHEL 8 | 2.28 | no |

RHEL 9 and its rebuilds miss by a single glibc release. That's close enough to look like it
ought to work, and it doesn't.

On an older distribution, build from source against your own glibc.

> The floor comes from the CI image rather than from a deliberate policy, so it moves only
> when someone changes that image. The release job is pinned to `ubuntu-22.04` precisely so
> it can't drift upward unnoticed — a newer image would silently raise the minimum distro
> that can run PaddleBoard.

> **How this was checked.** The rows marked *verified* were tested by running the shipped
> binary inside that distribution's container image, not by reading the binary's headers.
> Header-reading got this wrong once: a sweep that looked only at the `libc.so.6`
> version-reference block concluded the floor was 2.34, because the `GLIBC_2.35` requirement
> lives in the **`libm.so.6`** block. If you re-derive this number, take the maximum across
> *every* `required from` block — or just run it.

## System libraries

Almost everything the binary links against ships inside the archive — `libstdc++`, `libssl`
and `libcrypto`, `libxcb`, `libxkbcommon`, and the X11 shims are all in `lib/`, and the
binary carries an `RPATH` of `$ORIGIN/../lib`, so it finds them without `LD_LIBRARY_PATH`.

One library is **not** bundled and has to come from your system:

```sh
# Debian / Ubuntu
sudo apt install libasound2        # libasound2t64 on Ubuntu 24.04 and newer
# Fedora / RHEL
sudo dnf install alsa-lib
# Arch
sudo pacman -S alsa-lib
```

`libasound.so.2` is a hard link-time dependency, so when it's missing PaddleBoard fails
before it starts, with a dynamic-loader error rather than a PaddleBoard one:

```
error while loading shared libraries: libasound.so.2: cannot open shared object file
```

Desktop installs nearly always have it already. Minimal, container, and server images
often don't. Verified by running the shipped binary in a stock `ubuntu:22.04` container:
it fails with exactly the error above, and once `libasound2` is installed it loads with no
unresolved libraries at all — this is the only external dependency.

> If you're on both an old distribution *and* a minimal image, you'll see the missing-library
> error first: the dynamic loader reports libraries it cannot find before it checks symbol
> versions in the ones it can. Installing ALSA can therefore turn a `libasound.so.2` error
> into a `GLIBC_2.35` one. That's progress, not a new problem — see
> [glibc requirement](#glibc-requirement).

Vulkan is a runtime requirement too, but it's loaded dynamically and degrades more
gracefully — see [PaddleBoard fails to open windows](#paddleboard-fails-to-open-windows).

## Build from source

Install the build dependencies:

```sh
script/linux
```

PaddleBoard needs a few packages beyond Zed's set, because the embedded browser panel uses
`wry`, which wants the GTK3 + WebKit2GTK stack:

```sh
sudo apt-get install -y libwebkit2gtk-4.1-dev libgtk-3-dev librsvg2-dev \
                        clang libssl-dev pkg-config
```

Then:

```sh
cargo run --release
```

To produce a distributable archive exactly as CI does, run `script/bundle-linux`.

## What differs from macOS

Most of PaddleBoard is identical across platforms. Two things aren't.

### Local Models

Managed local models work on Linux — **both x86_64 and aarch64** — so a self-built ARM
install still gets local inference even though it gets no automatic updates.

Acceleration differs: macOS uses Metal, Linux uses the CPU build unless your setup provides
otherwise. Expect slower tokens-per-second than an equivalent Apple silicon machine.

### The sandbox

The sandbox tier available to you depends on what's installed. On Linux, PaddleBoard looks
for `libkrun` in the usual distribution library directories for the microVM tier, and falls
back to Podman + gVisor, then to the native tier.

Click the **shield** in the status bar to see which tier is active on this machine. Your
choice is honored — PaddleBoard never silently downgrades you to a weaker tier.

## Troubleshooting

These are the failures PaddleBoard links here from directly, so the headings below are the
link targets — keep their wording if you edit them.

### Could not start inotify

PaddleBoard watches project files with inotify, and the kernel caps how many watches and
instances a user may hold. On a large project, or with several editors open, you can hit
that cap and see `inotify_init returned ...` at startup.

Check the current limits and raise them:

```sh
cat /proc/sys/fs/inotify/max_user_instances   # often 128
cat /proc/sys/fs/inotify/max_user_watches     # often 65536 or 524288

sudo sysctl fs.inotify.max_user_instances=1024
sudo sysctl fs.inotify.max_user_watches=1048576
```

To make it stick across reboots, put the same two settings in
`/etc/sysctl.d/60-paddleboard.conf` and run `sudo sysctl --system`.

### I can't open any files

If the file picker never appears, PaddleBoard has no **xdg-desktop-portal** implementation
to talk to. The portal is what a sandboxed or Wayland-native app uses to show a native
file dialog; without a backend installed and running, there is nothing to show.

Install the backend that matches your desktop:

```sh
# GNOME / most desktops
sudo apt install xdg-desktop-portal xdg-desktop-portal-gtk
# KDE
sudo apt install xdg-desktop-portal xdg-desktop-portal-kde
# wlroots compositors (Sway, Hyprland)
sudo apt install xdg-desktop-portal xdg-desktop-portal-wlr
```

Then log out and back in, or restart the service:

```sh
systemctl --user restart xdg-desktop-portal
```

### PaddleBoard fails to open windows

PaddleBoard renders through Vulkan on Linux. If no usable Vulkan driver is present, the
system falls back to software emulation (llvmpipe) — which technically works and is far too
slow to edit in, so PaddleBoard warns rather than letting you discover it as mystery lag.

Install the Vulkan driver for your GPU (`mesa-vulkan-drivers` covers AMD and Intel; NVIDIA
ships its own), then confirm the loader can see it:

```sh
vulkaninfo | head        # from vulkan-tools
```

If you know you're on software rendering and want to proceed anyway — a VM, a remote
session, a screenshot run — set:

```sh
PADDLEBOARD_ALLOW_EMULATED_GPU=1 paddleboard
```

## Remote development

The remote server is built as a **statically linked musl binary**, so it runs on hosts
whose glibc is older than the one above.

Updating a remote server over SSH uses `rsync`. If it's missing, PaddleBoard tells you and
names the install command for your distribution — it reads `/etc/os-release` to get the
package manager right rather than guessing.
