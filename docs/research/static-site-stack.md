# Static site stack for a three-locale hub

Research for [#2](https://github.com/karim-belhadjali/hub/issues/2), feeding [Choose the stack](https://github.com/karim-belhadjali/hub/issues/8). Checked against primary sources on 2026-10-25.

## Short answer

Use Astro 7. It is the only candidate that covers every item on the ticket with first-party pieces: locale routing with fallback, typed Markdown and MDX content, per-locale RSS, a sitemap with hreflang alternates, and zero client JavaScript by default. It deploys to Cloudflare as plain static files with no adapter. Karim keeps React for anything interactive, as islands.

TanStack Start is now stable and can prerender, but for a content site it costs more than it saves. Every page hydrates a full React app, and i18n, RSS and hreflang are all assembly work. It is the right tool for Sila, not for the hub.

Hugo has the strongest built-in multilingual support of the lot, but it means Go templates and no React. Next.js static export works but brings React runtime weight and no i18n routing in the App Router.

## Versions checked

| Package | Latest | Released | Source |
|---|---|---|---|
| astro | 7.4.2 | 2026-10-21 | [npm](https://registry.npmjs.org/astro/latest), [changelog](https://github.com/withastro/astro/blob/main/packages/astro/CHANGELOG.md) |
| @astrojs/react | 7.1.1 | 2026-10-21 | [npm](https://registry.npmjs.org/@astrojs/react/latest) |
| @astrojs/mdx | 8.1.1 | | [npm](https://registry.npmjs.org/@astrojs/mdx/latest) |
| @astrojs/rss | 4.0.19 | | [npm](https://registry.npmjs.org/@astrojs/rss/latest) |
| @astrojs/sitemap | 3.7.4 | | [npm](https://registry.npmjs.org/@astrojs/sitemap/latest) |
| @tanstack/react-start | 1.169.4 | 2026-10-22 | [npm](https://registry.npmjs.org/@tanstack/react-start/latest) |
| next | 16.4.3 | | [npm](https://registry.npmjs.org/next/latest) |
| hugo | 0.168.0 | 2026-10-13 | [GitHub releases](https://github.com/gohugoio/hugo/releases/tag/v0.168.0) |
| wrangler | 4.152.0 | | [npm](https://registry.npmjs.org/wrangler/latest) |

Recent changes that matter here:

- Astro 7.0.0 shipped on 2026-06-22. It moved to Vite 8, made the Rust compiler the default, and made Sätteri the default Markdown processor in place of remark and rehype. Remark plugins still work but now need `@astrojs/markdown-remark` installed. [Changelog 7.0.0](https://github.com/withastro/astro/blob/main/packages/astro/CHANGELOG.md#700)
- Astro 7.4 raised the minimum Node to 22.22.0 and 7.4.2 raised it again to 22.23.4. Pin Node in CI. [Changelog 7.4.0 and 7.4.2](https://github.com/withastro/astro/blob/main/packages/astro/CHANGELOG.md)
- The Astro team joined Cloudflare on 2026-01-16. Astro stays MIT and the team says it stays platform agnostic. For a hub hosted on Cloudflare this is a plus, not a lock-in. [Astro blog](https://astro.build/blog/joining-cloudflare/)
- TanStack Start 1.0 was declared stable on 2026-10-13, twelve days ago. [TanStack blog](https://tanstack.com/blog/tanstack-start-1-0)

## Comparison

### Locale routing and fallback

**Astro.** Built in. `i18n.locales`, `i18n.defaultLocale` and `i18n.routing.prefixDefaultLocale` configure URLs. `i18n.fallback` maps a locale to another, so `fr: "en"` makes Astro build a French page for every English page that has no French file. `fallbackType` picks a redirect or a rewrite that serves the English content at the French URL. `getRelativeLocaleUrl()` builds links and `Astro.currentLocale` works on prerendered pages. [i18n guide](https://docs.astro.build/en/guides/internationalization/) Static builds generate rewrite fallbacks, including for dynamic routes. [Changelog 7.1.6, PR 17536](https://github.com/withastro/astro/pull/17536) Adding a locale is one entry in the array.

That built-in fallback works per page file. Posts live in one dynamic route fed by a content collection, so the cleaner fallback for posts is explicit: in `getStaticPaths` for `[locale]/posts/[slug]`, emit every English post for every locale and use the translation when it exists. This keeps the fallback rule in one readable function.

**TanStack Start.** No i18n layer of its own. The Router guide offers an optional `{-$locale}` path param, or Paraglide with the router's `rewrite` option and a `paraglideMiddleware` around the server entry. For prerendering, the guide builds the localized page list by hand with `localizeHref`. [Router i18n guide](https://tanstack.com/router/latest/docs/guide/internationalization-i18n) The official example detects locale by URL, cookie and browser preference, which a static host can't do. Only the URL strategy survives a static build. [start-i18n-paraglide vite.config.ts](https://github.com/TanStack/router/blob/da2adec76dbe6c0da6d6a0d5640f7b9388217f45/examples/react/start-i18n-paraglide/vite.config.ts) Fallback to English is code Karim writes.

**Next.js static export.** Redirects, rewrites and proxy are unsupported with `output: 'export'`, so locale handling is a `[lang]` segment plus `generateStaticParams`. [Static exports](https://nextjs.org/docs/app/guides/static-exports)

**Hugo.** Built in. Translations link by filename or `translationKey`, and each language can have its own content directory. Hugo falls back to the default language for UI strings, but the docs don't promise content fallback. That takes `lang.Merge`. [Multilingual](https://gohugo.io/content-management/multilingual/)

### Right-to-left layout

No framework does RTL for you. In all of them the work is the same three things: set `dir` and `lang` on `<html>` per locale, write CSS with logical properties such as `margin-inline-start` so one stylesheet flips, and load an Arabic font. [MDN logical properties](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values)

- **Astro.** Set `dir` in the base layout from `Astro.currentLocale`. The Fonts API self-hosts Google, Fontsource or local fonts at build time and preloads with `<Font preload />`. [Fonts guide](https://docs.astro.build/en/guides/fonts/)
- **TanStack Start.** Set `dir` in the root route. The Router guide covers `lang` but not `dir`. [Router i18n guide](https://tanstack.com/router/latest/docs/guide/internationalization-i18n)
- **Hugo.** The one stack with direction in config. Each language takes `direction: rtl` and templates read it. [Languages config](https://gohugo.io/configuration/languages/)

### Content authoring

**Astro.** Content collections in `src/content.config.ts` with a `glob()` loader and a Zod 4 schema give typed frontmatter. A build fails on bad frontmatter. Per-locale content is folders, filtered by id prefix such as `en/`. [Content collections](https://docs.astro.build/en/guides/content-collections/) `@astrojs/mdx` adds MDX to collections and lets posts import React components. [MDX integration](https://docs.astro.build/en/guides/integrations-guide/mdx/)

**TanStack Start.** No content layer. The docs point to the third-party Content Collections package with Zod for typed frontmatter, rendered by `@tanstack/markdown`, which does not execute MDX. [Rendering Markdown](https://tanstack.com/start/latest/docs/framework/react/guide/rendering-markdown)

**Hugo.** Markdown with front matter, no schema validation and no MDX.

### RSS, sitemap and Open Graph

**Astro.** `@astrojs/rss` builds a feed from a static endpoint. Endpoints take `getStaticPaths`, so `src/pages/[locale]/rss.xml.ts` gives one feed per locale. [RSS guide](https://docs.astro.build/en/guides/rss/), [Endpoints](https://docs.astro.build/en/guides/endpoints/) It emits RSS only. Atom would be a hand-written endpoint. `@astrojs/sitemap` takes an `i18n` option and writes an `xhtml:link` hreflang alternate for every locale on every URL. It needs `site` set. [Sitemap integration](https://docs.astro.build/en/guides/integrations-guide/sitemap/) Open Graph tags are plain `<meta>` in the layout.

**TanStack Start.** A built-in sitemap option on the `tanstackStart()` plugin, fed by link crawling during prerender. [SEO guide](https://tanstack.com/start/latest/docs/framework/react/guide/seo) The source accepts `alternateRefs` with `hreflang` per page, but only for pages listed by hand in `pages`. [schema.ts](https://github.com/TanStack/router/blob/da2adec76dbe6c0da6d6a0d5640f7b9388217f45/packages/start-plugin-core/src/schema.ts), [build-sitemap.ts](https://github.com/TanStack/router/blob/da2adec76dbe6c0da6d6a0d5640f7b9388217f45/packages/start-plugin-core/src/build-sitemap.ts) No RSS helper. Open Graph tags go in each route's `head()`.

**Hugo.** Per-language sitemaps under a sitemap index, with hreflang alternates for translated pages. [Sitemap templates](https://gohugo.io/templates/sitemap/), [embedded sitemap.xml](https://github.com/gohugoio/hugo/blob/master/tpl/tplimpl/embedded/templates/sitemap.xml)

### Static output, hosting and client JavaScript

**Astro.** `output: 'static'` is the default and prerenders every page. [Config reference](https://docs.astro.build/en/reference/configuration-reference/) Framework components render to HTML and ship no JavaScript unless they carry a `client:*` directive. [Islands](https://docs.astro.build/en/concepts/islands/), [Framework components](https://docs.astro.build/en/guides/framework-components/) On Cloudflare a static site needs no adapter: `astro build`, then `wrangler deploy` with an `assets` block pointing at `./dist`. [Deploy to Cloudflare](https://docs.astro.build/en/guides/deploy/cloudflare/)

**TanStack Start.** `prerender.enabled` with `crawlLinks` writes static HTML. Param routes are found only through crawled links or the `pages` list. [Static prerendering](https://tanstack.com/start/latest/docs/framework/react/guide/static-prerendering) Every SSR mode hydrates on the client. No option renders HTML without shipping React. [Selective SSR](https://tanstack.com/start/latest/docs/framework/react/guide/selective-ssr) The official Cloudflare path is a Worker built with `@cloudflare/vite-plugin`, not a static upload. [Hosting](https://tanstack.com/start/latest/docs/framework/react/guide/hosting)

**Cloudflare free plan.** Requests to static assets are free and unlimited. [Static assets billing](https://developers.cloudflare.com/workers/static-assets/billing-and-limitations/) Worker script requests are capped at 100,000 a day, with 20,000 files per version and 25 MiB per file. [Workers limits](https://developers.cloudflare.com/workers/platform/limits/)

**Build speed.** I found no benchmark from a primary source comparing these stacks. Astro 7 claims faster builds from the Rust compiler. [Changelog 7.0.0](https://github.com/withastro/astro/blob/main/packages/astro/CHANGELOG.md#700) A hub with a few dozen pages builds in seconds on any of them, so this shouldn't drive the choice.

### Third-party signup form

All three patterns work on every candidate, because they're HTML concerns.

- **Plain HTML post.** A `<form action>` pointing at the email service's endpoint. No JavaScript, works on any static page. The best fit for zero-JS Astro pages if the service supports it.
- **Script embed.** In Astro, mark the provider's tag `is:inline` so Astro leaves it untouched. [Client-side scripts](https://docs.astro.build/en/guides/client-side-scripts/) In TanStack Start it goes in `head()` scripts or a component.
- **Serverless function.** On Cloudflare, a small Worker next to the static assets, routed through `run_worker_first` for one path only so the rest stays free. [Static assets billing](https://developers.cloudflare.com/workers/static-assets/billing-and-limitations/) TanStack Start server functions would need its whole server bundle on Workers, which ends the static deploy.

### Cost to learn for Karim

- **Astro.** New `.astro` files, a superset of HTML with JSX-like expressions. Attributes stay HTML, so `class` not `className`. [Astro syntax](https://docs.astro.build/en/basics/astro-syntax/) His React components drop in through `@astrojs/react`. One trap: hydrated islands can't receive functions as props. [Framework components](https://docs.astro.build/en/guides/framework-components/) I'd put it at a weekend.
- **TanStack Start.** Nothing new to learn. The cost moves into building the i18n, RSS, hreflang and content plumbing himself, and into maintaining it.
- **Next.js.** Familiar React, new routing and caching model, and the static export removes the features that make Next worth it.
- **Hugo.** Go templates and Hugo's lookup rules. No React at all.

## Verdict per option

- **Astro 7.** Recommended. Every ticket item is first party, pages ship no JS, React stays available.
- **TanStack Start.** Not for the hub. Hydrates every page and leaves i18n, RSS and hreflang to hand-rolled code. Keep it for Sila.
- **Hugo.** Runner-up on features alone, with RTL direction and hreflang sitemaps built in. Rejected for the template language and no React.
- **Next.js static export.** Rejected. React runtime on every page, no i18n routing in the export, no RSS.
- **Eleventy 3.1.7.** Current per [npm](https://registry.npmjs.org/@11ty/eleventy/latest), not examined in depth. Same trade as Hugo without the built-in RTL config.

## Open points for Choose the stack

1. **Fallback for posts.** The built-in `i18n.fallback` or an explicit `getStaticPaths` that serves English content at `/fr/posts/x`? I'd go explicit. Either way, decide what an untranslated page says: a notice, `lang="en"` on the article, and whether its hreflang and canonical point at the English URL.
2. **Default locale in the URL.** `prefixDefaultLocale: true` gives `/en/...` everywhere and symmetric URLs, `false` keeps English at the root. Pick before the first LinkedIn post, because changing it breaks posted links.
3. **Markdown processor.** Astro 7 defaults to Sätteri. If any remark or rehype plugin is wanted, opt into `@astrojs/markdown-remark` from day one.
4. **Arabic font subsets.** The Fonts API docs show `latin` and `cyrillic` subsets but don't mention `arabic`. Confirm in a prototype, or self-host an Arabic font with the local provider.
5. **Signup pattern.** Depends on the email service ticket. Plain HTML post first, script embed second, Worker last.
6. **Atom or RSS.** `@astrojs/rss` writes RSS 2.0 only. RSS is enough for LinkedIn and readers, unless someone needs Atom.
7. **Cloudflare Workers or Pages.** Astro's guide recommends Workers static assets for new projects. That also leaves room for the one-path Worker if the form needs it.

## Not verified

- Whether Astro's `rewrite` fallback sets `Astro.currentLocale` to the requested locale or the fallback locale. This decides what `lang` and `dir` a fallback page gets. Test in the prototype.
- Whether the Fonts API is stable or experimental in 7.4. The page doesn't say, and it's not listed under experimental features.
- Build-time numbers for any stack.
