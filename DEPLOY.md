# Deployment workflow — kindo.dev

This site is a static Firebase Hosting site. The folder `public/` is the live site.
Deployment is automated through GitHub: **any push to the `main` branch auto-deploys
to https://kindo.dev** via the GitHub Action in `.github/workflows/firebase-deploy.yml`.

## How a change goes live

1. Someone edits files in `public/` and commits the change to `main` on GitHub.
2. The GitHub Action runs Firebase's official deploy action.
3. Firebase Hosting publishes the new `public/` to the live channel (kindo.dev).

No credentials live on your computer or in Claude's sandbox for this path. The only
deploy credential (a Firebase service-account key) is stored as an encrypted **GitHub
repository secret** and is used only inside GitHub's runners.

## Claude's role (including via Dispatch)

Claude does **not** run git inside this Windows folder — git's lock/atomic-write
machinery corrupts over the folder's network mount. Instead, for any change Claude:

1. Edits the file(s) here so you can see them in this folder.
2. Clones the GitHub repo into its own sandbox, copies the changed files in,
   commits, and pushes to `main` using a repo-scoped access token.
3. The GitHub Action deploys automatically.

This works asynchronously (Dispatch), because no step needs you present.

## Your manual fallback (unchanged)

You can still deploy by hand at any time:

```
firebase-tools-instant-win.exe   # if not already on PATH as `firebase`
firebase deploy --only hosting
```

This pushes the current local `public/` straight to Firebase, bypassing GitHub.

---

## One-time setup checklist

Done already (by Claude, in this folder):
- [x] `.gitignore` — excludes the 213 MB CLI binary, secrets, and build cruft
- [x] `firebase.json` — hosting config (serves `public/`)
- [x] `.firebaserc` — default Firebase project
- [x] `.github/workflows/firebase-deploy.yml` — auto-deploy on push to main

Still to do (needs your accounts — see chat for guided steps):
- [ ] Confirm the Firebase **project ID** (fills the `__PROJECT_ID__` placeholders)
- [ ] Create an empty **GitHub repository** for this site
- [ ] Connect Firebase's deploy action so it can authenticate:
      run `firebase init hosting:github` once with the CLI — it creates the
      service-account secret in the GitHub repo automatically
- [ ] Provide Claude a repo-scoped **access token** so it can push (for Dispatch)
- [ ] Delete the stray, broken `.git` folder Claude could not remove from here
