# Recaps

Running log of completed work on the PaddleBoard docs, newest first. Each `## YYYY-MM-DD`
groups a day; each `### ` is one coherent unit of work.

## 2026-08-06

### Linux and Windows platform pages

- **Added `src/linux.md` and `src/windows.md`** under a new **Platforms** section in `SUMMARY.md`. `getting-started.md` already covered basic install, so these are platform depth rather than a repeat of it.
- **Written from verified behaviour, not assumption.** Every claim was checked in the app repo first: the updater's `asset_name` map, `script/bundle-linux`'s output tree, `paddleboard_llama_manager`'s target gating, `paddleboard_sandbox_prereqs`' libkrun probing, and the release workflow's runner image.
- ⚠️ **The most useful finding is an absence.** `asset_name` maps only `("macos","aarch64")` and `("linux","x86_64")`. Every other platform returns `None`, which means **aarch64 Linux and Windows are never offered an update** — and the failure is silent. No error, nothing found, ever. Both pages state this plainly because it is invisible from inside the app.
- **The Linux glibc floor is documented as a CI artifact, not a policy** — the release builds on `ubuntu-22.04`, which puts the floor at glibc 2.35 and rules out Ubuntu 20.04 / Debian 11 / RHEL 8. Cross-referenced to why that image is pinned: a newer one would silently raise the minimum distro.
- **The Windows page leads with the honest status** — no release asset, not built in CI, not tested in CI, no auto-update, no managed local models. Because nothing compiles for Windows in CI, Windows-only breakage can sit on `main` with every check green. WSL2 is offered as the actually-supported path today.
- ⏸️ **Not done: relinking the app.** 67 `zed.dev/docs` links remain in user-facing code; **8 point at `zed.dev/docs/linux` and 4 at `.../windows`**, which these two pages now replace. That's a change in the app repo and wants its own PR.
- **Context that shaped the scope:** only 8 of those 67 links are `/languages/` links, across 8 languages. Mirroring Zed's page-per-language docs would chase ~12% of them while restating upstream behaviour this fork hasn't changed. Platform and feature pages are the higher-value gap — hence starting here.
