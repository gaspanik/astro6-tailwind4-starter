# Astro 6 + Tailwind CSS v4 Starter

A minimal, opinionated starter for building static sites and web apps with **Astro 6**, **Tailwind CSS v4**, **Lucide icons**, and **Biome**.

## Stack

| Tool | Version | Role |
|---|---|---|
| [Astro](https://astro.build) | ^6 | Framework / SSG |
| [Tailwind CSS](https://tailwindcss.com) | ^4 | Styling (via Vite plugin) |
| [@lucide/astro](https://lucide.dev) | ^1 | Tree-shakable SVG icons |
| [Biome](https://biomejs.dev) | 2.x (pinned) | Lint + Format |
| TypeScript | ^6 | Type safety (strict mode) |
| pnpm | — | Package manager (node ≥ 22.12.0) |

## Project Structure

```text
/
├── .github/
│   ├── copilot-instructions.md  # GitHub Copilot guidelines
│   └── prompts/                 # Reusable prompt templates (e.g. new-page)
├── .vscode/
│   ├── extensions.json          # Recommended extensions
│   └── launch.json              # Debug configuration
├── public/
│   ├── favicon.ico
│   └── favicon.svg
├── src/
│   ├── assets/          # Vite-processed assets (import to reference)
│   ├── components/      # Reusable .astro components
│   ├── layouts/
│   │   └── Layout.astro # Root HTML shell — global CSS imported here
│   ├── pages/           # File-based routes (each .astro → a URL)
│   └── styles/
│       └── global.css   # @import "tailwindcss" + @theme tokens
├── AGENTS.md            # AI agent guidelines (OpenAI Codex / generic)
├── CLAUDE.md            # Claude Code guidelines
├── GEMINI.md            # Gemini CLI guidelines
├── astro.config.mjs
├── biome.json
└── package.json
```

## Commands

Run from the project root:

```sh
pnpm install       # Install dependencies
pnpm dev           # Start dev server at localhost:4321
pnpm build         # Build production site to ./dist/
pnpm preview       # Preview production build locally
pnpm astro check   # Type-check .astro files
pnpm lint          # Biome lint --write
pnpm format        # Biome format --write
pnpm check         # Biome check --write (lint + format combined)
pnpm astro-upgrade # Upgrade Astro via @astrojs/upgrade
```

## Tailwind v4 Configuration

No `tailwind.config.js`. All theme customization lives in `src/styles/global.css`:

```css
@import "tailwindcss";

@theme {
  --color-brand: #6366f1;
  --font-sans: "Inter", sans-serif;
}
```

Define project-specific colors as `@theme` tokens (e.g. `--color-muted`) instead of using raw Tailwind scale utilities.

## Icons

Import any [Lucide](https://lucide.dev/icons/) icon by name from `@lucide/astro`:

```astro
---
import { Camera } from '@lucide/astro'
---

<Camera size={24} class="text-muted" />
```

## Code Style

Biome handles lint and format for JS/TS/JSON/CSS. `.astro` files are excluded from Biome and checked via `pnpm astro check` instead.

Key style rules (see `biome.json` for full config):
- Single quotes, semicolons `asNeeded`, trailing commas
- JSX attributes use double quotes
- 80-char line width, 2-space indent, LF line endings

Always run `pnpm check` before committing.

## Security

`.npmrc` enforces supply-chain hygiene:
- `ignore-scripts=true` — suppresses postinstall scripts globally
- `min-release-age=3` — blocks packages published fewer than 3 days ago

`pnpm-workspace.yaml` holds the necessary exceptions (`allowBuilds`, `minimumReleaseAgeExclude`).
