# Manifest: Your Git State at a Glance

The **Manifest** panel is a dockable tree that lays out the project's entire git world in
one place — the ship's manifest. Open it with the tree icon in the dock, or run
**`manifest: Toggle Focus`** from the command palette.

Everything in the panel reads from state PaddleBoard already tracks in the background, so
it renders instantly and never runs git commands on the UI thread.

## The five sections

Each section is a collapsible header with a count; click the header (or its disclosure
arrow) to fold it away.

### Repositories

Every repository in the workspace — multi-repo projects and worktree-backed workspaces
included — each flying a sailboat. The **active** repository is highlighted with a check;
click any other repo to make it active. The Branches, Commits, and Stashes sections below
always describe the active repository, so this row doubles as the panel's scope switch.

### Branches

The active repo's local and remote branches, sorted current-branch-first, then local
before remote, then by recency. Branches with an upstream show **ahead/behind counts**
(`↑2 ↓1`) so you can see what needs a push or pull without running anything.

### Commits

The current branch's recent history — up to 250 commits. Subjects stream in as commit
metadata loads (rows briefly show *Loading…* on first open). **Click any commit to open
its full diff** in a commit view tab.

For history beyond the overview — the full graph, searching, other branches — use the
**Git Graph** (the Git panel's History tab); Manifest deliberately stays a summary.

### Stashes

Every `stash@{n}` with its message. **Click a stash to open it in the stash view**, which
shows its diff and offers **Apply**, **Pop**, and **Drop**.

### Contributors

Everyone who has committed to the repository, with their commit count, busiest first.
Computed locally with `git shortlog` when the panel loads and refreshed when the branch
head changes.

> **Note:** in a collaboration session on a remote project, the Contributors section is
> empty — the query currently runs only against local repositories.

## Tips

- The panel docks left by default; drag it (or use the dock context menu) to move it right.
- Repo switching from the Repositories section is the same operation as the Git panel's
  repository selector — the two stay in sync.
