# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

`astro-intecsa` is the static marketing site for Grupo Intecsa. It is built with **Astro 5**, **React 19** (currently installed but unused), and **Tailwind CSS 4**. The site is fully static — no server runtime, API routes, or data layer.

## Common commands

Both `package-lock.json` and `pnpm-lock.yaml` are present, but **npm is the preferred package manager** unless the user says otherwise.

| Command | Purpose |
| :------ | :------ |
| `npm install` | Install dependencies |
| `npm run dev` | Start Astro dev server on `localhost:4321` |
| `npm run build` | Static build to `./dist/` |
| `npm run preview` | Preview the production build locally |
| `npm run astro -- ...` | Run Astro CLI commands (e.g. `astro add`, `astro check`) |

There is **no test runner or linter configured** in this project.

## High-level architecture

### Static Astro site with file-based routing

Pages live in `src/pages/` and become routes automatically. The current routes are:

- `/` — homepage (`index.astro`)
- `/about` — company overview (`about.astro`)
- `/products` — catalog download page (`products.astro`)
- `/politica-privacidad`, `/terminos-servicio`, `/aviso-legal`, `/codigo-conducta` — legal pages

### Layout and page composition

`src/layouts/Layout.astro` is the shell used by every page. It contains:

- A fixed top navbar with a hardcoded external logo and a contact link to a Monday form.
- A `<slot/>` wrapped in `mt-16` to clear the navbar.
- `Footer.astro` rendered at the bottom.
- Global meta description, title, favicon, and a JSON-LD Organization schema block.
- Import of `src/styles/global.css`.

Page frontmatter imports `Layout` and any section components, then wraps content in `<Layout>`. Several pages pass `title="... | Grupo INTECSA"` to `<Layout>`, but `Layout.astro` does not currently consume a `title` prop — it always uses `Grupo Intecsa`.

### Styling

Tailwind CSS 4 is wired through `@tailwindcss/vite` in `astro.config.mjs`. Custom global styles, keyframes (e.g. `bannerZoom`), and the `@import "tailwindcss"` directive live in `src/styles/global.css`. No Tailwind config file exists; customization is done via arbitrary values in class names.

### Components

Components in `src/components/` are written as `.astro` files:

- `Welcome.astro` — homepage content (hero banner, ABB certification section, featured cards, service/blog cards). It imports `Certifications.astro` for the certificate gallery.
- `IntecsaHero.astro` — about page content (company description, mission/vision/values, recent work, director letter).
- `Footer.astro` — site footer used by `Layout.astro`.
- `Certifications.astro` — client-side gallery modal for certificate thumbnails stored in `public/certs/`. It uses an inline `<script>` that relies on `DOMContentLoaded` and IDs `gellery-cert` / `modal-cert`.
- `CertificacionABB.astro` — standalone ABB certification banner. It is currently unused by any page.

### Assets

Static files are served from `public/`:

- `/assets/` — images and the CV/resume PDF.
- `/certs/` — certificate thumbnails used by the gallery modal.
- `/icons/` — SVG icons used across pages.
- `certificado_abb_2025.pdf` and `certificado_abb_2025_preview.jpg` — ABB certificate files at the root.

`src/assets/` still contains the starter `astro.svg` and `background.svg` images, which are unused.

### React integration

`astro.config.mjs` registers `@astrojs/react` with `include: ["**/react/*"]`, and `tsconfig.json` sets `jsxImportSource: "react"`. No `.jsx`/`.tsx` files or `src/react/` directory exist yet, so React is effectively available but not used in the current build.

### External dependencies and hardcoded links

The site relies on several external resources that are not bundled:

- Logo in the navbar is hotlinked from `https://blog-omega-mauve.vercel.app/...`.
- Contact link points to a Monday.com embedded form.
- Footer map is a Google Maps iframe.
- Some images on `/about` are loaded from Facebook CDN URLs.

These should not be replaced with local assets unless explicitly requested.

### Important implementation notes

- The certificate gallery in `Certifications.astro` is client-side only and has hardcoded thumbnail filenames. Adding or removing images in `public/certs/` requires updating the `certs_images` array in that component.
- The legal pages share the same prose structure (`max-w-3xl mx-auto px-4 py-16`) and use placeholder/standard copy with a "julio 2026" update date.
- `products.astro` is mostly placeholder content with commented-out `Image` imports and links to `/catalogos/*` routes that do not exist.
