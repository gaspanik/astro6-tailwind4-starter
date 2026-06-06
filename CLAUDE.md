# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Design Reference

If `DESIGN.md` exists in the project root, read it before starting any UI or feature work. It contains the site's design intent, color palette, typography, layout guidelines, and component conventions — treat it as the source of truth for visual and UX decisions.

## Commands

```sh
pnpm dev           # Start dev server at localhost:4321
pnpm build         # Build production site to ./dist/
pnpm preview       # Preview production build locally
pnpm astro check   # Type-check .astro files
pnpm lint          # Biome lint --write (JS/TS/JSON/CSS)
pnpm format        # Biome format --write (JS/TS/JSON/CSS)
pnpm check         # Biome check --write (lint + format combined)
pnpm astro-upgrade # Upgrade Astro via @astrojs/upgrade
```

### Astro Docs MCP (`mcp__AstroDocs__search_astro_docs`)

Use this tool when working with Astro-specific APIs, integrations, or configuration. Prefer it over training-data knowledge for:

- Astro component syntax, frontmatter, slots, `<slot />` usage
- Integrations: `@astrojs/react`, `@astrojs/tailwind`, `@astrojs/image`, etc.
- Content Collections, Actions, View Transitions, SSR/SSG config
- `astro.config.mjs` options and Vite plugin setup
- Any Astro 6 feature that may differ from earlier versions

Always check the docs when the behavior is uncertain or the user hits an unexpected error.

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

## Package Security

`.npmrc` enforces two project-wide constraints:

- `ignore-scripts=true` — suppresses postinstall scripts globally (supply-chain safety)
- `min-release-age=3` — blocks packages published fewer than 3 days ago

`pnpm-workspace.yaml` handles the necessary exceptions:

- `allowBuilds: [esbuild, sharp]` — grants build-script permission to these two packages that need it
- `minimumReleaseAgeExclude` — exempts specific Astro packages (e.g. freshly released patch versions) from the age gate

**When adding a new dependency:** if it fails to install due to these constraints, add it to `allowBuilds` (if it needs build scripts) or `minimumReleaseAgeExclude` (if it was just published) in `pnpm-workspace.yaml`.

## Code Quality (Biome)

No ESLint or Prettier. JS/TS/JSON/CSS is managed by **Biome**. `.astro` files are excluded from Biome — use `pnpm astro check` for type-checking them.

### Style Rules

- Single quotes, semicolons `asNeeded`, trailing commas
- JSX attributes use double quotes
- 80-char line width, 2-space indent, LF line endings

```ts
const example = {
  name: 'value',
  items: [1, 2, 3],
}
```

```tsx
<Component attr="value" />
```

Always run `pnpm check` before finishing any code change.

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

**Do not use v3 class names.** Common mistakes:

- `space-x-*` / `space-y-*` → use `gap-*` with flex/grid
- `divide-*` → use borders on individual children
- `shadow-sm` → `shadow-xs`
- `bg-opacity-*` / `text-opacity-*` → use `bg-black/50` syntax
- `flex-shrink` / `flex-grow` → `shrink` / `grow`
- `overflow-ellipsis` → `text-ellipsis`

### Font Family

`--heading-font-family` is already applied to h1–h6 via `@layer base` in `src/style.css`.
`--default-font-family` is set as the default body font via `@theme`.

**Never write these classes:**
- `font-[var(--heading-font-family)]`
- `font-(--heading-font-family)`
- `font-[var(--default-font-family)]`
- `font-(--default-font-family)`

They are redundant on heading elements and `<body>`.
If you need the heading font on a non-heading element (e.g. a logo `<a>` or `<p>`),
add the selector to `@layer base` or define a dedicated utility in `@theme` instead.

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

## Style Conventions

- Indentation: 2 spaces, LF line endings (enforced by `.editorconfig`).
- Use `uppercase` Tailwind class instead of writing text in ALL CAPS in HTML (accessibility: prevents screen readers from spelling out letters).
