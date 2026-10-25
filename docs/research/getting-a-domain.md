# Getting a custom domain without an international card

Research for [issue 6](https://github.com/karim-belhadjali/hub/issues/6), feeding [Choose how to get the custom domain](https://github.com/karim-belhadjali/hub/issues/12). Checked on 2026-10-25 against registrar help pages, PayPal's own pages, the free-domain projects' rules, the Public Suffix List and the WebAuthn spec. Karim's country isn't stated, so every answer that turns on it says so. `karimbelhadjali.com` is still unregistered today. Verisign's RDAP returns 404 for it.[^rdap]

## Short answer

Keep `karimbelhadjali.com`. Paying for it is a solvable problem, and a free domain trades one purchase for a second RP ID move and a name someone else can take away.

Ranked by how likely each path is to work:

1. **A friend pays, then pushes the domain to Karim's Namecheap account.** This works whatever country Karim is in. Namecheap's Change Ownership is free, needs no 60-day wait, and can put Karim's contacts on the registration.[^nc-push] Ask the friend to buy several years up front, since .com allows up to ten.[^com-ra]
2. **Karim pays through PayPal into registrar account credit.** Namecheap and Porkbun both take PayPal, and both let PayPal buy account credit that auto-renew draws from.[^nc-pay][^nc-auto][^pb-pay][^pb-credit] The catch is that PayPal is a wrapper. It still needs a card, a local bank account or a received balance behind it, and which of those works depends on the country.[^pp-ua][^pp-bank] Ten minutes in the PayPal wallet settles whether this path is open.
3. **A registrar in Karim's own country that takes local money.** Some exist. ATI in Tunisia is ICANN-accredited and sells .com for 30 TND a year before tax.[^icann-list][^ati] Hostinger's Egypt and UAE brands take Fawry.[^hostinger] Whether one fits depends entirely on where Karim lives, and on whether it hands over DNS control and the transfer code.
4. **Crypto into account credit.** Namecheap, Porkbun and Dynadot all turn crypto deposits into credit that covers renewals.[^nc-pay][^pb-crypto][^dyn-opts] Getting crypto without a card, and whether that's legal where Karim lives, is a country question this note doesn't answer.
5. **A free domain.** `eu.org`, `is-a.dev` and DigitalPlat's names all sit on the Public Suffix List, so a `sila` subdomain would work as a passkey RP ID.[^psl][^webauthn-rpid] None of them is `karimbelhadjali.com`, all of them can be lost, and the hub already launches on a free host address. Don't use one as the custom domain.

A prepaid or virtual card bought without a card is the path I expected to find and didn't. Wise doesn't issue its card to anyone in North Africa or the Gulf,[^wise-card] and the local schemes I could read are tied to companies or to the bank's own e-commerce allowance.

## Path 1. Someone else pays and pushes the domain

This is the most reliable path because it moves the payment problem to someone who doesn't have it.

How it goes at Namecheap:

- The friend registers `karimbelhadjali.com` in their own Namecheap account with their own card or PayPal.
- They open the domain's Sharing & Transfer tab and use Change Ownership with Karim's Namecheap username or email. Namecheap says "The process is free of charge."[^nc-push]
- Karim confirms from an emailed link within 7 days while logged in. The domain then sits in his account.[^nc-push]
- At the authorization step the friend chooses the new owner's contacts, so Karim becomes the registrant. That needs Karim's account details complete first.[^nc-push]
- Namecheap's own page says "You don't have to wait for 60 days", even right after registration.[^nc-push]
- The move is final. Namecheap "will not be able to reverse it".[^nc-push] That cuts in Karim's favour once it lands.

From then on Karim holds the account, the registrant record and the DNS. The friend holds nothing.

Renewal is the weak spot. Namecheap's auto-renew charges account funds first, then saved cards, and PayPal can't pay automatic renewals.[^nc-auto] So either the friend buys five or ten years at the start, or someone tops up Karim's account funds before each expiry. Namecheap retries a failed domain renewal every 24 hours until the domain expires.[^nc-auto]

Gift cards don't help here. I found no gift cards on Namecheap's, Porkbun's or Hostinger's sites, and none of their payment pages lists one.[^nc-pay][^pb-pay][^hostinger]

ICANN's Transfer Policy only bites if the domain later moves to another registrar. A change of registrant triggers a 60-day lock on inter-registrar transfers unless the holder opts out before the change.[^icann-transfer] A transfer is also refused within 60 days of creation.[^icann-transfer] Neither blocks the push. ICANN's Transfer Policy review proposes dropping the post-change lock, but I found no adopted effective date.[^tpr]

## Path 2. Karim pays through PayPal

Registrars that take PayPal, from their own pages:

| Registrar | PayPal | Renewal without a card |
|---|---|---|
| Namecheap | Yes. Account funds deposits by card or PayPal start at 5 USD[^nc-pay] | Auto-renew uses account funds first. PayPal can't pay auto-renewals directly[^nc-auto] |
| Porkbun | Yes, for purchases and renewals, but "PayPal can't be used for automatic payments"[^pb-pay] | Account credit from 10 USD, bought with any method Porkbun offers, covers automatic renewals[^pb-credit] |
| Cloudflare Registrar | Yes. Cloudflare billing lists PayPal[^cf-faq] | Registrar only charges the primary payment method, and auto-renew runs about 30 days before expiry. Domains must use Cloudflare nameservers[^cf-reg] |
| Dynadot | Yes, in USD, GBP, EUR, CAD, AUD and MXN. Also takes Payoneer from 30 USD[^dyn-opts] | Account credit, which crypto and wires can fund[^dyn-opts] |

So a PayPal account solves both the purchase and the renewal, as long as PayPal-bought credit sits in the registrar account before each expiry.

The real question is whether PayPal works for Karim at all:

- PayPal accounts in North Africa and the Gulf run under PayPal Pte. Ltd.'s rest-of-world agreement, which says "service availability varies by country/region" and that you see what's available by logging in.[^pp-ua]
- A linked bank must be "in the same country as your PayPal account, and in the local currency", and adding money from a bank only works in some countries.[^pp-bank][^pp-ua]
- Hostinger states it plainly for its own checkout. PayPal works only if "You have a debit or credit card connected to your PayPal account."[^hostinger-pp]
- PayPal's country list includes Morocco, Algeria, Tunisia, Egypt, Mauritania, Saudi Arabia, the UAE, Qatar, Kuwait, Bahrain, Oman and Jordan. Lebanon and Libya aren't on it.[^pp-countries]
- PayPal's payouts table lists send, receive and withdraw for Morocco, Saudi Arabia, Qatar, the UAE, Kuwait, Bahrain, Oman and Jordan. Algeria, Tunisia, Egypt and Mauritania aren't in it.[^pp-payouts]

In a country where Karim's PayPal can receive money, a friend can send PayPal balance and Karim spends it on registrar credit. Where it can't receive and won't link a local card, PayPal is a dead end.

## Path 3. A local registrar or a regional brand

ICANN-accredited registrars headquartered in the region, from ICANN's live list:[^icann-list]

| Country | Accredited registrars |
|---|---|
| Tunisia | ATI |
| Morocco | Genious Communications, Arcanes Technologies |
| UAE | E & Enterprise Cloud, Buzinessware, Gate Hills Technology, Hostyaga, Onpassive Technologies, PE Overseas |
| Kuwait | KuwaitNET |
| Jordan | Talal Abu Ghazaleh Intellectual Property |

I found none listed in Algeria, Egypt, Saudi Arabia, Qatar, Bahrain, Oman, Lebanon, Libya or Mauritania. Local hosting resellers exist everywhere, but a reseller sits between Karim and the registrar, so ask the questions below twice as hard.

What I could confirm:

- **ATI, Tunisia.** Sells .com and .net at 30 TND a year before tax, and .tn at 12 TND.[^ati] Its general terms accept individuals as clients and bill them in full at signature.[^ati-cg] The site doesn't say how to pay or whether you can set your own nameservers.
- **Genious, Morocco.** Shows a MAD currency option but lists prices in dollars and names no payment methods.[^genious]
- **Hostinger regional brands.** The Egypt brand takes cards, PayPal, Apple Pay, Google Pay and Fawry, priced in EGP. The UAE brand takes cards, PayPal, Google Pay, Fawry and crypto. The Morocco brand prices in MAD and takes cards, PayPal, Apple Pay and Google Pay. An account can't switch brand later.[^hostinger] Fawry is the only cash route I found on a first-party page.

Before paying any local seller, get written answers to four questions. Will Karim be the registrant of record? Can he set his own nameservers or at least every record type, including TXT? Will they hand over the transfer code on request, which ICANN requires within five days?[^icann-transfer] How does renewal get paid each year?

Local cards are the other country-specific route, and the ones I read don't fit an individual. Tunisia's "Carte Technologique Internationale" carries a 10,000 TND yearly allowance for resident companies, 100,000 TND for labelled startups.[^bct] Morocco's Office des Changes runs an e-commerce allowance loaded onto an international card. Its site blocked me, so I couldn't confirm the 2026 amount for individuals.

## Path 4. Crypto into account credit

- Namecheap takes crypto only as an account funds deposit, from 1 USD, usually credited within an hour.[^nc-pay]
- Porkbun takes one-time crypto deposits through Coinbase as credit "for future use like automatic renewals".[^pb-crypto]
- Dynadot accepts BTC, LTC, USDT and USDC and credits the account once confirmed.[^dyn-opts]

Renewal works the same as PayPal credit. The hard part sits upstream. Without a card, crypto means buying from a person or a local exchange, and the legal position differs by country. I'd treat this as the last paid option.

## Path 5. Free domains

The passkey rule first. WebAuthn says "The RP ID must be equal to the origin's effective domain, or a registrable domain suffix of the origin's effective domain."[^webauthn-rpid] The HTML spec's check returns false when the candidate is itself a public suffix.[^html-suffix] So if a service's parent name sits on the Public Suffix List, each user's name is its own registrable domain, a `sila` host under it can be an RP ID, and other users of the service can't scope credentials to it. The Android app still needs `assetlinks.json` served at the RP ID host.[^cm-prereq]

All of these sit in the private section of the Public Suffix List, version 2026-10-22.[^psl]

| Service | On the PSL | `sila` as an RP ID | Can it be lost |
|---|---|---|---|
| eu.org | Yes, `eu.org` and all of its open subzones such as `asso.eu.org` and `net.eu.org` | Yes. `karim.eu.org` is delegated to your own nameservers, so `sila.karim.eu.org` and `console.sila.karim.eu.org` are yours to create | Yes. The site states no term and no guarantee, says "No support is provided", and very strongly asks small commercial sites to buy a real domain instead[^euorg][^euorg-policy]. Approval time isn't published. Earlier Sila research found user reports of months[^sila-lf] |
| is-a.dev | Yes | Yes. Nested names like `sila.karim.is-a.dev` are allowed once you hold the parent[^isadev-faq] | Yes, easily. The terms let them "terminate your subdomain at any time, for any reason", ban commercial use and blogs not mainly about software, and say they are strongly against pull requests made with AI tools. Every DNS change is a pull request, and NS delegation needs a 30-day-old subdomain plus approval[^isadev-tos][^isadev-faq] |
| DigitalPlat FreeDomain | Yes, `us.kg`, `xx.kg`, `qzz.io` and `dpdns.org` | Yes. It delegates to your own nameservers[^dp-repo] | Yes. One domain per account, and names expire and need renewing under rules that "can change"[^dp-faq][^dp-renew] |
| js.org | Yes | Yes in principle | Needs "a clear connection to JavaScript", per the earlier Sila research.[^sila-lf] A bank demo fails that |

eu.org is the only one I'd trust with an RP ID, and only as a stopgap. is-a.dev's terms make the hub a removal risk the day someone reads the waitlist as commercial.

Free host addresses already cover the gap these would fill. ADR 0002 put the customer RP ID on `sila-demo.pages.dev`,[^adr2] and `pages.dev` is on the same list.[^psl] Moving from there to `eu.org` and later to `.com` would mean two RP ID moves and two rounds of broken links.

## Renewal

Whatever path Karim picks has to work again every year, or be paid far ahead.

- .com registrations and renewals can run up to ten years in total.[^com-ra] Each year bought now is one fewer renewal to fund.
- Namecheap and Porkbun both renew from account credit, and credit can be bought with PayPal or crypto.[^nc-auto][^pb-credit]
- Cloudflare renews only from the primary payment method, retrying three more times before you must renew by hand.[^cf-reg]
- A friend-paid domain renews from Karim's own account funds, so the friend tops up credit rather than taking the domain back.

## Open points for issue 12

- **Is it still `karimbelhadjali.com`?** I'd say yes. A free domain reopens ADR 0001,[^adr1] and the hub launches on a free address anyway.
- **Which country is Karim's PayPal in, and what can it link?** If it links a local card or bank, or can receive a balance, path 2 is open and Karim needs nobody.
- **Is there a friend abroad willing to pay?** If yes, path 1 at Namecheap is the default. Decide who funds renewals.
- **Which registrar.** Namecheap is the only one with a verified free push between accounts and funds-first auto-renew. Porkbun's PayPal credit renews too. Cloudflare forces its own nameservers.
- **How many years up front.** One to ten. More years means fewer renewal scrambles, at a higher first payment.
- **Where DNS lives.** Issue 18 asks Karim to record it. Registrar DNS is the simplest. Moving DNS elsewhere is a separate choice.
- **Is a local seller worth asking?** Only if Karim lives somewhere with one, and only with the four questions in path 3 answered.

## What I could not verify

- Which PayPal features work in Karim's country. PayPal's public pages defer to the logged-in account.
- Whether Porkbun can move a domain between accounts the way Namecheap does.
- ATI's payment methods, nameserver policy and transfer-out practice.
- Morocco's 2026 e-commerce allowance for individuals. The Office des Changes site rejected my requests.
- Spaceship's payment methods. Its legal pages returned 403.
- How long eu.org takes to approve a request today.
- DigitalPlat's renewal window and whether renewing ever costs money.

## Sources

[^rdap]: Verisign RDAP for .com, `karimbelhadjali.com`, queried 2026-10-25, HTTP 404. https://rdap.verisign.com/com/v1/domain/karimbelhadjali.com
[^nc-push]: Namecheap knowledge base, "How can I move a domain from one Namecheap account to another?". https://www.namecheap.com/support/knowledgebase/article.aspx/1187/46/how-can-i-move-a-domain-from-one-namecheap-account-to-another/
[^nc-pay]: Namecheap knowledge base, "What payment methods do you accept?". https://www.namecheap.com/support/knowledgebase/article.aspx/35/7/what-payment-methods-do-you-accept-for-domain-registrations
[^nc-auto]: Namecheap knowledge base, "Can I set up automatic billing for my Namecheap services?". https://www.namecheap.com/support/knowledgebase/article.aspx/10564/2207/can-i-set-up-automatic-billing-for-my-namecheap-services/
[^pb-pay]: Porkbun, "Payment options". https://porkbun.com/support/payment_options
[^pb-credit]: Porkbun knowledge base, "How to add funds to your Porkbun account". https://kb.porkbun.com/article/286-how-to-add-funds-to-your-porkbun-account
[^pb-crypto]: Porkbun knowledge base, "How to use cryptocurrency at Porkbun". https://kb.porkbun.com/article/143-how-to-use-crypto-currency-at-porkbun
[^cf-faq]: Cloudflare docs, "Billing FAQ". https://developers.cloudflare.com/billing/understand/faq/
[^cf-reg]: Cloudflare docs, "Registrar FAQ". https://developers.cloudflare.com/registrar/faq/
[^dyn-opts]: Dynadot, "Payment options". https://www.dynadot.com/payment-options
[^pp-ua]: PayPal user agreement, rest of world version served for Tunisia, Morocco, Algeria and Egypt, provider PayPal Pte. Ltd. https://www.paypal.com/tn/legalhub/paypal/useragreement-full
[^pp-bank]: PayPal help, "Why can't I link a bank account to my PayPal account?". https://www.paypal.com/c2/cshelp/article/why-cant-i-link-a-bank-account-to-my-paypal-account-help199
[^pp-countries]: PayPal, "Worldwide" country list. https://www.paypal.com/us/webapps/mpp/country-worldwide
[^pp-payouts]: PayPal Developer, "Payouts country and feature support". https://developer.paypal.com/docs/payouts/standard/reference/country-feature/
[^hostinger]: Hostinger help, "What payment methods are available at Hostinger?". https://www.hostinger.com/support/1583236-what-payment-methods-are-available-at-hostinger/
[^hostinger-pp]: Hostinger help, "How to pay with PayPal on Hostinger". https://www.hostinger.com/support/1583586-how-to-pay-with-paypal-on-hostinger
[^icann-list]: ICANN, "Accredited registrars" list, read 2026-10-25. https://www.icann.org/registrar-reports/accredited-list.html
[^ati]: ATI, "Nom de domaine". https://www.ati.tn/nom-de-domaine/
[^ati-cg]: ATI, "Conditions générales". https://www.ati.tn/conditions-generales/
[^genious]: Genious Communications, home page. https://www.genious.net/
[^bct]: Banque Centrale de Tunisie, circular to approved intermediaries n° 2019-02, 30 January 2019, article 14. https://www.mtc.gov.tn/fileadmin/StartupAct/Cir_2019_02_fr.pdf
[^wise-card]: Wise help, "Can I get the Wise card in my country?". https://wise.com/help/articles/2968915/can-i-get-the-wise-card-in-my-country
[^icann-transfer]: ICANN, "Transfer Policy", sections I.A.3.7.5, I.A.5.2 and II.C.2. https://www.icann.org/resources/pages/transfer-policy-2016-06-01-en
[^tpr]: GNSO Transfer Policy Review working group, final report, 4 February 2025, recommendation 26.4. https://gnso.icann.org/sites/default/files/policy/2025/correspondence/tpr-team-to-gnso-council-04feb25-en.pdf
[^com-ra]: ICANN, .com Registry Agreement, 1 December 2024, sections 2.1 and 2.2 of the functional specifications. https://itp.cdn.icann.org/en/files/registry-agreements/com/com-agreement-html-01-12-2024-en.htm
[^psl]: Public Suffix List, version 2026-10-22_08-36-41_UTC, commit 8437e526, private section entries for `eu.org`, `is-a.dev`, `us.kg`, `xx.kg`, `qzz.io`, `dpdns.org`, `js.org` and `pages.dev`. https://publicsuffix.org/list/public_suffix_list.dat
[^webauthn-rpid]: W3C, Web Authentication Level 3, Recommendation of 25 August 2026, "RP ID". https://www.w3.org/TR/webauthn-3/
[^html-suffix]: WHATWG HTML, "is a registrable domain suffix of or is equal to". https://html.spec.whatwg.org/multipage/browsers.html#is-a-registrable-domain-suffix-of-or-is-equal-to
[^cm-prereq]: Android Developers, "Credential Manager prerequisites", Digital Asset Links. https://developer.android.com/identity/credential-manager/prerequisites
[^euorg]: EU.org, home page. https://nic.eu.org/
[^euorg-policy]: EU.org, "General policy". https://nic.eu.org/policy.html
[^isadev-tos]: is-a-dev/register, `TERMS_OF_SERVICE.md`, last updated 31 August 2026. https://github.com/is-a-dev/register/blob/main/TERMS_OF_SERVICE.md
[^isadev-faq]: is-a.dev docs, "FAQ" and "Domain structure". https://docs.is-a.dev/faq/
[^dp-repo]: DigitalPlatDev/FreeDomain README. https://github.com/DigitalPlatDev/FreeDomain
[^dp-faq]: DigitalPlatDev/FreeDomain, `documents/domains/faq.md`. https://github.com/DigitalPlatDev/FreeDomain/blob/main/documents/domains/faq.md
[^dp-renew]: DigitalPlatDev/FreeDomain, "Check status and renew". https://github.com/DigitalPlatDev/FreeDomain/blob/main/documents/tutorial/platform/1.4-status-and-renewal.md
[^adr1]: Sila ADR 0001, "Sila runs on a subdomain of the personal hub". https://github.com/karim-belhadjali/islamic-bank/blob/main/docs/adr/0001-sila-on-a-hub-subdomain.md
[^sila-lf]: Sila research, "Local-first setup for milestone 1", section 4, branch `research/local-first-milestone-1`. https://github.com/karim-belhadjali/islamic-bank/blob/research/local-first-milestone-1/docs/research/local-first-milestone-1.md
[^adr2]: Sila ADR 0002, "Milestone 1 runs local first on temporary RP IDs". https://github.com/karim-belhadjali/islamic-bank/blob/main/docs/adr/0002-milestone-1-runs-local-first.md
