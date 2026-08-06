# Linux

PaddleBoard ships a Linux build as a `.tar.gz`. This page covers what's in it, what the
system needs, and the two places Linux behaves differently from macOS.

## Install

Download `paddleboard-linux-x86_64.tar.gz` from
[Releases](https://github.com/paddleboarddev/paddleboard/releases), unpack it, and run the
bundled installer:

```sh
tar -xzf paddleboard-linux-x86_64.tar.gz
cd paddleboard.app
./install.sh
```

`install.sh` puts the binary on your `PATH` and registers the desktop entry and icons, so
PaddleBoard appears in your application launcher. To install by hand instead, the archive
is an ordinary prefix tree:

| Path | Contents |
|---|---|
| `bin/` | the `paddleboard` binary and the `cli` helper |
| `lib/` | bundled shared libraries the binary needs |
| `share/applications/` | the `.desktop` entry |
| `share/icons/hicolor/{512x512,1024x1024}/apps/` | application icons |
| `licenses.md` | third-party licenses |

## x86_64 only

The published build is **x86_64**. There is no aarch64 (ARM) Linux release.

This matters beyond just "no download": the updater maps your platform to a release asset,
and **aarch64 Linux has no mapping**, so an ARM machine running a self-built PaddleBoard is
never offered an update. It won't error — it simply never finds anything. On ARM, build
from source and pull updates by rebuilding.

## glibc requirement

The release is built on **Ubuntu 22.04**, which sets the floor at **glibc 2.35**. Anything
older — Ubuntu 20.04, Debian 11, RHEL 8 — won't run the published binary and will fail at
load time with a message about `GLIBC_2.35` not being found.

On an older distribution, build from source against your own glibc.

> This floor is a property of the CI image, not a deliberate policy. It is pinned to
> `ubuntu-22.04` precisely so it can't drift upward without someone choosing it — a newer
> image would silently raise the minimum distro that can run PaddleBoard.

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

## Remote development

The remote server is built as a **statically linked musl binary**, so it runs on hosts
whose glibc is older than the one above.

Updating a remote server over SSH uses `rsync`. If it's missing, PaddleBoard tells you and
names the install command for your distribution — it reads `/etc/os-release` to get the
package manager right rather than guessing.
