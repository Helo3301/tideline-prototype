# Riverbend Massage Therapy — Wix Studio Site Spec

**Version:** 0.1.0 (draft)
**Author:** Sable, for Hestia's Creations portfolio
**Date:** 2026-05-02
**Companion piece:** [McKenzie Valley Tree](../mckenzie-valley-tree/SPEC.md) (WordPress brochure, emergency-CTA driven). This site is its functional opposite: appointment-driven, payment-routed, member-area gated. Together they demonstrate the same design DNA across two stacks and two service-business models.

---

## 1. Business Identity

**Riverbend Massage Therapy** — solo licensed massage therapist working out of a single-room studio in downtown Springfield, OR with a limited mobile radius for in-home sessions.

| Field | Value |
|---|---|
| Owner | Lena Whitford, LMT |
| Credentials | OR Board of Massage Therapists License #XXXXX, AMTA member, CPR-certified |
| Service area | Springfield, Eugene, Coburg, Creswell (in-studio); 10-mile mobile radius from studio |
| Modalities | Deep tissue · Prenatal · Sports recovery · Lymphatic drainage · Integrated session (custom blend) |
| Tagline | *"Bodywork that meets you where you are."* |
| Hours | Tue–Fri 10–7, Sat 9–3, closed Sun/Mon |
| Studio | 1 treatment room, ADA-accessible, hypoallergenic linens, diffuser-free room available on request |

**Brand voice:** Calm, practitioner-first, plain-language. No "wellness journey" filler. Treats the client like an adult who knows their own body. Mirrors McKenzie's *"Local work. Careful hands"* register — measured, specific, no upsell.

**H1 on Home:** *"Pressure where you want it. Quiet where you don't."*

---

## 2. Purpose

The site exists to do four things, in priority order:

1. **Convert qualified visitors to a booked first session** with as little friction as Wix can offer.
2. **Replace the owner's manual ops** — SMS tag, Google Doc intakes, Venmo invoices — with one cohesive system.
3. **Retain repeat clients** through packages, memberships, gift cards, and proactive rebook nudges.
4. **Earn organic local-search traffic** for "massage Springfield Oregon" + modality-specific intent terms.

**Non-goals:** Multi-therapist scheduling, insurance billing, HSA/FSA card processing, full SOAP charting. Those belong in Jane App or ClinicSense; this site stays brochure-plus-bookings.

---

## 3. Site Architecture

**Public pages (8):**

| Page | Slug | Role |
|---|---|---|
| Home | `/` | Hero · Services · Trust · CTA stack |
| Services | `/services` | Modalities grid + "How to choose" guide |
| Service detail | `/services/{slug}` | Per-modality landing (5 pages) |
| Book | `/book` | Wix Bookings widget + service-area gate |
| Pricing & Packages | `/pricing` | Single sessions · 5-pack · 10-pack · monthly membership |
| About | `/about` | Owner bio · credentials · studio photos |
| Gift Cards | `/gift-cards` | Wix Stores card SKUs |
| Contact + FAQ | `/contact` | Form · hours · directions · accessibility info · 12 FAQs |

**Member area (gated):**

| Page | Role |
|---|---|
| Dashboard | Upcoming appointments · package balance widget · last-session note (client-facing) |
| Intake | View / re-attest annually · upload referral notes |
| Billing | Card on file · invoice history · receipts |
| Rebook | One-tap rebook with last-used modality + therapist preferences pre-filled |

**Footer (locked, every page):** Phone · address (with map link) · hours · ADA badge · OR license # · cancellation policy link · privacy link.

---

## 4. Business Rules

These are the rules the *system* must enforce. Where Wix handles it natively, it's marked **W**; where Velo is required, **V**.

### 4.1 Booking
- **W** Booking horizon: minimum 24h advance, maximum 6 weeks.
- **W** Buffer: 15 min between sessions (cleaning + reset).
- **V** New-client gating: a first session is held in `pending` until the intake form is submitted and the waiver is signed; auto-confirms within 1 minute of completion.
- **V** Service-area gate (mobile only): booking address is geocoded; if outside the 10-mile radius, the booking is denied with a "request a phone consult" fallback.
- **V** Returning-client fast path: members with a valid (≤365 day) intake skip the gate.

### 4.2 Cancellation & no-show
- **W** ≥24h before start: free cancel, full refund of deposit.
- **V** <24h: 50% session fee charged automatically against card on file.
- **V** No-show: 100% session fee charged automatically; client tagged `no-show` in CRM; second no-show within 12 months requires prepayment.
- **V** Late arrival ≥15 min: session ends on time at full charge; client offered same-week rebook with no late penalty.

### 4.3 Pricing & payment
- **W** Standard pricing: 60 min / $110 · 90 min / $155 · 120 min / $200.
- **W** Packages: 5-session 10% off · 10-session 15% off · monthly unlimited (cap 6/mo) at $440.
- **V** Mobile add-on: $30 base within radius; surfaced transparently in checkout via Velo Mapbox distance calc before payment.
- **W** Deposit: 30% taken at booking via Wix Payments; balance at session end.
- **W** Payment methods: card (Wix Payments), ACH, gift card, package balance.
- **W** Tax: included in displayed prices; itemized on receipt.

### 4.4 Intake & consent
- **V** Intake form is HIPAA-*aligned*, not certified — informed-consent + privacy disclosure + waiver in one. Stored in Wix Forms with restricted-access role.
- **V** Annual re-attestation: Velo cron emails every active client on their intake anniversary; one-click renew flow.
- **W** Waiver signature captured in form; PDF auto-generated and attached to client record.
- **V** Sensitive medical conditions (pregnancy, recent surgery, anticoagulants) flag the booking for owner pre-review before confirming.

### 4.5 Communication SLA
- **W** Booking confirmed → instant confirmation email + .ics calendar attachment.
- **W** 48h before → reminder email with intake check + parking/access info.
- **V** 4h before → SMS via Twilio (Velo); opt-out per CTIA rules.
- **W** Post-session → "how do you feel" email with optional tip prompt.
- **V** 4 weeks no booking → rebook nudge with one-tap link.
- **V** Birthday → 15% off code, valid 30 days.
- **V** Package fully consumed → renewal offer with last-purchase modality pre-selected.
- **V** Cancellation <24h → fee charge + waitlist auto-fill (see §6).

### 4.6 Privacy & retention
- Intake records retained 7 years per OR statute, then purged.
- Cookies banner per Wix native; analytics is GA4 with IP anonymization.
- Right-to-delete request via Contact form, 30-day SLA.

---

## 5. Wix Studio Stack

This is a **Wix Studio** site, not classic Editor. Stack:

| Component | Use |
|---|---|
| Wix Studio | Layout, breakpoints, CSS variables (analogue to McKenzie's `theme.json`) |
| Wix Bookings | Services, calendar, deposits, staff (single-staff config) |
| Wix Forms | Intake (multi-page, file upload), contact, waitlist signup |
| Wix Members Area | Auth, dashboard, billing, intake history |
| Wix Stores | Gift cards, package SKUs (consumed via Velo to credit Bookings) |
| Wix Payments | Cards + ACH; deposits; saved payment methods |
| Wix Automations | All native automations (see §7) |
| Wix CRM Inbox | Unified inbox for SMS replies + form submissions |
| Wix SEO | Page-level meta, sitemap, schema; Velo augments where Wix is sparse |
| Velo (Wix's IDE) | Custom modules in §6 |

---

## 6. Velo Custom Features (Upwork demand-aligned)

These are the seven custom-coded modules that distinguish the site from a stock Wix Bookings install. Each maps to a feature category that small-business clients pay Upwork developers $300–$2,500 to build.

### 6.1 Service-area gate
Booking flow injects an address-validation step for mobile services. Velo geocodes via Mapbox, computes distance from studio coordinates, and either confirms eligibility or routes to a phone-consult form. Eliminates manual back-and-forth.

### 6.2 Intake-completed gate (booking middleware)
Velo subscribes to the `BookingCreated` event. If the client has no valid intake on file, the booking is held in `pending` and the client is sent a one-tap intake link. The form's submission webhook fires `BookingConfirmed`. Without this, the front desk (i.e. Lena) becomes the gating step.

### 6.3 Package balance widget
Member dashboard reads a `Packages` Wix Data collection keyed to the member ID. Shows remaining sessions per package, expiration date, and a one-tap rebook CTA that pre-applies the package. Auto-decrements on session completion.

### 6.4 Smart rebook nudge
Velo cron runs daily at 06:00. For every client whose last session is 28 days old and who has no upcoming appointment, sends a templated email with a deep link prefilled to their last modality and length. Suppresses if the client opted out or is tagged `do-not-market`.

### 6.5 Mobile add-on calculator
On the Book page, when a mobile service is selected, Velo computes a transparent fee from the entered postal code via Mapbox distance API. Surfaced *before* checkout — never as a checkout-stage surprise. Caches results 24h to stay under the Mapbox free tier.

### 6.6 Waitlist auto-fill
A separate `Waitlist` collection stores members who opted in for earlier slots. When a cancellation occurs <24h, Velo notifies the first eligible waitlist entry by email + SMS with a 15-minute claim window. If unclaimed, advances to the next. Logs the chain for audit.

### 6.7 External CRM sync (toggleable)
For a future Hestia-managed multi-client variant, Velo webhooks new bookings + intake submissions to HubSpot or Mailchimp. Toggleable in a private admin page so the same codebase serves multiple Hestia clients without rewrites.

---

## 7. Automations

Built on **Wix Automations** where possible; Velo backstops the conditional branches Wix can't express.

| Trigger | Action | Engine |
|---|---|---|
| Booking confirmed | Confirmation email + .ics | Wix |
| 48h before session | Reminder email + intake check | Wix |
| 4h before session | SMS (Twilio) | Velo |
| Session marked complete | Follow-up email + tip prompt | Wix |
| 4 weeks no booking | Rebook nudge | Velo (§6.4) |
| Birthday | 15% off code | Velo |
| Package balance hits 1 | Renewal offer email | Velo |
| Annual intake anniversary | Re-attest email | Velo |
| Cancel <24h | Fee charge + waitlist notify | Velo (§6.6) |
| Form submitted: contact | Inbox routing + auto-acknowledge | Wix |
| Member signs up | Welcome series (3 emails over 7 days) | Wix |

---

## 8. Design System

Mirrors McKenzie's design DNA so both sites read as Hestia's Creations portfolio pieces. Different palette, same spatial system, same photo treatment.

### 8.1 Palette (Wix Studio CSS variables)

| Token | Hex | Use |
|---|---|---|
| `--sage` | `#7c8e7a` | Primary brand |
| `--sage-deep` | `#4f6650` | Secondary |
| `--river-stone` | `#aaa094` | Mid-tone |
| `--copper` | `#a05a2c` | Accent / CTAs |
| `--bone` | `#f4ede2` | Default bg (matches McKenzie) |
| `--bone-grain` | `#ece4d6` | Lighter |
| `--charcoal` | `#2a2f33` | Body text |

### 8.2 Typography
- **Display:** Fraunces (variable serif, optical 24, weight 600/800)
- **Body:** Inter (variable sans, weight 400/600)
- **Scale:** Display XL 96px / L 64px / M 44px / S 28px · Body L/M/S 19/17/15px · Meta 13px (fluid via clamp())
- **Letter-spacing:** tight (-0.02em), normal, wide (0.04em), widest (0.12em)
- **Line-height:** tight 0.95, snug 1.05, normal 1.4, loose 1.65

### 8.3 Spacing & layout
- **Spacing scale:** 8 / 16 / 32 / 64 / 128 / 192px (same as McKenzie)
- **Content width:** 720px editorial, 1280px wide
- **Breakpoints:** 480 / 768 / 1024 / 1440

### 8.4 Photo treatment
Same specimen-block CSS chain as McKenzie: `saturate(0.75) sepia(0.05) brightness(0.95) contrast(1.05)` + 4% paper-grain SVG overlay + edge fade-to-bone. Continuity is the point — when Hestia shows both sites in a portfolio review, the visual lineage reads at a glance.

### 8.5 Accessibility
- WCAG 2.2 AA target; tested with axe-core on every page pre-launch.
- Focus rings on every interactive element (`outline: 2px solid var(--copper); outline-offset: 3px`).
- `prefers-reduced-motion` honored.
- Min body 18px.
- ADA studio info on Contact page (door width, parking, scent-sensitive room).

---

## 9. SEO

### 9.1 Structured data
- `LocalBusiness` + `MassageBusiness` (sitewide, in head)
- `Person` (Lena, on About)
- `Service` per modality (on each detail page)
- `FAQPage` (Contact + every modality FAQ section)
- `BreadcrumbList` (every non-home page)

### 9.2 On-page
- Title ≤60ch, meta ≤155ch, OG image per page (Velo guards this in pre-publish)
- Canonicals + auto-sitemap
- Per-modality landing pages with intent-matched H1 ("Prenatal massage Springfield OR")
- Internal linking from blog → modality → booking

### 9.3 Local
- Google Business Profile NAP-consistent with footer
- Citations: Yelp, AMTA directory, OR LMT directory
- Reviews: Wix Reviews → schema → home page testimonial pull

### 9.4 Performance
- Targets: LCP ≤2.5s, CLS ≤0.1, INP ≤200ms (Wix Studio defaults usually meet this; Velo is the risk)
- Velo bundles lazy-loaded; Mapbox + Twilio calls server-side only

### 9.5 Content engine
- Blog at `/journal` (sage-bordered card grid)
- Velo cron prompts owner monthly with a topic from a curated rotation (recovery, prenatal, deskwork necks, etc.)
- 600–900 words target, internal-linked to ≥2 modality pages

---

## 10. Privacy, Legal, Compliance

- **Intake form:** privacy disclosure block at top, e-signature consent at bottom, version-stamped (any change re-prompts re-attestation).
- **Cookies:** Wix native banner, granular consent.
- **Cancellation policy:** linked from booking flow + footer + reminder emails (no surprise charges).
- **Liability waiver:** signed at intake, PDF on file, references OR ORS XXX.
- **Right-to-delete:** Contact form trigger, Velo flags client record, 30-day SLA, audit log retained 7 years.
- **PCI:** Wix Payments handles card data — site never touches PAN.
- **CTIA SMS compliance:** opt-in language at intake, STOP keyword honored, sending window 8am–9pm local.

---

## 11. Build Sequence (mirrors McKenzie WP-1..WP-7)

| WP | Scope | Done = |
|---|---|---|
| WP-1 | Wix Studio scaffold + design tokens (CSS vars + Fraunces/Inter) | Token preview page renders all swatches + type scale |
| WP-2 | Wix Bookings: services, hours, deposits, staff config | All 5 modalities bookable in staging; deposit flow tested |
| WP-3 | Forms (intake, contact, waitlist) + Members Area + permissions | New client can sign up, fill intake, see dashboard |
| WP-4 | Page templates + final content (8 public + 4 member) | All pages built from tokens; Lighthouse a11y ≥95 |
| WP-5 | Velo modules (§6.1–6.7) | Each module has a unit test + manual happy-path test logged |
| WP-6 | Automations + Twilio + Mapbox + analytics | All automations fire in staging with seeded clients |
| WP-7 | SEO, schema, GBP sync, perf pass, packaging | CWV green on 5 representative pages; portfolio writeup drafted |

---

## 12. Out of Scope (v1)

- Multi-therapist scheduling (single-staff only)
- Insurance billing / superbill generation
- HSA/FSA card processing
- Full SOAP charting (use Jane App if needed)
- Multi-language (en-US only at launch)
- Native iOS/Android (Wix mobile app only)

---

## 13. Open Questions

These need owner input before WP-1 starts.

1. Existing client list (size, format) for migration into Members?
2. Phone routing — Wix Inbox forward to mobile, or Google Voice?
3. Email domain on Google Workspace, or Wix-hosted?
4. Service-area exact polygon, or postal-code list (current draft assumes radius)?
5. Tip prompt: opt-in by default or off?
6. Which CRM (HubSpot / Mailchimp / neither) for §6.7?
7. Annual intake re-attestation grace window — 30 days, 60, or block at 365 + 1?

---

## 14. Companion-piece notes (Hestia portfolio context)

This spec deliberately rhymes with McKenzie at the *system* level so the two sites can be presented together:

| Dimension | McKenzie | Riverbend |
|---|---|---|
| Stack | WordPress block theme | Wix Studio + Velo |
| Driver | Emergency CTA (call now) | Booking + payment |
| Form backend | `mailto:` | Wix Forms + member portal |
| Aesthetic | Forest + oxblood, paper grain | Sage + copper, paper grain |
| Type | Roboto Slab + Source Sans 3 | Fraunces + Inter |
| Trust signals | ISA cert, bonded/insured | OR LMT license, AMTA, ADA |
| Build pattern | WP-1..WP-7 | WP-1..WP-7 |
| Hand-crafted detail | Hero copy, emergency band | Hero copy, modality detail pages |

The shared paper-grain treatment, identical spacing scale, and parallel build sequence are intentional. They let a prospect look at both sites and see *the same studio's hand* — different platform, different business, same care.

---

*End of v0.1.0. Next pass: owner walkthrough → resolve §13 open questions → cut v0.2.0 with WP-1 ready to start.*
