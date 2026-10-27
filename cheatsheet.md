# Maintainer cheat sheet

Quick reference for editing this site. Everything you normally touch lives in three files:

| File | What's in it |
|---|---|
| `current_projects.md` | The whole page content: project table, how-to-apply text |
| `assets/css/style.css` | Colours, font sizes, spacing, layout |
| `_layouts/default.html` | Page skeleton: header, logos, theme toggle, footer |

After any change, rebuild the local preview (see the last section) and push to `main`.

---

## 1. Updating project information (most common)

Open `current_projects.md` and find the table under `## Current Projects`.

Each project is one row:

```markdown
| P | Benchmarking and adaptation (optional) of VLMs for medical tasks | <span class="status status--open">Open</span> | KI Proj 1/2 · 10 ECTS |
```

| Column | What to put |
|---|---|
| **Type** | `P`, `BA`, `MA`, `HiWi`, or a combination like `P/BA` |
| **Topic** | The title. Use `~~strikethrough~~` for taken topics (optional but consistent) |
| **Status** | `<span class="status status--open">Open</span>` or `<span class="status status--taken">Taken</span>` |
| **Notes** | ECTS, prerequisites, "KI Proj 1/2", etc. Use `—` when empty |

**Add a project** — copy an existing row and edit it. Keep the leading and trailing `|`.

**Mark a project as taken** — replace the status cell with:

```html
<span class="status status--taken">Taken</span>
```

and optionally strike the title: `~~Project title~~`.

**Remove a project** — delete the entire row.

**Reorder** — rows appear in the order they are written; there is no automatic sorting.
Put the most interesting or newest topics first.

### Rules that break the table

- Do **not** put a `|` inside a cell (it splits the column). Write `·` or `/` instead.
- Keep exactly four cells per row.
- Keep a blank line before and after the table.

### Type legend

Below the table there is one line explaining the codes:

```html
<p class="project-types"><strong>BA</strong> Bachelor's thesis, 10 ECTS · …</p>
```

If you introduce a new code (e.g. `MA`), add it here too.

---

## 2. Changing page text

At the top of `current_projects.md` is the YAML front matter:

```yaml
---
layout: default
title: Student Projects & Theses
lede: "The intro paragraph under the title."
description: "Used for search engines / link previews, not shown on the page."
---
```

- `title` — the big heading
- `lede` — the paragraph under it
- `description` — invisible on the page (SEO only)

The rest of the file is ordinary Markdown: `## Section` for headings, `- ` for bullets,
`1. ` for numbered lists, `> ` for the highlighted box.

---

## 3. Font sizes

All in `assets/css/style.css`. Search for the selector name to jump there.

| What | Selector | Current size |
|---|---|---|
| Body text | `body` | `1.0625rem` (17px), 1.125rem on wide screens, 1rem on phones |
| Section headings ("HOW TO APPLY") | `.prose h2` | `1.3rem` |
| Hero title | `.page-hero .page-title` | `clamp(1.8rem, 3.2vw, 2.45rem)` |
| Hero intro paragraph | `.page-lede` | `1.02rem` |
| Table text | `.prose table` | `0.9rem` |
| Table header | `.prose thead th` | `0.66rem` |
| Status labels (OPEN / TAKEN) | `.status` | `0.66rem` |
| Header wordmark | `.site-title` | `1rem` |
| Footer | `.site-footer` | `0.7rem` |

`rem` is 16px × value. Changing `body` does **not** scale everything — most elements
use `rem`, which follows the root font size, not `body`.

---

## 4. Colours

The palette is defined once at the top of `assets/css/style.css`:

```css
--paper: #eef2f6;   /* page background */
--ink: #1d1d1b;     /* main text */
--muted: #5c6670;   /* secondary text */
--rule: #dadde1;    /* hairlines */
--accent: #002f67;  /* JMU blue: links, header rule */
--open: #2f6b4f;    /* green status dot */
--taken: #8a5a52;   /* red status dot */
```

Dark mode overrides the same names in **two** places — update both, or the manual
toggle and the OS setting will disagree:

1. `@media (prefers-color-scheme: dark) { :root:not([data-theme]) { … } }`
2. `:root[data-theme="dark"] { … }`

Two more theme colours live next to them:

- `--surface` — background of the header bar
- `--hero-bg` — background of the banner card

By default the site follows the visitor's OS theme; the half-circle button in the
header lets visitors override it (stored in `localStorage`).

---

## 5. Preview & publish

```powershell
# one-time, if .preview/node_modules is missing
npm install --prefix .preview

# rebuild the static preview, then open it
node .preview/build.mjs
start .preview/index.html
```

`.preview/` is git-ignored: it is a local renderer, not part of the site.
Never edit `.preview/index.html` by hand — it is regenerated.

To publish:

```powershell
git add -A
git commit -m "update projects"
git push
```

GitHub Pages rebuilds automatically in about a minute.

### Don't break the base path

`_config.yml` contains:

```yaml
url: "https://pattern-recognition-jmu-test.github.io"
baseurl: "/open_projs"
```

`baseurl` must match the repository name. If it is wrong, the live site loads without
CSS, logos and banner image (the local preview will still look fine, so check the live
URL after pushing).

---

## 6. Common mistakes

- Editing `.preview/index.html` instead of the source files — it gets overwritten.
- Changing colours in only one of the two dark-mode blocks.
- Using a `|` inside a table cell.
- Forgetting a blank line between Markdown and a raw `<p …>` / `<span …>` block.
- Changing `baseurl` or the repository name without updating the other.
