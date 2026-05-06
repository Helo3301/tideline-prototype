# Tideline — From Prototype to Wix Studio Build Plan

**Date:** 2026-05-02
**Author:** Sable
**Inputs:** `redesign/*.html`, `WIX_RECON.md`, `SPEC.md`
**Purpose:** What changes between the static prototype and the Wix build, in order, with no ambiguity left at the seams.

---

## §1 — Verdict, in one sentence

**Buildable.** The recon found one feature drop (variable fonts), three patterns that need adapt-not-keep (column bleed, bento grid background, Bookings widget), and zero hard blockers. The prototype's *design language* — sage-on-bone, copper as load-bearing, Fraunces italic ritual, tide line, bento-not-cards — survives the move into Wix Studio. The prototype's *file structure* does not — Wix is page-based, panel-driven, with apps as embedded widgets, so "translate the HTML 1:1" is the wrong mental model. Translate the *patterns*.

---

## §2 — Changes to make to the prototype before WP-1

These are corrections to land *before* the spec hits Wix Studio so we're not redesigning at build time.

### 2.1 Drop variable-font axes (recon §2 item 2)
Wix Studio doesn't ship variable fonts in 2026. Replace `font-variation-settings` everywhere with static cuts:

- **Fraunces** — load 600, 700, 600-italic, 400-italic. Drop opsz axis; recover the optical-size feel with weight + tracking + size.
- **Inter** — load 400, 500, 600. Already static-friendly.

Update `styles.css` token block:
```css
/* Before: --display loaded 9..144 + 300..900 with opsz axis */
/* After: load Fraunces[600,700,400ital,600ital] + Inter[400,500,600] only */
```

The italic ritual still works. The hero numerics (`$155`, `90`, the giant pricing numbers) lose a little of their optical-size grace but compensate with weight contrast.

### 2.2 Re-mark all bleed-past-column heroes as "needs Wix overflow pattern" (recon §2 item 4)
Three places in the prototype use this trick:
- `deep-tissue.html` H1 (132% width, negative margin)
- The about-teaser pull-quote left border bleeds into the column gap
- The members-bar sub-nav extends past content padding

In Wix, this needs the documented 200vw bleed pattern (`overflow: visible` on parent, child sized to viewport) — *not* CSS `position`/`negative margin` directly on a Studio container. Mobile must explicitly toggle `overflow=hide` to avoid horizontal scroll.

**Action:** annotate each of the three bleed sites in the prototype with a `/* WIX: implement via 200vw bleed pattern, set mobile overflow=hide */` comment. I'll add these in 2.5.

### 2.3 Rewrite bento grids as custom-class pattern (recon §2 item 5)
The `gap: 1px; background: copper` trick is documented CSS, but Studio's Advanced CSS Grid widget exposes its own props that may override. The safe path:

- Wrap each bento in a *custom-class container* (not the Studio Grid widget)
- Apply our class with `display: grid; grid-template-columns: ...; gap: 1px; background: var(--copper);`
- Each child gets its own class with `background: var(--bone)`

This is what we're already doing in CSS. The translation is "don't use Studio's Grid widget for bentos — use a Custom Element with our class." Same code, different vehicle.

### 2.4 Forms: confirm `:focus` exposure on input semantic class
The contact and intake forms use a single underline + copper focus state. Recon flagged `:focus` exposure as unverified. **Cheapest test:** spike one form field in Studio with a custom class targeting `input.our-class:focus` and inspect computed style on the published page. If `:focus` is stripped, fall back to JS-applied class on focus event (still cheap, slightly less robust).

### 2.5 Annotate the prototype with WIX: comments
Walk every file and drop `<!-- WIX: ... -->` comments at the seams where Wix-specific implementation differs from the static HTML. Specifically:

- Every place we use `position: fixed` → `WIX: use Pin → Page in Inspector, not CSS fixed`
- Every bleed-past-column hero → `WIX: 200vw bleed pattern; mobile overflow=hide`
- Every form field → `WIX: spike :focus exposure first; JS fallback if needed`
- Every booking-widget zone → `WIX: hide widget title, copper button, Fraunces font via panel`
- Every paper-grain background → `WIX: smoke-test SVG data-URI in global.css post-publish`

I'll do this pass next time we return to the prototype — it's mechanical and shouldn't be done speculatively.

---

## §3 — Velo modules, in implementation order

From recon §4: all 7 modules are feasible. Build order optimizes for what de-risks the Bookings flow first.

| Order | Module | Spec § | Why this order |
|---|---|---|---|
| 1 | Intake-completed gate (booking middleware) | §6.2 | The whole new-client flow depends on this. Bookings V2 PENDING/CONFIRMED API is the load-bearing call. **Build on V2 from day one — V1 deprecates 2026-06-30.** |
| 2 | Service-area gate (Mapbox) | §6.1 | Required before any in-home service goes live. Backend `wix-fetch` to Mapbox; cache in `wix-data`. |
| 3 | Mobile add-on calculator | §6.5 | Same Mapbox infra as §6.1; piggyback. |
| 4 | Package balance widget | §6.3 | Pure read-rendering; no external integrations. Builds the Members area pattern. |
| 5 | Smart rebook nudge | §6.4 | Needs `jobs.config` cron (UTC, 1h minimum interval, 20-job cap). Encode 06:00 PT as 14:00 UTC and pin to PDT (no DST handling for v1). |
| 6 | Waitlist auto-fill on cancel <24h | §6.6 | Needs `onBookingCanceled` V2 event + Twilio. Build after Twilio integration is proven. |
| 7 | External CRM sync toggle | §6.7 | Last because it's optional and toggle-gated. Useful for v2 multi-client variant. |

**Twilio**: officially-documented integration via npm + backend `.jsw`. Set up Secrets Manager for the auth token before any module touches it.

---

## §4 — Order of WP execution (replaces SPEC §11)

The recon-recommended sequence (recon §5) refines SPEC §11:

| WP | Scope | Done = |
|---|---|---|
| **WP-0** *(new)* | Token preview page in Studio | `global.css` accepts our CSS-var system; SVG grain renders on published page; paper-grain visible at 4% opacity |
| **WP-1** | Static-cut font swap + design tokens validated | All 7 swatches + type scale + tide-line render in Studio |
| **WP-2a** *(new)* | Bookings widget spike on a throwaway page | Panel max-out screenshotted next to prototype Book; gap noted; design adapted if needed |
| **WP-2b** | Wix Bookings: services, hours, deposits, single-staff config | All 5 modalities bookable in staging; deposit flow tested |
| **WP-3a** *(new)* | Custom Members page end-to-end (Dashboard) | Gating + Studio canvas pattern proven on one page before all four are built |
| **WP-3b** | Forms (intake, contact, waitlist) + Members Area + permissions | New client flow works; intake renews annually |
| **WP-4** | Page templates + final content (8 public + 4 member) | All pages built from tokens; Lighthouse a11y ≥95 |
| **WP-5** | Velo modules in order from §3 above | Each module: unit test + manual happy-path log |
| **WP-6** | Automations + Twilio + Mapbox + analytics | All automations fire in staging with seeded clients |
| **WP-7** | SEO, schema, GBP sync, perf pass, packaging | CWV green on 5 representative pages; portfolio writeup drafted |

**WP-0, WP-2a, and WP-3a are the new de-risking spikes.** They each cost a half-day at most and either confirm the design holds or surface a Wix-specific friction we can correct before committing the rest of the build.

---

## §5 — Open questions still blocking WP-1 (from SPEC §13)

These need owner input before any code is written. None of them are recon-related — they're all business-decision blockers:

1. Existing client list (size, format) for migration into Members
2. Phone routing — Wix Inbox forward to mobile, or Google Voice
3. Email domain — Google Workspace, or Wix-hosted
4. Service-area exact polygon, or postal-code list (current draft assumes radius)
5. Tip prompt: opt-in by default, or off
6. Which CRM (HubSpot / Mailchimp / neither) for §6.7
7. Annual intake re-attestation grace window — 30, 60, or block at 365 + 1

A 30-minute call with Lena resolves all seven.

---

## §6 — What I'd want to see before declaring "ready to build"

1. **Owner walkthrough.** Lena reviews the 10 prototype pages, weighs in on copy voice (does it sound like her, or like me-pretending-to-be-her), gives sign-off on the pricing-tier framing and the cancellation-policy plain-language version.
2. **§13 questions answered.** All seven.
3. **Photography brief.** Shotlist for treatment-room (4), portrait (1 hero + 2 working), modality-specific (5 — one per practice). Ideally one shoot day with a local photographer.
4. **WP-0 spike done.** Token preview in Studio confirms `global.css` + paper grain + Pin → Page works.
5. **WP-2a spike done.** Bookings widget panel-customized as far as it goes; gap-to-prototype noted; if the gap is wider than expected, redesign Book.html before WP-4.

After that, WP-2b through WP-7 is mostly mechanical — the design language is locked, the patterns are documented, and the constraint map is the source of truth.

---

## §7 — Honest closing

This prototype + recon is enough for a build to start with conviction and not drift. It is not enough for a *quote*. A quote needs the WP-0/2a/3a spikes done and Lena's input on the open questions. Until those, any time/cost estimate is theatre.

Realistic build estimate, *given* spikes pass and §13 questions resolve:

- **Design + content build (WP-0 through WP-4):** 35–50 hrs
- **Velo modules (WP-5):** 25–40 hrs
- **Automations + integrations (WP-6):** 12–20 hrs
- **SEO + perf + packaging (WP-7):** 8–12 hrs
- **Total:** 80–120 hrs over 4–6 calendar weeks at part-time pace

Half of that is content production (copy review with Lena, photography shoot day, QA cycles), not custom code. Velo is the largest single block of new code; everything else is configuration + theming.

---

*End of build plan.*
