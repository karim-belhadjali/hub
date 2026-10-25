# Email service for the waitlist

Research for [#4][issue-4], feeding the decision in [#10][issue-10]. Sources checked on 2026-10-25. Free tiers move often, so re-check the numbers below before signing up.

## Short answer

Use Brevo's free plan. It is the only option that clears every hard constraint at once. It asks for no card, ever. It stores up to 100,000 contacts. It hosts data in the EU. Its signup forms do double opt-in on the free plan, take a Cloudflare Turnstile captcha, and switch to right-to-left for Arabic. Before the domain move it still delivers the confirmation email, because it swaps a Gmail sender for an address on its own authenticated domain.

The price is real. Brevo caps the free plan at 300 emails a day, stamps "Sent with Brevo" on every email, and has no tags. A waitlist becomes a Brevo list or an attribute, which #10 has to settle against the glossary.

Kit is the fallback. Tags are native, the free plan holds 10,000 subscribers and includes the API. Kit has no sender of its own to fall back on, though, so before the domain it sends as Karim's Gmail address, which Google tells bulk senders not to do.

Drop the rest. Buttondown charges for tags and stops at 100 subscribers. MailerLite stops at 250. Loops can't send a single email without a domain. beehiiv can't send its confirmation email in Arabic. A self-built list on Cloudflare can collect but has no free way to send.

## The constraint that decides it

No domain means every option fails or bends on the same point: who signs the email. Before the domain move the waitlist sends exactly one kind of email, the double opt-in confirmation. So the real question is whether a service can deliver that confirmation with no domain Karim controls.

- Gmail publishes `p=none; sp=quarantine` for DMARC today and Yahoo publishes `p=reject`, read from DNS on 2026-10-25 with `nslookup -type=TXT _dmarc.gmail.com` and `_dmarc.yahoo.com`. A Yahoo sender address will bounce at any provider. A Gmail one survives for now.
- Google's sender guidelines say "Don't impersonate Gmail From: headers" and warn that Gmail will move to a quarantine policy. [Google sender guidelines][google-senders]
- A free host subdomain such as `*.pages.dev` doesn't help. Loops says free hosting subdomains "cannot be used as sending domains" because you can't add DNS records to them. [Loops sending domain][loops-domain]

Services that send from their own authenticated domain get past this: Brevo, beehiiv, Buttondown and, with a contradiction in its docs, MailerLite. Services that send from your address don't: Kit, Loops, and any self-built path.

## Comparison

| | Brevo | Kit | beehiiv | MailerLite | Buttondown | Loops | Self-built on Cloudflare |
|---|---|---|---|---|---|---|---|
| Free cap | 100,000 contacts | 10,000 subscribers | 2,500 subscribers | 250 subscribers | 100 subscribers | 1,000 subscribed contacts | 5 GB in D1 |
| Sends on free | 300 a day | unlimited broadcasts | unlimited | 2,500 a month | about one email a day to the list | 4,000 per rolling 30 days | none, see below |
| Card | never | not stated for Free | never | not required | not stated | not required | not required for Workers Free |
| Branding | "Sent with Brevo" | Kit branding, removal is paid | beehiiv branding | removal is paid | removal is a $79 add-on | "Powered by Loops" footer | none |
| Sender with no domain | rewritten to a `brevosend.com` address | your own address, Gmail discouraged | beehiiv subdomain | auto subdomain, docs disagree | `username@buttondown.email` | not possible | not possible |
| Tags | none, lists and attributes | native tags | 3 segments, custom fields | unlimited groups | $9 a month add-on | mailing lists, properties | whatever you build |
| Locale field | custom attribute | custom field | custom field | not confirmed | not checked | custom property | a column |
| Double opt-in | yes, on free, template per form | yes, per form | yes, one language per publication | not confirmed | not checked | not found in docs | needs sending |
| Arabic | RTL forms, email editor not confirmed | not documented | not supported for system emails | not confirmed | not checked | not checked | you write it |
| Data location | EU | US and other countries | US | not checked | not checked | not checked | your Cloudflare account |

Sources for each cell are in the sections below.

## Options

### Brevo

**Free plan.** No time limit, 300 sends a day that don't roll over, 100,000 contacts, and the "Sent with Brevo" sticker can't be removed on Free. [Brevo free plan FAQ][brevo-free] The pricing page says "Free forever, no credit card needed" and that sending starts "once we approve your account for sending". [Brevo pricing][brevo-pricing] Paid plans take a card through Adyen or PayPal, which matters if Karim ever pays before he has a card. [Brevo pricing][brevo-pricing]

**Sender before the domain.** Brevo replaces a free address such as `mycompany@gmail.com` with `mycompany@5000001.brevosend.com` for marketing email. Brevo calls this "a stopgap measure to maintain basic deliverability, not a permanent solution" and warns recipients may not recognise the address. [Brevo sender requirements][brevo-senders] That stopgap is what makes the waitlist work before the domain move, so it's acceptable for a few months, not for a launch.

**Signup from a static site.** Forms embed as an iframe, an HTML snippet or a simple HTML form. Fields map to contact attributes, including multiple-choice ones. The form takes Google reCAPTCHA or Cloudflare Turnstile, and its writing direction can be set to right to left. [Brevo signup form][brevo-form] Double opt-in works on every plan, adds a `DOUBLE_OPT-IN` attribute, and lets each form pick its own confirmation template, so one form per locale gives a French and an Arabic confirmation. [Brevo double opt-in][brevo-doi] [Brevo signup form][brevo-form]

**Tags and counting.** Brevo has lists and attributes, no tags. A form adds contacts to the lists you choose. The list details endpoint returns `totalSubscribers` and `uniqueSubscribers`. [Brevo API, get a list][brevo-list-api] Contacts export to CSV from a list or a segment. [Brevo export][brevo-export] The free plan's exclusions are A/B tests, dedicated IPs, landing pages, popups, push, WhatsApp and sales workflows. The API isn't on that list. [Brevo free plan FAQ][brevo-free]

**GDPR.** Brevo processes and stores its databases inside the EU, on OVH in France and Germany and on Google Cloud in Belgium. [Brevo data location][brevo-location] The double opt-in article recommends copying the form's consent text into the confirmation template, since that is the only record of which wording a subscriber agreed to. [Brevo signup form][brevo-form]

**Weak spots.** 300 a day means a launch email to 3,000 Sila subscribers takes ten days on Free. The account needs sending approval, and I couldn't find what Brevo checks.

### Kit

**Free plan.** Free for as long as you stay under 10,000 subscribers, with unlimited broadcasts, unlimited forms and landing pages, tagging and segmentation, and API access. Sequences, visual automations and rules are paid. [Kit Free plan][kit-free] The pricing page shows "Remove Kit branding" only on paid plans and says "No credit card required" only for the paid trials. [Kit pricing][kit-pricing]

**Sender before the domain.** Kit sends from the address you confirm. "By default, your friendly-from address will be your sending address". [Kit domain verification][kit-verify] Kit says "All marketing emails should be sent from a domain you own, not from a personal account". [Kit own domain][kit-own-domain] Kit documents no fallback sender of its own, so before the move it means Karim's Gmail address and the risk Google describes above.

**Signup and tags.** Each form or landing page has its own confirmation email, and double opt-in stays on unless you tick auto-confirm. [Kit confirmation email][kit-confirm] A form field saved as a tag applies a tag from the subscriber's choice. [Kit tag fields][kit-tag-fields] I found no form setting that applies a fixed tag to every signup, and rules, which would, are paid. A Cloudflare Worker calling the API can tag instead. The V4 API keys work on any plan, but Kit scopes them to "individual use". [Kit API overview][kit-api]

**GDPR.** Kit stores data "on servers located in the United States and other countries" and is processor for subscriber data under its DPA, with SCCs included. [Kit privacy policy][kit-privacy] [Kit DPA][kit-dpa]

**Arabic.** Nothing in Kit's help centre covers right-to-left email.

### beehiiv

No card, ever, and up to 2,500 subscribers on Free. [beehiiv pricing][beehiiv-pricing] The October 2026 rename turned Launch into Free with the same cap. [beehiiv 2026 plans][beehiiv-2026] Free includes custom fields, three segments and the API except the Send API. [beehiiv pricing][beehiiv-pricing] Creating an API key requires Stripe Identity verification. [beehiiv API keys][beehiiv-api-keys] The create subscription endpoint takes `custom_fields` and a `double_opt_override`, but no tags. [beehiiv create subscription][beehiiv-create]

The deal breaker is language. A publication's subscriber-facing language, which covers double opt-in confirmations and unsubscribe pages, is one of English, Spanish, French, German, Italian or Portuguese. [beehiiv language][beehiiv-language] Free allows one publication, so Arabic subscribers would confirm in English or French.

### MailerLite

Up to 250 subscribers and 2,500 emails a month, no card. Above 250 active subscribers, sending locks. Groups, which work as tags, and segments are unlimited. API access on Free doesn't include sending. [MailerLite pricing][mailerlite-pricing] Its docs disagree about domains. One page says MailerLite gives users without a domain a personal subdomain. [MailerLite free domain][mailerlite-free-domain] Another says that "after the trial ends, you will need to authenticate your sending domain". [MailerLite account][mailerlite-account] The 250 cap rules it out regardless.

### Buttondown

Free stops at 100 subscribers. Tagging and segmentation is a $9 a month add-on. [Buttondown pricing][buttondown-pricing] It sends from `username@buttondown.email` when there is no custom domain. [Buttondown sender address][buttondown-sender] A paid add-on for tags breaks the no-spending rule.

### Loops

No card, 1,000 subscribed contacts, 4,000 sends per rolling 30 days, a "Powered by Loops" footer. [Loops pricing][loops-pricing] The form endpoint is the cleanest static-site option on this list, a plain HTML `POST` that takes mailing lists and custom properties. [Loops form endpoint][loops-form] It can't send anything until Karim owns a domain. [Loops sending domain][loops-domain]

### Self-built on Cloudflare

Collecting is easy and free. D1 on Workers Free gives 5 million rows read and 100,000 written a day, with 5 GB of storage. [D1 pricing][d1-pricing] Sending is the wall. Cloudflare Email Service sending is "Not available" on Workers Free, and sending to arbitrary recipients needs Workers Paid. [Cloudflare Email Service pricing][cf-email-pricing] Resend's free plan needs a verified domain you own. [Resend domains][resend-domains] No confirmation email means no double opt-in, and the per-tag count becomes a SQL query instead of a dashboard number.

## What changes at the domain move

- **Sender authentication.** Brevo needs its Brevo code, DKIM and a DMARC record that includes Brevo's `rua` address, then the sender stops being rewritten. [Brevo sender requirements][brevo-senders] Kit verifies a sending domain with SPF, DKIM and DMARC records. [Kit domain verification][kit-verify] Either way, it's a handful of DNS records on Cloudflare.
- **Form endpoints.** Brevo and Kit forms post to the provider's own hosts, so the move leaves them alone. Only the success and confirmation redirect URLs, which point at the hub, need updating. That's my reading of how embeds work, not a documented statement.
- **Links in emails already sent.** Those links point at the free address. They keep working only if the free address keeps serving or redirecting after the move, which is the map's existing rule for posted links, so it applies to emails too.

## Open points for "Choose the email service"

1. **Tag or list.** Brevo has no tags. Either a waitlist is a Brevo list, which contradicts the glossary line "never a list of its own", or it's a multiple-choice attribute plus a segment. The list route has a documented count. The attribute route keeps the glossary true. Pick one and amend the glossary if it's the list.
2. **Where the project tag comes from.** One Brevo form per project and locale, fixed to a list, means no code. A Worker calling the API means one form and a hidden field. The form route fits a static site with no backend.
3. **Locale field.** A `LOCALE` category attribute with `en`, `fr`, `ar`, set by the form, not chosen by the visitor.
4. **Double opt-in.** On, with one confirmation template per locale and the consent text pasted into each.
5. **Traction number.** Read from the list or segment count in the dashboard. Decide whether investors see unconfirmed signups or only confirmed ones.
6. **Brevo approval.** Sign up first and check that Brevo approves a sender with no website or domain. If it doesn't, Kit with the Gmail sender is the fallback.
7. **300 a day.** Fine for confirmations. Revisit before the first launch email, which is a reason to buy the domain before Sila's launch, not after.

## Not verified

- What Brevo's sending approval checks.
- Whether Brevo's email editor, as opposed to its forms, supports right to left.
- Whether Kit's Free plan ever asks for a card.
- Whether Brevo's dashboard shows a count on a segment, as it does on a list.
- MailerLite's free-plan domain rule, given its two articles disagree.

## Sources

[issue-4]: https://github.com/karim-belhadjali/hub/issues/4
[issue-10]: https://github.com/karim-belhadjali/hub/issues/10
[google-senders]: https://support.google.com/a/answer/81126
[brevo-pricing]: https://www.brevo.com/pricing/
[brevo-free]: https://help.brevo.com/hc/en-us/articles/208580669-FAQs-What-are-the-limits-of-the-Free-plan
[brevo-senders]: https://help.brevo.com/hc/en-us/articles/14925263522578-Comply-with-Gmail-Yahoo-and-Microsoft-s-requirements-for-email-senders
[brevo-form]: https://help.brevo.com/hc/en-us/articles/208771869-Create-a-sign-up-form-in-Brevo
[brevo-doi]: https://help.brevo.com/hc/en-us/articles/208733449-Why-use-a-double-opt-in-subscription-form
[brevo-list-api]: https://developers.brevo.com/reference/getlist-1
[brevo-export]: https://help.brevo.com/hc/en-us/articles/5310065850642-Export-your-contacts-from-Brevo
[brevo-location]: https://help.brevo.com/hc/en-us/articles/360001005510-Data-storage-location
[kit-pricing]: https://kit.com/pricing
[kit-free]: https://help.kit.com/en/articles/16627071-the-kit-free-plan
[kit-verify]: https://help.kit.com/en/articles/2502558-verify-your-domain-to-optimize-your-deliverability
[kit-own-domain]: https://help.kit.com/en/articles/2502505-why-do-i-need-my-own-domain-to-send-email
[kit-confirm]: https://help.kit.com/en/articles/2502655-the-confirmation-email
[kit-tag-fields]: https://help.kit.com/en/articles/3317488-adding-tag-dropdowns-checkboxes-to-kit-forms
[kit-api]: https://help.kit.com/en/articles/9902901-kit-api-overview
[kit-privacy]: https://kit.com/privacy
[kit-dpa]: https://kit.com/dpa
[beehiiv-pricing]: https://www.beehiiv.com/pricing
[beehiiv-2026]: https://www.beehiiv.com/blog/beehiiv-pricing-2026
[beehiiv-api-keys]: https://www.beehiiv.com/support/article/13091918395799-how-to-access-your-publication-id-or-api-keys
[beehiiv-create]: https://developers.beehiiv.com/api-reference/subscriptions/create
[beehiiv-language]: https://www.beehiiv.com/support/article/4993791529751-setting-a-default-language-for-your-publications-subscriber-facing-content
[mailerlite-pricing]: https://www.mailerlite.com/pricing
[mailerlite-free-domain]: https://www.mailerlite.com/help/the-limitations-of-using-a-free-domain
[mailerlite-account]: https://www.mailerlite.com/help/how-to-get-approved
[buttondown-pricing]: https://buttondown.com/pricing
[buttondown-sender]: https://buttondown.com/blog/sending-email-address
[loops-pricing]: https://loops.so/pricing
[loops-form]: https://loops.so/docs/forms/custom-form
[loops-domain]: https://loops.so/docs/sending-domain
[d1-pricing]: https://developers.cloudflare.com/d1/platform/pricing/
[cf-email-pricing]: https://developers.cloudflare.com/email-service/platform/pricing/
[resend-domains]: https://resend.com/docs/dashboard/domains/introduction

- Google, Email sender guidelines: <https://support.google.com/a/answer/81126>
- Brevo: [pricing][brevo-pricing], [free plan FAQ][brevo-free], [sender requirements][brevo-senders], [signup form][brevo-form], [double opt-in][brevo-doi], [get a list API][brevo-list-api], [export][brevo-export], [data location][brevo-location]
- Kit: [pricing][kit-pricing], [Free plan][kit-free], [domain verification][kit-verify], [own domain][kit-own-domain], [confirmation email][kit-confirm], [tag fields][kit-tag-fields], [API overview][kit-api], [privacy policy][kit-privacy], [DPA][kit-dpa]
- beehiiv: [pricing][beehiiv-pricing], [2026 plans][beehiiv-2026], [API keys][beehiiv-api-keys], [create subscription][beehiiv-create], [subscriber-facing language][beehiiv-language]
- MailerLite: [pricing][mailerlite-pricing], [free domain limits][mailerlite-free-domain], [creating an account][mailerlite-account]
- Buttondown: [pricing][buttondown-pricing], [sender address][buttondown-sender]
- Loops: [pricing][loops-pricing], [form endpoint][loops-form], [sending domain][loops-domain]
- Cloudflare: [D1 pricing][d1-pricing], [Email Service pricing][cf-email-pricing]
- Resend: [domains][resend-domains]
