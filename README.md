# Riverbend Massage Therapy — Prototype

**Live:** https://helo3301.github.io/riverbend-prototype/
**Status:** Critique-then-redesign portfolio piece by Hestia's Creations
**Client:** Fictional. Riverbend is a hypothetical solo-LMT studio in Springfield, Oregon — used to demonstrate Wix Studio + Velo capability against a realistic small-business spec.

---

## What this is

A static HTML/CSS prototype that proves a committed visual direction (editorial bodywork — sage on bone with copper as load-bearing structural ink, Fraunces italic as a sitewide ritual, a "tide line" signature element bisecting every page) for a Wix Studio + Velo site that doesn't yet exist.

The repo contains:

1. **The redesign** — 15 prototype pages (13 public + 2 member-area) with a shared design system in `styles.css`.
2. **The critique** — the original 10 ChatGPT-generated mockups in `docs/ai-mockups/`, kept for before/after value.
3. **The recon** — `docs/WIX_RECON.md`, a Wix Studio + Velo capability map produced before declaring the prototype "buildable."
4. **The build plan** — `docs/BUILD_PLAN.md`, which translates the prototype into a Wix-specific WP execution sequence.
5. **The original spec** — `docs/SPEC.md`, the v0.1.0 site spec the prototype was built against.

## Versions

- **v0.1** — initial 10-page prototype (8 public + 2 member-area), three documents, AI-mockup reference set.
- **v0.2** — feedback iteration: added Modalities index + 3 missing modality detail pages (Sports, Lymphatic, Integrated), a real Policies page (Cancellation / Privacy / Accessibility / SMS / Scope) so footer policy links resolve, fixed all date logic for May 2026, normalized navigation across all pages, restored Book as primary CTA on Contact, replaced "verify →" credentials with real verification links, softened over-absolute language, accessibility fixes on slot grids, relocated the Wix Bookings seam annotation from the Book page to the prototype index where it belongs.

## Why it exists

To answer the question *"if I had to ship a Wix Studio site for a real LMT, would I produce another generic-spa-template, or would I make something with a point of view?"* — and to demonstrate the difference rigorously enough that a prospect can see both versions side by side.

## Reading order

1. Visit https://helo3301.github.io/riverbend-prototype/ — the prototype index, with all 15 pages, all 10 AI reference mockups, and the three documents linked.
2. Compare any redesign page against its AI counterpart (e.g. the Pricing page redesign vs `AI·05`).
3. Read `docs/WIX_RECON.md` for the keep / adapt / drop verdicts on each design move against current Wix Studio capability.
4. Read `docs/BUILD_PLAN.md` for what changes between the static prototype and the actual Wix build, in order.

## Design language, in one sentence

Editorial bodywork — sage on bone, copper as load-bearing structural ink, Fraunces italic ritual, and one signature element (the tide line) that bisects every page at the same vertical anchor.

## Technical notes

- Vanilla HTML + CSS only, no framework, no build step.
- Single shared stylesheet (`styles.css`), CSS custom properties for every design token.
- Fluid type via `clamp()`, paper-grain via inline SVG data URIs.
- WCAG 2.2 AA targets; reduced-motion honored; mobile collapse rules at 960px.
- Photography placeholders are colored gradients with comments noting the intended treatment — to be replaced with real photography during the Wix build.

## License

Private IP. © Tim Allen, all rights reserved. Licensing negotiable under commercial engagement.

The prototype is published publicly only for portfolio reference. Do not reuse the design system, copy, or layout patterns without explicit permission.
