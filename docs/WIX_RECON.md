# Wix Studio Reconnaissance — Riverbend Prototype

**Date:** 2026-05-02 · **Author:** Sable
**Companion:** SPEC.md §5, §6, §8
**Purpose:** Keep / adapt / drop for every design move in `redesign/` before WP-1.

---

## §1 — Custom CSS surface area in Wix Studio (2026)

Wix Studio exposes custom CSS through three vehicles:

1. **`global.css`** — Code panel → Page Code → CSS. Sitewide. https://support.wix.com/en/article/studio-editor-about-css-editing
2. **CSS Classes panel** — per-element. Pick a Wix-exposed "Global class" or write a "Custom class" in the panel. https://www.wix.com/studio/blog/custom-css
3. **Velo `$w` `.add()`** — programmatic class application at runtime. https://dev.wix.com/docs/develop-websites/articles/coding-with-velo/frontend-code/custom-css/apply-custom-css-styling

**Documented to work:** custom classes on any "supported element"; **CSS variables** (including site-theme vars: "You can also use site theme CSS variables…"); media queries; multiple animations per element; hover, scrollbar, custom-cursor styling.

**Undocumented / unverified from public docs:**
- Whether `linear-gradient + url(data:image/svg+xml;…)` survives Wix's HTML sanitizer.
- `font-variation-settings` — see §2 item 2.
- The exact "supported elements" list. Wix references it but the API page exceeded my fetch limit. Community consensus: **app widgets (Bookings, Forms, Stores) expose far fewer classes than native Studio elements**, and the deepest internals (calendar cells, checkout fields) are not addressable.

---

## §2 — Pattern verdicts

| # | Prototype pattern | Verdict | Notes / workaround |
|---|---|---|---|
| 1 | Custom CSS — clamp scale, vars, gradient+SVG bg, border separators, no radius | **KEEP** | All belong in `global.css`. CSS vars officially supported. Smoke-test the SVG data-URI post-publish. |
| 2 | Variable Google Fonts (Fraunces opsz/wght/ital, Inter wght) | **DROP variable, KEEP families** | Wix's own help article lists variable fonts as a *requested* feature, not shipped: https://support.wix.com/en/article/studio-editor-request-using-variable-fonts-in-your-sites-typography Use static cuts (Fraunces 600/800/600-italic, Inter 400/600). Lose `opsz`; recover the optical-size feel with weight + tracking. |
| 3 | Fixed left ruler (1px copper, rotated text) | **KEEP via Pin → Page** | Studio Inspector → Position → Pinned has scopes Container / Section / **Page** that ignore section boundaries. https://support.wix.com/en/article/studio-editor-pinning-elements-and-sections No CSS `position: fixed` needed. |
| 4 | Bleed-past-column hero (H1 overflows column) | **ADAPT** | Studio containers default to `overflow: visible`; documented 200vw bleed pattern: https://support.wix.com/en/article/studio-editor-working-with-overflow-content Build the hero in a wider parent cell with negative margin. Mobile breakpoint needs explicit `overflow=hide` to avoid horizontal scroll. |
| 5 | Bento grid via `gap: 1px; background: copper` | **ADAPT** | Studio Advanced CSS Grid exposes gap sizing in the Inspector but doesn't surface "background-through-gap" as a native pattern: https://support.wix.com/en/article/studio-editor-working-with-an-advanced-css-grid Workaround: custom class on the grid container with `background: var(--copper); gap: 1px;`, cells `background: var(--bone)`. Both are documented CSS properties on container classes. |
| 6 | Wix Bookings as load-bearing widget | **ADAPT (heavy)** | Customization is **panel-based, not CSS-first**. Documented surface: backgrounds, borders, dividers, fonts/colors per text element, button styling, show/hide of subtitles/filters/badges/availability, button labels (25-char limit). https://support.wix.com/en/article/wix-bookings-customizing-your-booking-calendar-page The calendar grid itself is not documented as CSS-addressable. Copper CTA + Fraunces fonts: achievable in panel. Replace native heading with our H1 above + hide widget title via panel toggle: achievable. Replace internal calendar layout: not. |
| 7 | Wix Forms (multi-page, file upload, copper-underline focus, italic labels) | **ADAPT** | Multi-page is built-in (right-click field → Move to next page). https://support.wix.com/en/article/studio-editor-adding-and-customizing-a-form File upload is a **premium field** requiring upgrade. Per-state field styling (regular/hover/error) exposed via panel. Copper-underline focus: needs custom class on input semantic class — confirm `:focus` is exposed. Italic labels: panel font selection. |
| 8 | Wix Members Area (dashboard, intake, billing, rebook) | **KEEP** | Custom Members pages are designed **from scratch** and gated by role or pricing plan. https://support.wix.com/en/article/studio-editor-using-the-members-area Pre-built tabs remain optional. Cleanest part of the stack — full Studio canvas, just permission-flagged. |
| 9 | Wix Stores gift cards | **ADAPT** | Gift Card landing page heavily customizable: colors, shapes, borders, fonts, buttons, display toggles. https://support.wix.com/en/article/wix-gift-cards-customizing-your-sites-gift-card-page Shared Wix Stores checkout is more limited: https://support.wix.com/en/article/customizing-the-checkout-page Expect landing matches brand cleanly; expect checkout looks Wix-y. |
| 10 | Velo (7 modules) | See §4 | Mostly feasible. Two friction points called out below. |
| 11 | Tide-line signature section | **KEEP** | Styled full-width section is a Studio primitive. No friction beyond §2.4 overflow patterns if bleeding past section width. |

---

## §3 — App customization envelopes

**Bookings.** Panel covers layout (daily/weekly desktop, monthly/weekly mobile), display toggles (subtitles, filters, dates, availability, video badges), design (backgrounds, borders, dividers, fonts, colors), buttons. Button labels capped at 25 chars. Calendar grid internals are not documented as CSS-addressable; one community article asserts custom CSS works, Wix's official article does not. **Plan: hide what we can in-panel, build editorial framing above/below, accept the grid stays Wix-native.** https://support.wix.com/en/article/wix-bookings-customizing-your-booking-calendar-page

**Forms.** Multi-page built-in. File upload is premium. Per-state styling (regular/hover/error) in panel. Underline-only focus needs custom class on input semantic class — feasible if `:focus` is exposed. Italic labels: trivial.

**Members Area.** Custom pages are blank Studio canvases, gated per-page by role or pricing plan. Pre-built tabs remain optional. Most flexible of the four apps. https://dev.wix.com/docs/develop-websites/articles/code-tutorials/wix-members/building-your-own-members-area

**Stores / Gift Cards.** Gift Card landing fully panel-customizable. Shared Wix Stores checkout is branded-but-constrained: https://support.wix.com/en/article/customizing-the-checkout-page

---

## §4 — Velo capability check (spec §6.1–6.7)

| # | Module | Verdict | Citation |
|---|---|---|---|
| 6.1 | Service-area gate (Mapbox geocode + radius) | **Feasible** | `wix-fetch` does outbound HTTPS from backend; secrets in Secrets Manager. https://dev.wix.com/docs/velo/apis/wix-fetch/introduction |
| 6.2 | Intake-completed gate (hold pending until intake) | **Feasible-with-friction** | Bookings V2 supports `PENDING → CONFIRMED/DECLINED` for services configured "requires approval"; `confirm()` advances. https://dev.wix.com/docs/velo/apis/wix-bookings-v2/bookings/how-bookings-are-confirmed-or-declined Friction: **service must be set to "requires approval"** so *every* booking lands PENDING; we auto-confirm via Velo for returning clients. Can't selectively gate only new clients at the API level. **Bookings V1 events deprecate 2026-06-30 — build on V2 SDK from day one.** |
| 6.3 | Package balance widget | **Feasible** | `wix-data` collection read keyed to member ID, rendered in custom Members page. |
| 6.4 | Smart rebook nudge (daily 06:00) | **Feasible-with-friction** | `jobs.config` supports daily + CRON. **All times UTC**, **shortest interval 1 hour**, max 20 jobs. https://dev.wix.com/docs/develop-websites/articles/workspace-tools/developer-tools/recurring-jobs/about-scheduling-recurring-jobs Encode 06:00 PT as 13:00 or 14:00 UTC depending on DST stance — pick one and document. |
| 6.5 | Mobile add-on calculator (Mapbox, 24h cache) | **Feasible** | Backend `wix-fetch` to Mapbox; cache in `wix-data` with TTL field; surface result before checkout via backend `.web.js` module. |
| 6.6 | Waitlist auto-fill on cancel <24h | **Feasible** | Subscribe to `onBookingCanceled` (V2 SDK), read `Waitlist` collection, send `triggeredEmails.emailContact()` for email + Twilio for SMS. https://dev.wix.com/docs/velo/apis/wix-crm-frontend/triggered-emails/email-contact |
| 6.7 | External CRM sync toggle | **Feasible** | Backend `wix-fetch` POST to webhook; toggle in admin page or settings collection. https://dev.wix.com/docs/velo/articles/getting-started/integration-with-third-party-services |

**Twilio SMS** (spec 4.5): Wix has first-party docs for the `twilio` npm module wrapped in a backend `.jsw` web module. https://www.twilio.com/en-us/blog/integrate-sms-wix-site-twilio-programmable-messaging-velo-ide

---

## §5 — Recommended order of operations

1. **WP-1a — Token preview page first.** One Studio page rendering the full type scale, swatches, spacing scale, paper-grain background, and a sample tide-line section. Confirm `global.css` accepts our CSS-variable system and the SVG data-URI grain renders post-publish. Don't build any other page until this passes.
2. **WP-1b — Static-cut font swap.** Replace prototype's variable-font CSS with static cuts. Re-eyeball headings; the "opsz 144" feel must come from weight + tracking.
3. **WP-2 — Bookings spike before any page templates.** Drop the widget on a throwaway page, max-out panel customization, screenshot next to prototype's Book page. This decides whether Book in WP-4 needs editorial framing only or a fundamentally different layout. **Bookings is the load-bearing widget; design around what it actually allows.**
4. **WP-3 — One Members custom page end-to-end.** Build Dashboard from scratch first to confirm gating + Studio-canvas pattern. Intake/Billing/Rebook reuse the pattern.
5. **WP-4** onward only after the above three smoke tests pass.

---

## §6 — Open unknowns (need a human in the Studio panel)

1. **Exact "supported elements" list for CSS Classes.** Wix references it; the public API page is too large to fetch. Enumerate which Bookings/Forms/Members sub-elements expose semantic classes.
2. **Whether `:focus` and other pseudo-classes are exposed on form input semantic classes** — required for the copper-underline focus state.
3. **Whether SVG data-URI backgrounds survive Wix's HTML sanitizer in `global.css`.** Publish and inspect.
4. **Bookings calendar-grid CSS reachability.** Third-party blog asserts it; Wix's official doc does not. Try a custom class on the widget in Studio.
5. **Whether `gap: 1px; background: var(--copper)` survives Studio's Advanced CSS Grid** — the widget may impose its own background or padding.
6. **Whether "requires approval" mode UX-degrades the booking flow** enough to feel slow even when Velo auto-confirms in seconds. Stopwatch test in staging.
7. **Whether `font-variation-settings` is stripped silently or actively rejected.** Try in a custom class, inspect computed styles on the published page.
8. **Members 2024-2025 overhaul scope.** Referenced in the prompt but not surfaced in current Wix help articles I could reach. The current model (custom pages + permissions) appears stable; no major overhaul is named publicly.
