# Copilot Instructions

This project is an **Astro 6 + Tailwind CSS v4** starter. Follow the conventions below when generating or editing code.

## Stack

- **Astro 6** — file-based routing under `src/pages/`; `.astro` components use frontmatter (`---`) for server-side logic
- **Tailwind CSS v4** — no `tailwind.config.*`; configured via `@theme` in `src/styles/global.css`; imported with `@import "tailwindcss"`
- **TypeScript** — strict mode (`astro/tsconfigs/strict`)
- **pnpm** — package manager (Node ≥ 22.12.0)

## Project Structure

```
src/layouts/Layout.astro   # Root HTML shell — import global CSS here
src/pages/                 # Each .astro file becomes a route
src/components/            # Reusable .astro components
src/assets/                # Static assets processed by Vite (use import)
public/                    # Assets served as-is
src/styles/global.css      # Tailwind entry — @theme tokens go here
```

## Dev Commands

```sh
pnpm dev          # localhost:4321
pnpm build        # Build to ./dist/
pnpm preview      # Preview production build
pnpm astro check  # Type-check .astro files
pnpm check        # Biome lint + format (run before finishing any change)
```

## Code Style (Biome — no ESLint/Prettier)

- Single quotes, semicolons `asNeeded`, trailing commas
- JSX attributes use double quotes
- 80-char line width, 2-space indent, LF line endings
- `.astro` files are excluded from Biome — type-check with `pnpm astro check`

**Always run `pnpm check` before finishing any code change.**

## Tailwind v4 Rules

### Customization

Add tokens to `src/styles/global.css` — never create a `tailwind.config.js`:

```css
@import "tailwindcss";

@theme {
  --color-brand: #6366f1;
  --font-sans: "Inter", sans-serif;
}
```

### Breaking Changes from v3

- `space-x-*` / `space-y-*` → use `gap-*` with flex/grid
- `divide-*` → use borders on individual children

### Color Tokens

Never use raw Tailwind scale colors for project-specific UI. Define named tokens:

```css
@theme {
  --color-dark: #111111;
  --color-muted: #666666;
  --color-border: #e5e5e5;
}
```

Use `text-muted` instead of `text-neutral-400`.

### Spacing

Prefer the standard Tailwind scale (`gap-2`) over arbitrary values (`gap-[8px]`). Add to `@theme` if a non-standard value is used more than once.

### Shared Class Consolidation

When 3+ sibling elements share the same classes, consolidate onto the parent with `*:`:

```html
<ul class="*:hover:text-white">
  <li><a href="#">About</a></li>
  <li><a href="#">Works</a></li>
  <li><a href="#">Contact</a></li>
</ul>
```

## Icons

Use `@lucide/astro` for tree-shakable SVG icons:

```astro
---
import { Camera } from '@lucide/astro';
---

<Camera size={16} class="text-muted" />
```

| Prop | Default |
|---|---|
| `size` | `24` |
| `color` | `currentColor` |
| `stroke-width` | `2` |

## Adding Dependencies

`.npmrc` enforces `ignore-scripts=true` and `min-release-age=3`. If a package fails to install:

- Needs build scripts → add to `allowBuilds` in `pnpm-workspace.yaml`
- Just published → add to `minimumReleaseAgeExclude` in `pnpm-workspace.yaml`

## Accessibility

Do not write uppercase text directly in HTML. Apply `text-transform: uppercase` via the `uppercase` Tailwind class instead. This prevents screen readers from spelling out each letter.

```html
<!-- ❌ -->
<a href="#concept">CONCEPT</a>

<!-- ✅ -->
<a href="#concept" class="uppercase">Concept</a>
```
