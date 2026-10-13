# Next.js web UI and bilingual direction

Use the project's component library and design system. For a bilingual product, treat locale and direction as part of every screen's behavior. Arabic (RTL) and English (LTR) are examples; configure the actual language pair for the project.

## Locale contract

- Define supported locales, default locale, URL strategy, translation ownership, and missing-translation behavior. With App Router, a locale route segment such as `app/[locale]/` is one option; adapt to existing routing. Keep a direct link to a page stable and shareable.
- Set the document's `lang` and `dir` from the active locale on the server. Do not rely on a client effect to correct an initially wrong direction. Set both when switching locale.
- A language switch should preserve the equivalent route and meaningful query state when possible. Define what happens when content is unavailable in the target language. Check Back, Forward, Reload, and direct links.
- Keep interface strings in locale dictionaries or the repository's i18n system. Use locale-aware plural, number, date, and time formatting. Review Arabic copy with a fluent speaker; do not assemble sentences from translated fragments.
- Decide and record in the plan: digit style (Arabic-Indic or Western) per locale, calendar (Gregorian or Hijri), currency, and first day of the week. Format with `Intl` (`Intl.NumberFormat`, `Intl.DateTimeFormat`, `Intl.PluralRules`) rather than string concatenation.
- Localize page metadata and alternate-language links (`hreflang`, plus `x-default` when there is a default) where pages are indexed. Keep canonical and alternate URLs consistent with the routing policy.

## Structure and data

- Server-render pages when that serves content and performance goals. Keep client components at interaction boundaries, and avoid sending large admin-only libraries to public routes.
- Keep a consistent page shell and one main landmark. Put navigation permissions in a testable source when links vary by role.
- For shareable filters, search, sorting, pagination, or multi-step views, encode meaningful state in the URL. Back, Forward, Reload, and a pasted link should reconstruct the view in either locale.
- Centralize API access and error mapping. Keep personal state in authenticated reads rather than cacheable public responses. Match caching to privacy and freshness needs.
- When proxying API calls, verify header forwarding against the real deployment (see [deployment](deployment.md)).

## RTL and LTR layout

- Prefer CSS logical properties (`margin-inline-start`, `padding-inline-end`, `inset-inline-start`) and direction-aware component primitives over separate left/right layouts. Test both directions after changing shared styles.
- Mirror directional UI cues such as back arrows, progress flows, and drawers when their meaning depends on reading direction. Do not mirror logos, photos, charts, or other content automatically.
- If Tailwind is used, prefer `ms-*`/`me-*`/`ps-*`/`pe-*`/`text-start`/`text-end` over `ml-*`/`pl-*`/`text-left`, use `rtl:`/`ltr:` variants for exceptions, and add `rtl:rotate-180` (or swap icons) for directional icons.
- Fonts: load a font with proper Arabic glyph support (and Latin if the product mixes scripts) via `next/font` with subsets and `display: swap`; define a fallback stack per script. Give Arabic text enough line-height, and never apply letter-spacing to it.
- Validation messages: the API returns stable error codes; the UI maps codes to localized messages in both languages. Do not send localized prose from validators as the contract.
- Check overlays, focus order, tables, form labels, validation messages, and icon alignment in both directions. Verify third-party components in both locales rather than assuming their RTL support.
- For mixed-script content such as URLs, email addresses, codes, phone numbers, and user-entered names, use `dir="auto"` or an explicit field direction as appropriate. Isolate inline fragments so punctuation and numbers do not jump across text.
- Keep responsive layouts usable without unexpected horizontal overflow in either direction. Check touch target sizes on touch devices.

## Accessibility and states

- Aim for the accessibility standard the product requires; WCAG 2.2 AA is a useful baseline. Prefer semantic controls, visible focus, labels, keyboard access, reduced-motion support, and more than color alone to convey status.
- Contrast: at least 4.5:1 for body text and 3:1 for large text and UI boundaries, in light and dark themes.
- Every input has a programmatically associated label; errors are linked with `aria-describedby` and announced (`aria-live` or focus moved to the error summary).
- Move focus sensibly on route change, dialog open or close, and locale switch. Provide a skip-to-content link.
- Interactive targets are at least 24x24 CSS px (aim for 44x44 on touch). Translate `aria-label` text, and mark foreign-language fragments with their own `lang`.
- Run an automated check (axe, Lighthouse, or `eslint-plugin-jsx-a11y`) and a manual keyboard pass; automated tools catch only part of the problems.
- Design relevant loading, empty, error, and success states in both languages. An empty view should explain what happened and offer a useful next action where one exists.
- Test the locale switch and multi-step navigation with keyboard, Back, Forward, Reload, and a direct URL where those actions are part of the flow.

## Browser verification

- Use the repository's browser test harness. Exercise each affected flow in RTL and LTR at representative mobile and desktop widths when responsive behavior matters.
- Check the document `lang` and `dir`, translated labels and errors, locale-preserving navigation, mixed-direction fields, and absence of horizontal overflow. Include missing-translation or missing-content behavior if the feature can encounter it.
- Run against isolated test data and avoid coupling tests through shared rate limits or state. Capture failures and mention any browser checks that could not run.
