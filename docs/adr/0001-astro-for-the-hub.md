# Astro for the hub, not TanStack Start

The hub is built with Astro 7 as static files served by the `karimbelhadjali` Worker. Karim works in React and TanStack every day, so TanStack Start looks like the obvious pick, and it is the right tool for Sila. For the hub it would hydrate a full React app on every page and leave Karim to wire up three-locale routing, English fallback, per-locale RSS and hreflang sitemaps himself. Astro has all of those built in and ships no JavaScript unless a page asks for it. That made it worth a weekend of learning `.astro` syntax. Decided in [Choose the stack](https://github.com/karim-belhadjali/hub/issues/8), from [Research: static site stack for a three-locale hub](https://github.com/karim-belhadjali/hub/issues/2).

## The rest of the stack

- `@astrojs/react` is installed from the start. React is for islands such as the signup form, and pages stay `.astro`.
- Posts and project pages are MDX content collections with Zod schemas, so a post can embed a component without a later migration.
- Styling is Astro's scoped CSS plus a small set of CSS variables, with no Tailwind. Every rule uses logical properties such as `margin-inline-start`, so the same stylesheet flips for Arabic.
- pnpm, Node 24 LTS pinned in `.nvmrc` and `engines`, strict TypeScript.

## Considered options

- **TanStack Start.** Rejected for the hub because every page hydrates React, and it has no i18n, RSS or hreflang of its own. Kept for Sila.
- **Hugo.** It has the best built-in multilingual and RTL config, but it means Go templates and no React.
- **Next.js static export.** It puts the React runtime on every page and gives no locale routing in an export.
