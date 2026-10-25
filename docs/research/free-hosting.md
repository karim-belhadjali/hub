# Free hosting and leaving a free address

Research for [#3](https://github.com/karim-belhadjali/hub/issues/3), feeding [Choose the host and the free address](https://github.com/karim-belhadjali/hub/issues/9). Checked against vendor docs on 2026-10-25.

## Short answer

Host the hub on Cloudflare Pages, in the same account as `sila-demo.pages.dev`.

Pages is the only option where the free address can hand off to the custom domain with a documented, code-free 301 that keeps every path, costs nothing and counts against no quota. That mechanism is a Bulk Redirect on `<project>.pages.dev`, and Cloudflare publishes a guide for exactly this case. GitHub Pages does the same handoff automatically and for free, but it can't do language negotiation, custom headers or a form proxy, so it loses on everything else. Netlify and Vercel can both do the redirect with a host-matching rule, but Netlify's free plan pauses the whole site, redirect included, once 300 monthly credits run out, and Vercel's Hobby terms bar commercial use, which a waitlist built to show investors a traction number arguably is.

Cloudflare now tells people to start new projects on Workers instead of Pages. I'd ignore that here. Pages isn't deprecated, and the Workers route gives a longer two-label address and no documented free redirect for `workers.dev`.

## The redirect after the domain move

This is the fact the decision turns on. Each row says how the old free address sends every path to the same path on the custom domain, for good.

| Host | Mechanism | Free and permanent? | Catch |
| --- | --- | --- | --- |
| Cloudflare Pages | Account-level Bulk Redirect. Source `<project>.pages.dev`, target `https://<custom domain>`, status 301, with preserve query string, subpath matching, preserve path suffix and include subdomains turned on | Yes. Bulk Redirects are on the Free plan with 10,000 URLs across lists. No request quota applies | Include subdomains also catches `<hash>.<project>.pages.dev` previews and branch aliases, so previews stop working unless that box is left off. Quotas are per account, shared with Sila |
| Cloudflare Workers | No documented equivalent. A Worker can check the host and 301, but only if `run_worker_first = true`, which bills every request | Free up to 100,000 requests a day | Past the daily limit, every request gets a 429, on the custom domain too. Whether Bulk Redirects accept a `workers.dev` source is undocumented |
| GitHub Pages | Automatic. Set the custom domain in the repo's Pages settings and `<user>.github.io` answers every path with a 301 | Yes, as long as the repo keeps the custom domain | The 301 points to `http://`, so a site with HTTPS enforced takes two hops. Setting a custom domain on the user site moves every project site of the account under it |
| Netlify | Domain-level rule in `_redirects`, such as `https://<site>.netlify.app/* https://<custom domain>/:splat 301!`. Netlify only auto-redirects apex and `www` | Free while credits last | Each request spends credits. At zero credits Netlify pauses every project and shows "Site not available", redirect included, until the next cycle |
| Vercel | `vercel.json` redirect with `has: [{ "type": "host", "value": "<project>.vercel.app" }]`, source `/:path*`, destination on the custom domain, `statusCode: 301` | Free within 1,000,000 CDN requests a month | Hobby is for personal, non-commercial use only. Default `permanent` gives 308, so set `statusCode` for a 301 |

Sources. Cloudflare's [redirect to custom domain guide](https://developers.cloudflare.com/pages/how-to/redirect-to-custom-domain/) gives the Bulk Redirect steps and parameters. The [redirects availability table](https://developers.cloudflare.com/rules/url-forwarding/) lists Bulk Redirects on Free with 15 rules, 5 lists and 10,000 URLs. Preview URL formats come from [Pages preview deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/). For Workers, [`run_worker_first`](https://developers.cloudflare.com/workers/static-assets/binding/) and the [billing page](https://developers.cloudflare.com/workers/static-assets/billing-and-limitations/) cover invocation billing and the 429, and the [limits page](https://developers.cloudflare.com/workers/platform/limits/) gives 100,000 requests a day. The [`workers.dev` docs](https://developers.cloudflare.com/workers/configuration/routing/workers-dev/) say nothing about redirecting it. For GitHub, the docs don't spell out the `github.io` redirect, so I checked it live on 2026-10-25. `https://mojombo.github.io/blog/x/?a=1` returned `301 Location: http://tom.preston-werner.com/blog/x/?a=1`, which then returned `301` to the `https://` URL. A project site behaved the same: `https://jekyll.github.io/jekyll/docs/?a=1` returned `301 Location: http://jekyllrb.com/docs/?a=1`. The project-site rule is in [About custom domains](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages). For Netlify, see [domain-level redirects](https://docs.netlify.com/routing/redirects/redirect-options/), [manage multiple domains](https://docs.netlify.com/manage/domains/manage-domains/manage-multiple-domains/) for the apex and `www` limit, and [how credits work](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/how-credits-work/) for the pause. For Vercel, the `has` host condition and `statusCode` are in the [vercel.json reference](https://vercel.com/docs/project-configuration/vercel-json), the request quota in the [Hobby plan](https://vercel.com/docs/plans/hobby).

## Comparison

### Cost, card and terms

| Host | Free limits that matter | Card | Use restrictions |
| --- | --- | --- | --- |
| Cloudflare Pages | 500 builds a month, one at a time, 20,000 files, 25 MiB per file, 100 custom domains per project. Static requests free. Functions share the Workers quota of 100,000 requests a day | Not asked. Sila already runs `sila-demo.pages.dev` on a free account with no card | None found for the Free plan |
| Cloudflare Workers | 100,000 script requests a day, 10 ms CPU per request, 20,000 asset files. Static asset requests are free and unlimited | Same account as above | None found |
| GitHub Pages | 1 GB site, soft 100 GB a month of bandwidth, soft 10 builds an hour unless built by Actions. Repo must be public on GitHub Free | No | Not for running an online business, commercial transactions or SaaS. No passwords or card numbers |
| Netlify | 300 credits a month, hard limit. A production deploy costs 15, 1 GB of bandwidth costs 20, 10,000 requests cost 2 | Netlify says "You'll never be charged for the Free plan". The pricing pages don't say a card is skipped at sign-up | Staff forum answers allow commercial use. I didn't find it in the terms |
| Vercel | 100 GB transfer, 1,000,000 CDN requests, 1,000,000 function invocations, 100 deployments a day. Over a limit, the feature waits 30 days | Docs ask for a card only when upgrading to Pro | Personal, non-commercial only. Commercial covers any financial gain for anyone involved, including advertising a product |

Sources are the [Pages limits](https://developers.cloudflare.com/pages/platform/limits/), [Workers limits](https://developers.cloudflare.com/workers/platform/limits/), [Workers static asset billing](https://developers.cloudflare.com/workers/static-assets/billing-and-limitations/), [ADR 0002](https://github.com/karim-belhadjali/islamic-bank/blob/main/docs/adr/0002-milestone-1-runs-local-first.md), [GitHub Pages limits](https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits), [creating a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site), the [Netlify credit plans](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/credit-based-pricing-plans/), the [Netlify forum on commercial use](https://answers.netlify.com/t/can-we-use-netlify-free-plan-for-commercial-purposes/41545), the [Vercel Hobby plan](https://vercel.com/docs/plans/hobby), [Vercel fair use](https://vercel.com/docs/limits/fair-use-guidelines#commercial-usage) and the [Vercel terms](https://vercel.com/legal/terms).

On Vercel, the hub promotes Sila and collects a waitlist so Karim can show investors a traction number. That reads as advertising a product for someone's financial gain. I wouldn't build on Hobby and hope nobody looks.

### The free address and its name

- **Cloudflare Pages** gives `<project>.pages.dev`, one label, so the project name is the address. Cloudflare's docs don't say what happens when the name is taken. A [third-party write-up](https://daleseo.com/cloudflare-pages/) says Cloudflare appends random characters. The docs don't describe a rename either, so treat the name as fixed at creation.
- **Cloudflare Workers** gives `<worker>.<account subdomain>.workers.dev`, two labels. The account subdomain can be changed in the dashboard, and the Worker name follows DNS label rules. See [`workers.dev` routing](https://developers.cloudflare.com/workers/configuration/routing/workers-dev/). The [migration guide](https://developers.cloudflare.com/workers/static-assets/migration-guides/migrate-from-pages/) confirms Workers use `workers.dev` where Pages used `pages.dev`.
- **GitHub Pages** gives `karim-belhadjali.github.io` for a user site, one per account, or `karim-belhadjali.github.io/hub` for a project site. The name is the GitHub username, so nobody can take it. See [About GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages).
- **Netlify** gives `<site>.netlify.app`, where the site name is editable in project settings. See [custom domains](https://docs.netlify.com/domains-https/custom-domains/).
- **Vercel** builds its URLs from the project name and the account slug, and shortens names that look like real domains. See [generated URLs](https://vercel.com/docs/deployments/generated-urls). The docs don't say what the production `vercel.app` name becomes when the plain project name is taken.

### Redirect and header files, and language at the root

The hub needs `/` to send French and Arabic browsers to their locale, from `Accept-Language`.

- **Cloudflare Pages** reads `_redirects` with up to 2,000 static and 100 dynamic rules and statuses 301, 302, 303, 307 and 308. Rules can't match on host, and `_redirects` can't match on language. `_headers` allows 100 rules. Language at `/` needs a Function. A file at `functions/index.js` serves `/` only, and the auto-generated `_routes.json` keeps every other path static and free. See [Pages redirects](https://developers.cloudflare.com/pages/configuration/redirects/), [Pages limits](https://developers.cloudflare.com/pages/platform/limits/) and [Functions routing](https://developers.cloudflare.com/pages/functions/routing/).
- **Cloudflare Workers** takes the same `_redirects` and `_headers` files natively, and lists language-based redirects as unsupported. Language at `/` needs Worker code with `run_worker_first = ["/"]`. See [Workers redirects](https://developers.cloudflare.com/workers/static-assets/redirects/) and [the binding docs](https://developers.cloudflare.com/workers/static-assets/binding/).
- **GitHub Pages** has no redirect or header file. Language can only be picked in the browser with JavaScript. ADR 0002 already rejected it for Sila because it can't serve an extensionless file as JSON.
- **Netlify** is the only host with language built into the redirect file, such as `/ /fr 302 Language=fr`. It matches only the first language the browser sends and ignores quality values. See [redirect options](https://docs.netlify.com/routing/redirects/redirect-options/).
- **Vercel** can match a redirect on any request header with a regex, so `accept-language` works in `vercel.json` without a function. See the [vercel.json reference](https://vercel.com/docs/project-configuration/vercel-json).

### Cookieless analytics

- **Cloudflare Web Analytics** is free and doesn't use cookies or localStorage. Pages turns it on in one click under Metrics. It also runs on any other host with a manual snippet, with a soft limit of ten sites per account. See [about Web Analytics](https://developers.cloudflare.com/web-analytics/about/), [Pages setup](https://developers.cloudflare.com/pages/how-to/web-analytics/), the [FAQ](https://developers.cloudflare.com/web-analytics/faq/) and the [GDPR FAQ](https://www.cloudflare.com/trust-hub/gdpr/).
- **Vercel Web Analytics** uses no third-party cookies and identifies visitors with a hash discarded after 24 hours. Hobby includes 50,000 events a month and a one-month window. Past the limit, collection stops after a three-day grace and resumes after seven days. See [privacy](https://vercel.com/docs/analytics/privacy-policy) and [limits](https://vercel.com/docs/analytics/limits-and-pricing).
- **GitHub Pages and Netlify** have nothing free and built in for page views. Netlify's [pricing](https://www.netlify.com/pricing/) lists analytics only on paid plans. Either could use Cloudflare's snippet.

### Functions for a waitlist proxy

- **Cloudflare Pages** has Functions, billed as Workers requests, 100,000 a day free. See [Pages limits](https://developers.cloudflare.com/pages/platform/limits/).
- **Cloudflare Workers** gives the same 100,000 a day with 10 ms of CPU each. See [Workers limits](https://developers.cloudflare.com/workers/platform/limits/).
- **GitHub Pages** has none. The form would have to post straight to the email service.
- **Netlify** includes Functions on Free, paid for in credits at 10 per GB-hour plus the request cost. Form submissions are free. See the [credit plans](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/credit-based-pricing-plans/).
- **Vercel** gives 1,000,000 invocations and 4 active CPU hours a month on Hobby. See [Hobby](https://vercel.com/docs/plans/hobby).

### Custom domain setup

- **Cloudflare Pages** takes a subdomain with DNS anywhere, through a CNAME to `<project>.pages.dev`, added in the dashboard first or it fails with a 522. The apex needs the domain added as a Cloudflare zone with Cloudflare nameservers. See [Pages custom domains](https://developers.cloudflare.com/pages/configuration/custom-domains/).
- **Cloudflare Workers** needs Cloudflare nameservers for any custom domain, apex or not. TLS is issued automatically. See [Workers custom domains](https://developers.cloudflare.com/workers/configuration/routing/custom-domains/) and the [migration guide](https://developers.cloudflare.com/workers/static-assets/migration-guides/migrate-from-pages/).
- **GitHub Pages** takes a CNAME to `<user>.github.io`, with apex and `www` redirecting to each other automatically when both are set. See [About custom domains](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages).
- **Netlify** takes the apex through an ALIAS to `apex-loadbalancer.netlify.com` or an A record to `75.2.60.5`, and recommends `www` as primary with outside DNS. See [custom domains](https://docs.netlify.com/domains-https/custom-domains/).
- **Vercel** takes an A record for the apex or a CNAME for `www`, and recommends `www` as primary. Domain redirects are set in project settings. See [deploying and redirecting](https://vercel.com/docs/domains/working-with-domains/deploying-and-redirecting).

### Pages and Workers today

The [Pages docs](https://developers.cloudflare.com/pages/) open with "Start new projects with Workers" and call Workers "Cloudflare's primary platform". They don't say Pages is deprecated. Cloudflare's [changelog](https://developers.cloudflare.com/changelog/?product=pages) shows no Pages retirement notice as of 2026-10-25. The [migration guide](https://developers.cloudflare.com/workers/static-assets/migration-guides/migrate-from-pages/) moves a project to Workers, but the new site lives on `workers.dev` and the guide says nothing about what becomes of the old `pages.dev` name. So the risk is real but slow. If Cloudflare ever retires `pages.dev`, links posted to it break whatever the hub runs on by then. Buying the domain early is the only fix for that.

## Open points for issue 9

1. **Project name.** On Pages the project name is the address and can't be changed later. `karimbelhadjali.pages.dev` matches the planned domain. `karim.pages.dev` is shorter but may already be taken. Check availability in the dashboard before creating.
2. **Shared account with Sila.** Same account means one Bulk Redirect quota and one Web Analytics site list. The hub's redirect rule must name only the hub's hostname so it never touches `sila-demo.pages.dev`, where Google fetches `assetlinks.json`.
3. **Previews after the move.** Decide whether the Bulk Redirect includes subdomains. With it on, preview and branch URLs redirect too, which breaks the French and Arabic review builds. With it off, only the production address moves.
4. **Pages or Workers.** Pages wins on the redirect and the shorter name. If Karim wants Workers anyway, test before launch whether a Bulk Redirect accepts a `workers.dev` source. Otherwise the redirect costs Worker invocations forever.
5. **Apex or `www`.** The apex on Pages needs the domain on Cloudflare nameservers. That's free and needs no card, but it ties DNS to Cloudflare, which [Register karimbelhadjali.com](https://github.com/karim-belhadjali/islamic-bank/issues/18) should know about.

## Not verified

- Whether Bulk Redirects work with a `workers.dev` hostname as the source.
- What Cloudflare does when a Pages project name is taken, beyond one third-party post.
- Whether Netlify or Vercel ask for a card at sign-up. Docs only say the free plans never charge.
- Whether Vercel's domain settings can redirect the auto-assigned `vercel.app` domain without `vercel.json`.
- Netlify's free-plan commercial use, which rests on staff forum answers, not the terms.
