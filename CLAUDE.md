# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```sh
pnpm dev       # Start dev server at localhost:4321
pnpm build     # Build production site to ./dist/
pnpm preview   # Preview production build locally
pnpm astro check  # Type-check .astro files
```

## Stack

- **Astro 6** — file-based routing under `src/pages/`, `.astro` components use frontmatter (`---`) for server-side logic
- **Tailwind CSS v4** — integrated via `@tailwindcss/vite` plugin (no `tailwind.config.*` file); imported with `@import "tailwindcss"` in `src/styles/global.css`
- **TypeScript** — strict mode via `astro/tsconfigs/strict`
- **pnpm** — package manager (node ≥ 22.12.0 required)

## Architecture

- `src/layouts/Layout.astro` — root HTML shell; import global CSS here
- `src/pages/` — each `.astro` file becomes a route automatically
- `src/components/` — reusable `.astro` components
- `src/assets/` — static assets processed by Vite (use `import` to reference)
- `public/` — assets served as-is (favicon, etc.)

## Tailwind v4

### Configuration

No `tailwind.config.js`. Customizations go in `src/styles/global.css` via `@theme`:

```css
@import "tailwindcss";

@theme {
  --color-brand: #6366f1;
  --font-sans: "Inter", sans-serif;
}
```

### v4 Class Name Changes (Critical)

- `space-x-*` / `space-y-*` → use `gap-*` with flex/grid
- `divide-*` → use borders on individual children

### Color Tokens

Avoid raw Tailwind scale colors for project-specific palette. Define all project colors as `@theme` tokens:

```css
@theme {
  --color-dark: #111111;
  --color-muted: #666666;
  --color-border: #e5e5e5;
}
```

Then use `text-muted` instead of `text-neutral-400`.

### Spacing

Use the standard Tailwind scale (1 unit = 4px) over arbitrary values. `gap-2` beats `gap-[8px]`. Add to `@theme` if a non-standard value is used more than once.

## Icons

`@lucide/astro` provides tree-shakable SVG icons as Astro components.

```astro
---
import { Camera } from '@lucide/astro';
---

<Camera />
<Camera size={16} />
<Camera color="#ff3e98" size={48} stroke-width={1} />
```

**Props:**

| Prop | Default | Notes |
|---|---|---|
| `size` | `24` | Width & height in px |
| `color` | `currentColor` | Inherits from CSS by default |
| `stroke-width` | `2` | |
| `absoluteStrokeWidth` | `false` | Keep stroke width independent of size |

Tailwind classes can be applied via `class` prop: `<Camera class="w-4 h-4 text-muted" />`

### Shared Class Consolidation

When 3+ sibling elements share repeated classes, consolidate onto the parent with `*:`:

```html
<!-- ✅ -->
<ul class="*:hover:text-white">
  <li><a href="#">About</a></li>
  <li><a href="#">Works</a></li>
  <li><a href="#">Contact</a></li>
</ul>
```

`*:` applies to direct children only.
