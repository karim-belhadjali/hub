# What the domain move breaks

Research for [Research: what the domain move breaks](https://github.com/karim-belhadjali/hub/issues/5), feeding [Plan the domain move](https://github.com/karim-belhadjali/hub/issues/15). Sources read on 2026-10-25.

## Short answer

Almost nothing breaks if the free address keeps answering forever with a per-path 301 to the same path on `karimbelhadjali.com`. That one redirect carries every LinkedIn post, every email already sent, the GitHub link and Google's ranking signals. The free address is a permanent dependency from the first post onward, so it must never be deleted, renamed or pointed anywhere else.

What the redirect can't fix is small and known in advance. LinkedIn preview cards on old posts keep the old address forever. A saved locale choice in the browser is lost. A Search Console property on the free address has to exist before the move or the Change of Address tool can't be used. None of these cost more than an hour if the site builds every absolute URL from one config value and puts the locale in the path.

Sila's passkeys don't depend on the hub's address at all. The RP ID move to `sila.karimbelhadjali.com` depends only on owning the domain, and it can happen before, with or after the hub's move. The one shared risk is a zone-wide redirect rule on `karimbelhadjali.com` catching `sila.karimbelhadjali.com/.well-known/assetlinks.json`, which would break Android passkeys.

## Posted links

**What breaks.** Old LinkedIn posts keep their old preview card and their old link for good. LinkedIn's help center says only the text of a post can be edited, and shared rich media such as an article link "can't be replaced". New URLs added by editing won't get a new preview image. Changing the card means deleting the post and posting again, which throws away its reactions and comments. [LinkedIn: Edit a shared post](https://www.linkedin.com/help/linkedin/answer/a522811)

Post Inspector doesn't rescue old posts either. It refreshes what LinkedIn holds about a URL, but changes "will only affect the URL's preview image and information for new posts". "The preview for any existing posts with this URL will remain the same." [LinkedIn: Use Post Inspector to refresh URL](https://www.linkedin.com/help/linkedin/answer/a6233775)

LinkedIn caches previews on its own schedule. Its troubleshooting page says to allow 48 hours after the last share before trying again, and the edit page says the share box remembers a link for three days. [LinkedIn: Troubleshooting issues sharing URLs](https://www.linkedin.com/help/linkedin/answer/a525063), [LinkedIn: Edit a shared post](https://www.linkedin.com/help/linkedin/answer/a522811)

LinkedIn shortens any link longer than 26 characters once you click Post. [LinkedIn: Short URLs in shared posts](https://www.linkedin.com/help/lms/answer/a521889) A free address with a path will almost always pass 26 characters, so posted links become `lnkd.in` links that lead to the free address. LinkedIn doesn't document whether those short links ever expire or can be repointed. Treat them as fixed pointers to the old URL.

**What still works.** A click on an old card or short link reaches the free address, and a server-side 301 takes the reader to the same page on the new domain. That's the whole mechanism, and it only works while the free address is alive.

**Cost to avoid.**

- Keep the free address redirecting every path to the same path, for good. Cost is zero on a free host, if ticket 3 confirms the mechanism is free and permanent.
- Accept that old cards show the old address. The card text and image still describe the right page, so this is cosmetic.
- Don't delete and repost old posts. It costs engagement and gains a different address in a card nobody reads closely.
- Before the first post after the move, run Post Inspector on each new URL so new shares get a fresh card.
- The only way to avoid the dependency entirely is to buy the domain before the first episode links anywhere. [Register karimbelhadjali.com](https://github.com/karim-belhadjali/islamic-bank/issues/18) already says the domain gates episode 0. If that holds, the hub may have almost no LinkedIn links on the free address at all, and this section shrinks to a footnote.

## Search

Google's site-move guide covers this case directly. The steps for a small site:

- Use server-side permanent redirects, 301 or 308, from each old URL to its matching new URL. Don't send old URLs to the new home page. Keep chains short. [Google: Site moves with URL changes](https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes)
- Keep the redirects "for as long as possible, generally at least 1 year", and consider keeping them indefinitely. [Google: Site moves with URL changes](https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes)
- Each new page carries a self-referencing canonical. Submit a sitemap of the new URLs. Update hreflang annotations to the new URLs. Update internal links. Expect temporary ranking fluctuation. Permanent redirects don't lose PageRank. [Google: Site moves with URL changes](https://developers.google.com/search/docs/crawling-indexing/site-move-with-url-changes)
- hreflang needs fully qualified URLs, and every locale version must list itself and all the others or Google ignores the set. [Google: Tell Google about localized versions of your page](https://developers.google.com/search/docs/specialty/international/localized-versions) With English, French and Arabic, that's nine absolute URLs per page that all change at the move.
- Submit a Change of Address in Search Console. You must own both the old and new properties under one Google account. It needs a 301 from the old home page to the new one and runs pre-move checks. Its effect lasts 180 days. [Google: Change of Address tool](https://support.google.com/webmasters/answer/9370220)

**The trap.** A free host subdomain can't be a Domain property in Search Console, because that needs a DNS record and the host owns the DNS. It can be a URL-prefix property verified by an HTML file or tag. [Google: Verify your site ownership](https://support.google.com/webmasters/answer/9008080) The Change of Address tool accepts root-level properties such as `http://example.com`, but not path-level ones. [Google: Change of Address tool](https://support.google.com/webmasters/answer/9370220) My inference: once every path on the free address 301s, the verification file and tag no longer load from it. So the free-address property has to be verified before the redirect goes live.

**Cost to avoid.** Low, if done from day one.

- Generate canonical, hreflang, sitemap and `og:url` values from a single site-URL setting, so the move is one config change and a rebuild.
- Verify the free address as a URL-prefix property in Search Console at launch.
- At the move, verify the new domain, ship the redirect, submit the new sitemap, then file the Change of Address.

For a new personal site with little ranking to lose, this is cheap insurance, not a rescue.

## Waitlist

**Form endpoint.** If the signup form posts to the email service's own endpoint, the move doesn't touch it. If it posts to a function on the hub's own address, an old cached page posting to the free address hits the redirect. Browsers turn a POST into a GET on a 301 or 302 and drop the body. [Fetch Standard: HTTP-redirect fetch](https://fetch.spec.whatwg.org/#http-redirect-fetch) RFC 9110 names 308 as the fix when that's unwanted. [RFC 9110 §15.4.2](https://www.rfc-editor.org/rfc/rfc9110#section-15.4.2) So a 301 on the form endpoint silently loses signups. Either post straight to the email service, or have the redirect answer 308 for the form path. Cheapest is posting to the service.

**Links inside emails already sent.** Links to hub pages point at the free address and ride the same 301 as LinkedIn links. Unsubscribe and preference links are hosted by the email service, so the hub's move doesn't affect them. Ticket 4 has to confirm that for the chosen service.

**Sender address.** Before the domain, the service sends from its own shared domain. After the move, sending from `karimbelhadjali.com` needs SPF, DKIM and DMARC. Google requires SPF or DKIM from every sender, and all three from bulk senders. [Google: Email sender guidelines](https://support.google.com/a/answer/81126) The setup details belong to ticket 4. The cross-cutting point is ordering. The sender switch is a separate step from the web move, and it can wait until the DNS records verify.

**Consent records.** Under GDPR the controller must be able to show that each person consented. [GDPR Article 7, paragraph 1](https://eur-lex.europa.eu/eli/reg/2016/679/oj) The controller is Karim and the purpose doesn't change, so a new address doesn't void consent. What the plan should keep is proof of what people agreed to. Archive the form wording and privacy notice as they stood on the free address, with dates, before the move. If the service stores a signup source URL per subscriber, old records will show the free address. That's accurate history, not a defect.

**Cost to avoid.** Near zero. Post the form to the email service, archive the consent wording, and switch the sender after DNS checks pass.

## Browser state

`localStorage` belongs to one origin: "Each site has its own separate storage area." [HTML Standard: Web storage](https://html.spec.whatwg.org/multipage/webstorage.html#the-localstorage-attribute) Anything the hub saves on the free address, such as a chosen locale, is invisible on `karimbelhadjali.com`. Cookies on the free host don't follow either. A server-side redirect can't read `localStorage`, so the value can't be carried across.

It matters only if the locale lives in browser storage alone. If the locale is in the path, like `/fr/...`, the 301 preserves the path and the reader lands in the same language. Only someone typing the bare root loses the saved choice and gets the default or Accept-Language pick once. That's ticket 11's call, and this is one more reason to put the locale in the URL.

A service worker would be worse. The Service Workers spec hands navigation requests inside a worker's scope to that worker's fetch handler. [Service Workers: Handle Fetch](https://w3c.github.io/ServiceWorker/#handle-fetch) A worker on the free address could keep serving cached old pages to returning visitors and never show them the redirect. Hub v1 should ship without one.

301 and 308 responses are heuristically cacheable. [RFC 9110 §15.4.2](https://www.rfc-editor.org/rfc/rfc9110#section-15.4.2), [RFC 9110 §15.4.9](https://www.rfc-editor.org/rfc/rfc9110#section-15.4.9) Browsers may remember a wrong target for a long time, so test the redirect map before it goes live.

**Cost to avoid.** None beyond the locale-in-path and no-service-worker choices.

## Sila ties

**What depends only on the domain.** The customer RP ID `sila.karimbelhadjali.com` and everything bound to it.

- A WebAuthn RP ID must be equal to the caller origin's effective domain or a registrable suffix of it. [WebAuthn Level 3: RP ID](https://www.w3.org/TR/webauthn-3/#rp-id) Credentials are scoped to the RP ID. A passkey made for `sila-demo.pages.dev` will never work for `sila.karimbelhadjali.com`, which is why ADR 0002 re-registers test passkeys at the switch.
- Android checks Digital Asset Links on the RP ID's own host at `/.well-known/assetlinks.json`. It needs HTTP 200 and `Content-Type: application/json`, and "verification fails if the response has a 301 or 302 HTTP redirect". [Android: Credential Manager prerequisites](https://developer.android.com/identity/credential-manager/prerequisites)
- The web build's related-origins file sits at `https://<rp id>/.well-known/webauthn`, served over HTTPS as `application/json` with an `origins` array. Clients must honour at least five registrable origin labels. [WebAuthn Level 3 §5.11](https://www.w3.org/TR/webauthn-3/#sctn-related-origins)
- `pages.dev` is on the Public Suffix List, so `sila-demo.pages.dev` is its own registrable domain. [Public Suffix List](https://publicsuffix.org/list/public_suffix_list.dat)

So the move needs DNS for `sila`, a static host serving the two files, the `ServerDomain` switch and re-registration, as [issue 18](https://github.com/karim-belhadjali/islamic-bank/issues/18) lists. The staff console stays on `console.localhost` until the bank is deployed, and the domain move doesn't touch it.

**What depends on the hub's address.** Only links. The hub's Sila project page URL, the waitlist form on it, episode descriptions that point to the hub, and any Sila README or ticket that links to the hub page. All of these ride the hub's 301. No passkey, RP ID or well-known file depends on where the hub lives.

**Does the hub on a free address constrain the RP ID move?** No. `sila.karimbelhadjali.com` is not a suffix of the hub's free address, nor of the apex `karimbelhadjali.com`, so neither host matters to the RP ID. The RP ID can move the day the domain is bought, while the hub is still on the free address. The two moves can be planned separately.

**Two hazards where they meet.**

- Once the hub and `sila` share the `karimbelhadjali.com` zone, a zone-wide redirect rule for the hub could catch `sila.karimbelhadjali.com/.well-known/assetlinks.json`. Examples are a www-to-apex rule written with a wildcard or a "redirect all subdomains" rule. A 301 there breaks Android passkeys silently. Every hub redirect must be scoped to the hub's own hostnames. Google also has to be able to fetch the file. [Android: Credential Manager prerequisites](https://developer.android.com/identity/credential-manager/prerequisites)
- The hub's redirect off its free address must never list `sila-demo.pages.dev`. Cloudflare's documented pages.dev redirect uses a Bulk Redirects list with an "include subdomains" option. [Cloudflare: Redirect a pages.dev address to a custom domain](https://developers.cloudflare.com/pages/how-to/redirect-to-custom-domain/) That's harmless while the hub and Sila are separate Pages projects, but the list should name the hub's project and nothing else. Keep `sila-demo.pages.dev` serving its files until the Sila-side switch and re-registration are done. After that it can stay as is or be retired.

**Cost to avoid.** None, as long as the Sila-side ticket and the hub's redirect rules are written with these two rules in them.

## Other services

**Open Graph.** `og:url` is "the canonical URL of your object that will be used as its permanent ID in the graph". [The Open Graph protocol](https://ogp.me/) The site has to emit the new domain in `og:url` and `og:image` after the move. Building them from the same site-URL setting as the canonical makes that free. Old LinkedIn cards keep the old values regardless, as covered above.

**Analytics.** Cloudflare Web Analytics sets up sites by hostname. On a Pages project it is a one-click switch that injects the script on the next deploy. [Cloudflare Web Analytics: Get started](https://developers.cloudflare.com/web-analytics/get-started/) The docs don't say whether one site entry covers both the pages.dev address and a custom domain. Expect page views to split by hostname at the move, and plan to read them side by side or accept a seam on move day. Ticket 3 picks the analytics tool and should answer this for it. The traction number lives in the email service, so the move doesn't touch it.

**GitHub.** The profile's website field and social links are plain fields edited in settings, or through the REST API. [GitHub: Personalize your profile](https://docs.github.com/en/account-and-profile/tutorials/personalize-your-profile) The profile README is a commit. Both take minutes, and the 301 covers anyone who clicked before the edit.

## Open points for "Plan the domain move"

1. **Buy the domain before episode 0?** If the domain lands before the first episode links to the hub, most of this file stops mattering. Decide whether episode 0 waits for it, as issue 18 implies, or ships on the free address.
2. **Order of steps.** A sensible order: verify the free address in Search Console at launch. Buy the domain. Point `sila` at the well-known files and switch the RP ID. Attach the custom domain to the hub and verify it. Turn on the free-address redirect. File the Change of Address. Switch the email sender once DNS checks pass. Update GitHub and run Post Inspector on the new URLs. Then check every posted link.
3. **Redirect status per path.** 301 for pages. 308, or no hub-hosted endpoint at all, for the form path.
4. **Redirect lifetime.** Forever is the answer. The plan should record that the free-address project is never deleted, and who checks it still answers.
5. **Single site-URL setting.** Confirm the spec for hub v1 builds canonical, hreflang, sitemap and Open Graph URLs from one value. This belongs in [Write the spec for hub v1](https://github.com/karim-belhadjali/hub/issues/14).
6. **Locale in the path** so redirects carry it, decided in [Choose the URL and locale scheme](https://github.com/karim-belhadjali/hub/issues/11).
7. **No service worker in hub v1.**
8. **Consent archive.** Snapshot the form wording and privacy notice before the move.
9. **Zone rules.** Scope every hub redirect to the hub's hostnames. Add a check that `https://sila.karimbelhadjali.com/.well-known/assetlinks.json` returns 200 JSON after any zone change.
10. **Sila-side ticket.** Create it in the Sila repo, blocked by issue 18. It covers DNS for `sila`, the two well-known files, the `ServerDomain` switch, re-registering test passkeys, keeping `sila-demo.pages.dev` up until then, and the zone-rule check above.
11. **Link check after the move.** List every URL posted on LinkedIn and in emails, and confirm each returns one 301 to the matching new page.

### What the plan needs from tickets 3 and 4

- **From [ticket 3](https://github.com/karim-belhadjali/hub/issues/3).** The exact free mechanism for a per-path 301 from the free address that preserves path and query. Whether it can answer 308 for one path. Whether it stays free with no card for good. Whether preview deployment hostnames are caught. Whether analytics continue across hostnames.
- **From [ticket 4](https://github.com/karim-belhadjali/hub/issues/4).** Whether the signup form can post straight to the service. Whether unsubscribe links are service-hosted. The DNS records the custom sender needs. Whether consent records store a source URL and can be exported.

## Not verified

- Whether LinkedIn's preview crawler and Post Inspector follow a 301 to build a card from the target page. LinkedIn's help pages don't say. This only affects new shares of old URLs, which the plan should avoid anyway.
- Whether `lnkd.in` short links expire, and whether editing a post's text re-shortens a changed URL. Not documented.
- LinkedIn's actual cache lifetime. Its own pages give 48 hours and three days in different places. Third-party guides say seven days.
- Whether Bulk Redirects stays free on Cloudflare's free plan, and how it treats preview deployment hostnames. That is ticket 3's to confirm.
- Whether Cloudflare Web Analytics can count two hostnames under one site.
