# GBU blog

Source code for GBU blog made with Hugo.

## Quick start (development)

Open a terminal in the project root and run the dev server (includes drafts):

```fish
cd /Users/harshs/code/personal/gbu-blog
hugo server -D
```

Visit: http://localhost:1313

## Create a new post

Use the built-in new command which uses the archetype at `archetypes/post.md`:

```fish
# creates content/posts/my-new-post.md as draft
hugo new posts/my-new-post.md
```

Edit `content/posts/my-new-post.md`. When you're ready to publish set `draft = false` in the front matter.

## About page and author image

The about page is `content/about.md`.
Place your profile photo under `static/images/` (example: `static/images/harshs.png`). The page uses `/images/harshs.png` by default. Recommended sizes: 240×240 or 400×400.

## Build & deploy

Build a production version into `public/`:

```fish
hugo --minify
```

The `public/` directory is the output to upload to your hosting provider (Netlify, Vercel, S3, etc.).

## Git & ignore

A `.gitignore` is included with common excludes for Hugo, macOS, editors, node, and Python environments.

## Theme

This site uses the `Hugo-Gruvbox-Theme` made by [MayADevBe](https://github.com/MayADevBe/Hugo-Gruvbox-Theme?tab=readme-ov-file) located in `themes/Hugo-Gruvbox-Theme`.

## Notes & tips

- To include drafts in previews use `-D` with `hugo server`.
- If you want automatic image resizing / optimization, I can convert the avatar and other images to use Hugo Pipes and `resources.Get`.

---

If you'd like, I can add a short CONTRIBUTING.md or a CI deploy file for Netlify/Cloudflare Pages.
