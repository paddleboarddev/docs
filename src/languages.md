# Language Support

PaddleBoard keeps the default install lean with two tiers.

## Ready to use

Common languages — Rust, TypeScript/JavaScript, Python, Go, JSON, YAML, HTML/CSS, and more —
are enabled by default. Open a matching file and the language server attaches automatically,
downloading on first use.

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
