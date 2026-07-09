# Portfolio site (Jekyll)

This is a Jekyll site. GitHub Pages builds Jekyll sites automatically, no build step, no GitHub
Actions, no git command line needed.

## What to upload

Upload everything in this folder **except** the old `writeups/` folder, that was the first,
hand-coded version and is now replaced by `_posts/`. Skip it.

Everything else, keep the folder structure exactly as it is:

```
_config.yml
_layouts/default.html
_layouts/post.html
_posts/2026-07-09-building-a-segmented-home-lab.md
_posts/2026-07-09-leading-smb1001-to-gold-tier-3.md
index.html
blog/index.html
assets/css/style.css
README.md
```

## First-time setup

1. On GitHub, click **New repository**.
   - Name it `YOUR-USERNAME.github.io` (your actual GitHub username, exactly, this exact name is
     what makes GitHub serve it as a website automatically).
   - Public. Don't initialise with a README.
2. On the repo page, **Add file → Upload files**, drag in everything listed above (keeping
   folders intact), commit straight to `main`.
3. **Settings → Pages** → Source: Deploy from a branch, branch `main`, folder `/ (root)`. Save.
4. Wait a minute or two. Live at `https://YOUR-USERNAME.github.io`.

## Before you publish, edit these

- `_config.yml`: replace `YOUR-GITHUB-USERNAME` with your actual GitHub username.
- `_posts/2026-07-09-leading-smb1001-to-gold-tier-3.md`: has an `[EDIT NOTE]` at the top and a
  couple of inline edit prompts, fill in real specifics or trim them, then delete the note.
  Don't publish anything you can't back up if an interviewer asks about it directly.
- `_posts/2026-07-09-building-a-segmented-home-lab.md`: the "What's next" line about a Wazuh SIEM
  was my suggestion, not something you confirmed. Edit or remove it if it's not accurate.

## Adding a new blog post (this is the whole workflow, going forward)

1. Create a new file in `_posts/` named exactly `YYYY-MM-DD-your-title.md` (the date in the
   filename matters, that's how Jekyll sorts and dates posts).
2. Start it with this front matter, then write the post in Markdown underneath:

```
---
layout: post
title: "Your Post Title"
tags: [Tag One, Tag Two]
excerpt: "One or two sentences, this shows on the homepage and blog list."
---

Your post content here, normal Markdown: ## headings, **bold**, lists, etc.
```

3. Upload that one file to `_posts/` on GitHub (Add file → Upload files, or edit directly in the
   GitHub web UI with "Add file → Create new file"). Commit.
4. Give it a minute, it appears automatically on the homepage and `/blog/`, no other file needs
   to change.

## Custom domain (joshcapozzi.com), do this once you own the domain

1. At your domain registrar, add these DNS records:
   - Four **A** records for the apex domain (`joshcapozzi.com`) pointing to:
     `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - A **CNAME** record for `www` pointing to `YOUR-USERNAME.github.io`
2. In the repo, create a new file at the root named exactly `CNAME` (no file extension)
   containing one line: `joshcapozzi.com`
3. In **Settings → Pages**, enter `joshcapozzi.com` under "Custom domain", save, wait for the DNS
   check to go green, then tick **Enforce HTTPS**.
4. DNS can take anywhere from minutes to ~24 hours to propagate, don't panic if it's not instant.
