# Every locale is prefixed, and the root picks one

Every hub URL starts with its locale: `/en/`, `/fr/` or `/ar/`. Project pages live at `/<locale>/projects/<slug>` and posts at `/<locale>/posts/<slug>`, with the same ASCII slug in every locale. English could have sat at the root for shorter links, but Karim wanted the three locales treated as equals, and every link he posts makes the scheme permanent. So it is fixed before the first post. Decided in [Choose the URL and locale scheme](https://github.com/karim-belhadjali/hub/issues/11).

## How the root works

- The bare root and any unprefixed path, such as `/projects/sila`, get a 302 to the same path in a locale. The locale comes from the cookie the language switcher sets, then from `Accept-Language` if it's French or Arabic, and otherwise it's English.
- That redirect is a small Worker on `karimbelhadjali`. It only runs when no static file matches, so every page is still served as a static file with no invocation.
- LinkedIn posts and episodes link the prefixed URL in the post's own language, such as `karimbelhadjali.com/en/projects/sila`, so a posted link never depends on the redirect.
- `www.karimbelhadjali.com` 301s to the apex with the path unchanged, through a redirect rule scoped to the `www` host. `sila` and `packetvillage` are untouched.
- A post with no French or Arabic translation still shows in the French and Arabic post lists, marked as English, and links to its `/en/` URL. No `/fr/` or `/ar/` page exists for it until a translation does.

## Considered options

- **English at the root.** Shorter links and no Worker code, but English becomes the default and the other two become the variants.
- **Translated slugs.** Better for local search, but the switcher would need a lookup table, and Arabic slugs get percent-encoded when pasted.
