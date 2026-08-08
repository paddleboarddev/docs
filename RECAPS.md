# Recaps

Running log of completed work on the PaddleBoard docs, newest first. Each `## YYYY-MM-DD`
groups a day; each `### ` is one coherent unit of work.

## 2026-08-08

### The Linux page, corrected against the actual v0.2.6 tarball

- **Everything here came from unpacking the shipped `paddleboard-linux-x86_64.tar.gz`** and reading it with `llvm-objdump`, rather than from the build scripts. The page was originally written from `script/bundle-linux`, which is why all three errors below are things the source predicts correctly but the artifact contradicts.
- ⚠️ **The Install section told users to run `./install.sh`, which is not in the archive.** `bundle-linux` never copies it — the tarball is `bin/`, `lib/`, `libexec/`, `share/`, `licenses.md` and nothing else. The first command a Linux beta tester runs would have failed with "No such file or directory". Replaced with run-in-place, a `~/.local` install that mirrors what `script/install.sh` actually does, and the desktop-entry steps with their absolute-path rewrites.
- ⚠️ **`script/install.sh` is not an installer for the published archive** — it installs a tarball you built yourself via `script/install-linux` (`PADDLEBOARD_BUNDLE_PATH`) and exits with an error without one. Since PaddleBoard runs no hosted release server, there is also no `curl | sh` path. Both stated explicitly, because the name invites the wrong assumption.
- **The glibc floor is 2.34, not 2.35.** The old text reasoned from the CI image (`ubuntu-22.04` → 2.35); the binary's highest version reference is `GLIBC_2.34`, and so is every bundled library's. The practical difference is the **RHEL / Rocky / AlmaLinux 9 family, which ships exactly 2.34** and was being written off. Now a table, with the CI-pin note reworded to explain why the binary asks for less than the image provides.
- ⚠️ **New section for `libasound.so.2`, the one hard dependency not in the archive.** It's a `DT_NEEDED` entry, so it fails in the dynamic loader before any PaddleBoard code runs — no friendly message, just `error while loading shared libraries`. Desktop installs have it; container and server images often don't. Install lines for apt/dnf/pacman, including the `libasound2t64` rename on Ubuntu 24.04.
- **The archive table was wrong about `bin/` too** — it listed "the `paddleboard` binary and the `cli` helper" as if both lived there. `bin/paddleboard` **is** the `cli` crate; the editor is `libexec/paddleboard-editor`. The table now lists all four `libexec` entries, including the bundled llama.cpp runtime.
- ✅ **Verified the three anchors the app links into are untouched** — `#could-not-start-inotify`, `#i-cant-open-any-files`, `#paddleboard-fails-to-open-windows` all still resolve in the built HTML. Checked rather than assumed, since relinking those was a separate PR's work.
- ⏸️ **Still unverified: that the tarball actually runs.** This is static linkage analysis — it settles the symbol floor and the link graph, not whether the app launches. Docker wasn't available on the machine; an unpack-and-run in an Ubuntu 22.04 container is still the outstanding check before beta.

## 2026-08-06

### Linux and Windows platform pages

- **Added `src/linux.md` and `src/windows.md`** under a new **Platforms** section in `SUMMARY.md`. `getting-started.md` already covered basic install, so these are platform depth rather than a repeat of it.
- **Written from verified behaviour, not assumption.** Every claim was checked in the app repo first: the updater's `asset_name` map, `script/bundle-linux`'s output tree, `paddleboard_llama_manager`'s target gating, `paddleboard_sandbox_prereqs`' libkrun probing, and the release workflow's runner image.
- ⚠️ **The most useful finding is an absence.** `asset_name` maps only `("macos","aarch64")` and `("linux","x86_64")`. Every other platform returns `None`, which means **aarch64 Linux and Windows are never offered an update** — and the failure is silent. No error, nothing found, ever. Both pages state this plainly because it is invisible from inside the app.
- **The Linux glibc floor is documented as a CI artifact, not a policy** — the release builds on `ubuntu-22.04`, which puts the floor at glibc 2.35 and rules out Ubuntu 20.04 / Debian 11 / RHEL 8. Cross-referenced to why that image is pinned: a newer one would silently raise the minimum distro.
- **The Windows page leads with the honest status** — no release asset, not built in CI, not tested in CI, no auto-update, no managed local models. Because nothing compiles for Windows in CI, Windows-only breakage can sit on `main` with every check green. WSL2 is offered as the actually-supported path today.
- ⏸️ **Not done: relinking the app.** 67 `zed.dev/docs` links remain in user-facing code; **8 point at `zed.dev/docs/linux` and 4 at `.../windows`**, which these two pages now replace. That's a change in the app repo and wants its own PR.
- **Context that shaped the scope:** only 8 of those 67 links are `/languages/` links, across 8 languages. Mirroring Zed's page-per-language docs would chase ~12% of them while restating upstream behaviour this fork hasn't changed. Platform and feature pages are the higher-value gap — hence starting here.
