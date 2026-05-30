# PaddleBoard documentation

Task-oriented "how-to" docs for [PaddleBoard](https://github.com/paddleboarddev/paddleboard),
built with [mdBook](https://rust-lang.github.io/mdBook/) and deployed to GitHub Pages via
`.github/workflows/mdbook.yml`.

## Local development

```sh
cargo install mdbook   # or: brew install mdbook
mdbook serve           # live-reload at http://localhost:3000
```

Content lives in `src/`; the table of contents is `src/SUMMARY.md`.

## Deploy

Pushing to `main` builds the book and publishes it to GitHub Pages.
