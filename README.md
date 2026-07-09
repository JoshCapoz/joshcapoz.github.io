# joshcapozzi.com

Personal portfolio and blog for Joshua Capozzi, a cybersecurity analyst based in Perth, Western Australia. Live at [joshcapozzi.com](https://joshcapozzi.com).

The site covers current security operations work, a home lab used for hands-on practice, and write-ups on projects like leading an SMB1001 Gold (Tier 3) certification.

## Stack

- [Jekyll](https://jekyllrb.com/), built and served automatically by GitHub Pages, no CI config required
- Plain HTML/Liquid templates and hand-written CSS, no frontend framework or build tooling
- [`jekyll-feed`](https://github.com/jekyll/jekyll-feed) for RSS, [`jekyll-sitemap`](https://github.com/jekyll/jekyll-sitemap) for search indexing
- Custom domain via GitHub Pages, DNS pointed at GitHub's servers

## Structure

```
_config.yml            Site config, title, description, plugins
_layouts/default.html  Base page template (nav, head, footer)
_layouts/post.html     Blog post template
_posts/                Blog posts, one Markdown file per post
index.html             Homepage
blog/index.html        Full post archive
assets/css/style.css   All styling
assets/img/            Favicon and other images
```

## Writing a new post

Add a file to `_posts/` named `YYYY-MM-DD-title.md`:

```markdown
---
layout: post
title: "Post Title"
tags: [Tag One, Tag Two]
excerpt: "One or two sentences shown on the homepage and blog list."
---

Post content in Markdown.
```

Push it to `main` and it appears on the homepage and `/blog/` automatically, no other file needs to change.

## License

Site content and code are © Joshua Capozzi. Feel free to reference the structure or approach for your own Jekyll site.
