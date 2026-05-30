# Git Login

Save your git host credentials once so HTTPS git operations stop prompting on every
clone/fetch/push.

## Save a login

Run **`git login: Manage`** from the command palette:

1. Pick a provider — **GitHub**, **GitLab**, **BitBucket**, or a custom host.
2. Enter your username and paste a **Personal Access Token** (the modal links straight to the
   provider's token page and lists the scopes to grant).
3. **Save.** The token is written to your **OS keychain** — never to settings or plaintext.

After that, git HTTPS `clone` / `fetch` / `push` authenticate silently. The password prompt
only appears when there's no saved login. **Remove** clears a saved login.

## Environment-variable fallback

If you'd rather not store a token, set one of these and PaddleBoard answers git auth from it:

```sh
export GITHUB_TOKEN=...      # or GITLAB_TOKEN / BITBUCKET_TOKEN
```

## Notes

- PaddleBoard sends the conventional token username per provider unless you set your own:
  `x-access-token` (GitHub), `oauth2` (GitLab), `x-token-auth` (BitBucket).
- **PAT-only** today — no OAuth sign-in yet.
- **HTTPS only.** SSH key passphrases and host-key confirmations are untouched and still
  prompt as usual.
