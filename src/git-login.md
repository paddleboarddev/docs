# Git Login

Save your git host credentials once so HTTPS git operations stop prompting on every
clone/fetch/push.

## The logins list

Run **`git login: Manage`** from the command palette. The modal lists each provider —
**GitHub**, **GitLab**, **BitBucket** — with its live status: *Signed in as `<user>`* or
*Not signed in*. Click a row to select it, or click **Remove** on a row to delete its saved
login. Custom hosts (e.g. a self-hosted GitLab) are managed through the form fields below
the list.

## Sign in with GitHub (browser)

On builds configured with an OAuth client id, selecting GitHub shows a
**Sign in with GitHub (browser)** button:

1. PaddleBoard displays a short code (like `WDJB-MJHT`) and opens `github.com/login/device`.
2. Enter the code and approve access.
3. The token is stored in your OS keychain automatically — you're signed in.

This is the same device flow the `gh` CLI uses; no client secret is involved. If the button
isn't there, the build has no client id configured — use a token instead, which always works.

## Save a token manually

1. Pick a provider from the list (or enter a custom host).
2. Enter your username and paste a **Personal Access Token** (the modal links straight to the
   provider's token page and lists the scopes to grant).
3. **Save.** The token is written to your **OS keychain** — never to settings or plaintext.

After that, git HTTPS `clone` / `fetch` / `push` authenticate silently. The password prompt
only appears when there's no saved login.

## Remember from the prompt

No saved login yet? When git's password prompt appears, tick **"Remember on this device"**
before submitting — the credential is saved to your keychain and the next operation
authenticates silently.

## Beyond git: API requests

A saved GitHub login also authenticates PaddleBoard's GitHub API requests — commit-author
avatars in git blame resolve on private repos, and you skip the unauthenticated rate limit.
GitHub Enterprise hosts use their own saved login. (`GITHUB_TOKEN` still wins when set.)

## Environment-variable fallback

If you'd rather not store a token, set one of these and PaddleBoard answers git auth from it:

```sh
export GITHUB_TOKEN=...      # or GITLAB_TOKEN / BITBUCKET_TOKEN
```

## Notes

- PaddleBoard sends the conventional token username per provider unless you set your own:
  `x-access-token` (GitHub), `oauth2` (GitLab), `x-token-auth` (BitBucket).
- OAuth sign-in is GitHub-only — GitLab and BitBucket don't offer a comparable device flow,
  so they use tokens.
- **HTTPS only.** SSH key passphrases and host-key confirmations are untouched and still
  prompt as usual.
