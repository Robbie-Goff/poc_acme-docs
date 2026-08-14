# Docs POC — Sveltia CMS + MkDocs Material + GitHub Pages

A proof-of-concept documentation site on a personal GitHub account.
Markdown lives in `docs/`, writers edit in the browser at `/admin/`,
every save becomes a commit, and GitHub Actions rebuilds the site.

## Repository layout

```
.github/workflows/deploy.yml    Builds the site and deploys to GitHub Pages on every push
mkdocs.yml                      Site config: name, theme, markdown features
docs/                           All content (this is the whole docs site)
  index.md                      Homepage
  guides/                       "Guides" collection (3 sample pages)
  faq/                          "FAQ" collection
  release-notes/                "Release notes" collection (dated pages)
  images/                       Uploaded media lands here
  admin/index.html              The CMS app (loaded from CDN, ~10 lines)
  admin/config.yml              CMS config: which repo, which collections, what fields
```

## Build the POC (30–45 minutes)

### Step 1 — Create the repo

1. On github.com, create a new **public** repository (public is required for
   free GitHub Pages), e.g. `acme-docs`.
2. Copy everything in this folder into it (keep the `.github` folder!) and push.

### Step 2 — Set the placeholders

Search for `YOURUSERNAME/YOURREPO` and replace with your real values in:

- `mkdocs.yml` (`site_url`, `repo_url`)
- `docs/admin/config.yml` (`backend.repo`)

Commit and push.

### Step 3 — Turn on GitHub Pages

1. Repo → Settings → Pages → Source: **GitHub Actions**.
2. Repo → Actions: you should see the "Build and deploy docs" workflow run
   (re-run it if it ran before you flipped the setting).
3. Your site is now live at `https://YOURUSERNAME.github.io/YOURREPO/`.
   Click around — sidebar, search, dark-mode toggle should all work.

### Step 4 — Log in to the CMS (token method, no infrastructure)

1. On GitHub: Settings → Developer settings → Personal access tokens →
   **Fine-grained tokens** → Generate new token.
   - Repository access: only your docs repo
   - Permissions: **Contents: Read and write**, **Pull requests: Read and write**
2. Open `https://YOURUSERNAME.github.io/YOURREPO/admin/`.
3. Choose the token sign-in option and paste the token.

You should see Guides, FAQ, Release notes, and Top-level pages in the sidebar.

### Step 5 — Run the POC test loop

- [ ] Edit "Getting started", save, publish it from the Workflow tab, and
      confirm the live page updates within ~2 minutes.
- [ ] Check the repo's commit history — your edit should be there as a commit.
- [ ] Create a new Guide; confirm the file appears in `docs/guides/` and the
      page shows up in the site sidebar.
- [ ] Upload an image into a page; confirm it renders on the live site.
- [ ] Save a draft but don't publish; confirm the live site is unchanged and
      a pull request exists in the repo.
- [ ] Edit a file directly on GitHub (pencil icon) and confirm that route
      still works alongside the CMS.
- [ ] Give a non-technical colleague a token and this URL, and watch them
      make an edit without help. This is the test that matters.

## After the POC

- **Nicer login:** replace tokens with "Sign in with GitHub" by deploying
  https://github.com/sveltia/sveltia-cms-auth to Cloudflare Workers (free),
  registering a GitHub OAuth app, and setting `base_url` in
  `docs/admin/config.yml`. ~1 hour.
- **Add writers:** repo → Settings → Collaborators. Each writer needs a free
  GitHub account; the CMS commits under their name.
- **Instant publishing instead of review:** delete the
  `publish_mode: editorial_workflow` line in `docs/admin/config.yml`.
- **New content types:** add a collection block in `docs/admin/config.yml`
  and a matching folder under `docs/`.
- **Company adoption:** transfer the repo to a GitHub organisation
  (Settings → Danger zone → Transfer). Update the repo name in the two config
  files and the OAuth app callback; everything else moves with it.
- **If docs must be private:** GitHub Pages free tier only serves public
  sites. Move hosting to Cloudflare Pages + Cloudflare Access (free up to
  50 users) — the repo, CMS, and workflow barely change.

## Local preview for developers (optional)

```bash
pip install mkdocs-material
mkdocs serve        # http://127.0.0.1:8000, live-reloads on save
```
