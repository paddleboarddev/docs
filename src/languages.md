# Language Support

PaddleBoard keeps the default install lean with two tiers.

## Ready to use

Common languages — Rust, TypeScript/JavaScript, Python, Go, JSON, YAML, HTML/CSS, and more —
are enabled by default. Open a matching file and the language server attaches automatically,
downloading on first use.

## Python

**Basedpyright** is the default language server for Python. PyRight and pylsp are disabled
by default — Basedpyright is a superset of PyRight, so running both means duplicate
diagnostics on every line.

To go back to one of the others, set the language server list yourself in `settings.json`:

```json
{
  "languages": {
    "Python": {
      "language_servers": ["pyright", "!basedpyright", "ruff", "..."]
    }
  }
}
```

The `!` prefix disables a server, and `"..."` stands in for the remaining defaults.
Swap `pyright` for `pylsp`, `ty`, or `pyrefly` the same way.

### "Import could not be resolved"

If Basedpyright reports `Import "<package>" could not be resolved`, it is usually right:
the package isn't installed in the interpreter it is analysing against. Basedpyright checks
imports more strictly than pylsp did, so switching to it can surface an environment problem
that was always there.

Create and populate a virtualenv in the project root, then reopen the folder:

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

PaddleBoard discovers `.venv` automatically. To point at a different interpreter, run
**`toolchain: Select Toolchain`** from the command palette.

## Install support (opt-in)

Languages that need an external toolchain are opt-in. Run **`Manage Languages`** from the
command palette to enable them; each row shows its prerequisite up front so it never fails
silently when the runtime is missing.

Six ship a built-in server:

| Language | Server | Prerequisite |
|---|---|---|
| Java | jdtls | JDK 17+ |
| Kotlin | kotlin-language-server | JDK 17+ |
| PHP | intelephense | Node |
| C# | roslyn | .NET |
| C++ | clangd | downloads clangd (C stays on by default; clangd is shared) |
| Swift | SourceKit-LSP | Swift toolchain (Xcode / swift.org) — resolved from `PATH`, not downloaded |

**Ruby** and **Dart** get their servers from extensions, so their row opens the Extensions page.

> Why opt-in? It keeps the default install small and means you choose to pull in a heavier
> toolchain knowingly, rather than hitting a confusing "server reset the connection" crash
> when the runtime isn't present.
