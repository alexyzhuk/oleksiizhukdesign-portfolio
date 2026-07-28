# CLAUDE.md

Guidance for working in this repo. It's a personal portfolio site for Oleksii Zhuk (Product Designer), built with Astro + Tailwind v4. Static, no backend, no data fetching.

## Stack

- **Astro 5** (`.astro` components, file-based routing, MDX integration enabled but unused so far).
- **Tailwind CSS v4** via the `@tailwindcss/vite` plugin — configured in CSS, not JS. `tailwind.config.js` exists but is **empty**; do not add config there. The theme lives in `src/styles/global.css` under `@theme { ... }`.
- **Fonts**: Geist Sans + Geist Mono, self-hosted via `@fontsource/*`, imported (all weights) in `BaseLayout.astro`.
- `@astrojs/sitemap` is a devDependency but not yet wired into `astro.config.mjs`.
- No TypeScript app code beyond Astro frontmatter; `tsconfig` extends `astro/tsconfigs/strict`.

## Commands

```sh
npm run dev       # dev server at localhost:4321
npm run build     # production build to ./dist/
npm run preview   # preview the production build
```

There are **no tests, linters, or formatters** configured. Verify changes by running the dev server and checking pages in the browser.

## Routing & pages

Pages live in `src/pages/` (file = route):

- `index.astro` → `/` — home: Hero, SelectedWork, Method, Expertise, Testimonials, Contacts.
- `about.astro` → `/about` — HeroAlt, Story, RandomFacts.
- `case-study-datuum.astro` → `/case-study-datuum`
- `case-study-osvita.astro` → `/case-study-osvita`

All pages render `<BaseLayout>` directly (no surrounding `<html>/<head>/<body>` — BaseLayout owns the full document). BaseLayout also emits canonical URL, OpenGraph/Twitter meta, and JSON-LD `Person` structured data; pass `title`, `description`, and optionally `image` (OG image, defaults to `/og-image.jpg`). The `site` URL for canonical/OG/sitemap is set in `astro.config.mjs`.

## Layout & structure

- `src/layouts/BaseLayout.astro` — the document shell (`<html>`, `<head>`, favicons, font imports, global CSS). Renders `<Navigation>` (toggle with `showNav` prop), a `<slot />`, and `<Footer>`. Props: `title` (required), `description`, `showNav`.
- `src/components/` — homepage/about section components (one per page section).
- `src/components/casestudy/` — the reusable case-study design system: `CaseStudyLayout` (cover, sticky sidebar with company info + table of contents, prev/next nav) plus content blocks (`ImageBlock`, `Gallery`, `Table`, `StatsBlock`, `SingleStat`, `DoubleStat`, `ProblemStatement`, `VideoEmbed`, etc.).

### Adding a case study

Create `src/pages/case-study-<name>.astro`. Wrap content in `<BaseLayout>` → `<CaseStudyLayout>`, passing `company`, `companyColor`, `title`, `subtitle`, `coverImage`, `logo`, `about`, `industry`, `duration`, `website`, a `sections` array (`{ id, title }`, must match `<section id="...">` in the body for the TOC/scroll-spy), and optional `nextCase`/`prevCase`. Compose the body from the `casestudy/` blocks. Register the card in `SelectedWork.astro`'s `projects` array. Put images under `public/case-study-<name>/`.

## Styling conventions

- **Design tokens** are defined once in `src/styles/global.css` `@theme` — colors (`--color-text-primary`, `--color-bg-dark`, accent `--color-blue/-light`, etc.), a custom `xs` breakpoint (480px). Use these token-derived Tailwind utilities (`text-text-primary`, `bg-bg-dark`, `border-stroke-weak-dark`, `text-blue-light`…) rather than raw hex.
- The site is **dark-theme only** (`bg-dark` body, light text). A fixed noise texture (`/texture-dark.svg`) is painted via `body::before`.
- Reusable component classes (`.cs-section`, `.cs-h2`, `.cs-card`, `.nav-item`, `.method-card`, `.toc-link`, carousels, buttons…) are defined in `global.css` using `@apply`. Prefer reusing these over re-deriving long utility strings; look there first before styling a new case-study element.
- `.mono` applies Geist Mono. Headings get `font-medium tracking-tighter` globally.

## Client-side JavaScript

No framework/islands — interactivity is plain `<script>` tags inside `.astro` components (each runs as a module, scoped per component). Patterns in use: mobile menu + scroll-state in `Navigation`, carousels in `SelectedWork`/`Expertise`/`Testimonials`, scroll-spy + smooth-scroll + sticky/centered TOC in `CaseStudyLayout` (IntersectionObserver), lightbox/gallery behavior in `Gallery`/`ImageBlock`. When adding interactivity, follow this pattern (query DOM, guard with `?`, add listeners) rather than introducing a framework.

## Assets

All static assets are in `public/` and referenced by absolute path (`/foo.svg`). Case-study images are namespaced in `public/case-study-<name>/`; about-page images in `public/about/`. Preferred image formats already in use: `.avif`/`.webp` for photos, `.svg` for icons/diagrams.
