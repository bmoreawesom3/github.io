# Portfolio Template

A clean, dependency-free portfolio you can deploy to GitHub Pages. It has a
**School** section and a **Professional** section, light/dark mode, a mobile
menu, and subtle scroll animations. No build step, no frameworks — just edit
the files and push.

## Files

```
portfolio/
├── index.html        ← all your content lives here
├── css/style.css     ← colors, fonts, layout
├── js/main.js        ← theme toggle, menu, animations
├── assets/
│   └── favicon.svg    ← browser tab icon
└── README.md
```

## Customize

**Your content** — open `index.html` and edit the text. Look for the
`EDIT ME` comments and the placeholder copy ("Your Name", "Project title", etc.).

**Add a project** — copy a whole `<article class="card"> … </article>` block
and paste it inside the same section's `<div class="grid">`. Update the title,
description, tags, links, and the ID (`S-01`, `S-02`… for school; `P-01`,
`P-02`… for professional).

**Use a screenshot on a card** — inside `.card-cover`, replace the
`<span class="card-id">…</span>` with:

```html
<img src="assets/my-screenshot.png" alt="Short description" />
```

Put the image file in the `assets/` folder.

**Change the color** — in `css/style.css`, edit `--accent` under `:root`
(and `--accent` under `[data-theme="dark"]` for the dark version). That one
variable drives buttons, links, and highlights.

**Change the fonts** — swap the Google Fonts `<link>` in `index.html`, then
update `--font-display`, `--font-body`, and `--font-mono` in `style.css`.

## Deploy to GitHub Pages

1. Create a repository named **`your-username.github.io`** (use your actual
   GitHub username).
2. Put these files in the **root** of the repo (so `index.html` is at the top
   level, not inside a subfolder).
3. Commit and push.
4. In the repo, go to **Settings → Pages**, set the source to the `main`
   branch / root, and save.
5. Your site goes live at `https://your-username.github.io` within a minute or
   two.

To preview locally first, just open `index.html` in a browser — everything
works from the file system.

## Accessibility & polish (already built in)

- Semantic landmarks, a skip link, and visible keyboard focus
- Respects the "reduce motion" system setting
- Responsive down to small phones
- Remembers the visitor's light/dark choice
