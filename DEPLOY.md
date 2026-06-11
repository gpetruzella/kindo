# Deployment workflow - kindo.dev

This site is a static Firebase Hosting site. The folder `public/` is the live site.
Deployment is automated through GitHub: **any push to the `main` branch auto-deploys
to https://kindo.dev** via the GitHub Action in `.github/workflows/firebase-deploy.yml`.

## How a change goes live

1. Someone edits files in `public/` and commits the change to `main` on GitHub.
2. The GitHub Action runs Firebase's official deploy action.
3. Firebase Hosting publishes the new `public/` to the live channel (kindo.dev).

No credentials live on your computer or in Claude's sandbox for this path. The only
deploy credential (a Firebase service-account key) is stored as an encrypted **GitHub
repository secret** (`FIREBASE_SERVICE_ACCOUNT_KINDO_GCP`) and is used only inside
GitHub's runners.

## Claude's role (including via Dispatch)

Claude does **not** run git inside this Windows folder - git's lock/atomic-write
machinery corrupts over the folder's network mount, and recently-written files can
read back stale or null-padded. Instead, for any change Claude:

1. Edits the file(s) here so you can see them in this folder.
2. Clones the GitHub repo into its own sandbox, writes the changed files there with
   verified-clean content, commits, and pushes to `main` using a repo-scoped token.
3. The GitHub Action deploys automatically.

This works asynchronously (Dispatch), because no step needs you present.

## Your manual fallback (unchanged)

You can still deploy by hand at any time:

```
firebase-tools-instant-win.exe   # if not already on PATH as `firebase`
firebase deploy --only hosting
```

This pushes the current local `public/` straight to Firebase, bypassing GitHub.
