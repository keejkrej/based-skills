# Static sites

Content-heavy, marketing, blog, and documentation sites where shipping HTML is the point.

## Defaults

- Use Astro for static sites, blogs, docs, and lightweight marketing pages
- Prefer static generation (`output: 'static'`) unless a route genuinely needs server-side data
- Use Astro Content Collections for typed, structured content (blogs, docs, changelogs)
- Use MDX for pages that need embedded components or rich layouts
- Add interactivity as Astro Islands — reach for React/Preact/Svelte/Vue only when a component needs client JS
- Keep the SPA stack (React, Effect Atom, TanStack Router) out of content sites unless the site is actually an app
- Use Tailwind CSS v4 for styling
- Use Bun for package management and runtime
- Build toolchain and component install → `tooling` skill

## When to choose Astro over the React SPA stack

- Mostly content with occasional islands of interactivity
- SEO-first pages that must render without client hydration
- Blogs, docs, landing pages, and changelog sites
- Performance target is fast first paint, not rich client state

## When to fall back to the SPA stack

- Persistent client state, dashboards, authenticated workspaces, or real-time collaboration
- Complex routing and data-loading patterns
- Heavy Effect/Schema contracts shared with a TS backend

## Islands

- Start with zero JS; add a client directive only when a component needs it
- Common directives: `client:load`, `client:visible`, `client:media`, `client:only`
- Keep island components small and isolated; share props via slot/children, not global stores

## Content

- Organize content under `src/content/` with collection schemas in `src/content/config.ts`
- Use frontmatter for metadata; validate with `zod` or a schema library
- Reference static assets from `public/`; optimize images with Astro's built-in `<Image />` component

## Docs

- Astro: https://docs.astro.build
- Astro Content Collections: https://docs.astro.build/en/guides/content-collections/
- Astro Islands: https://docs.astro.build/en/concepts/islands/
