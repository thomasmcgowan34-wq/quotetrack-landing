# Landing page — how it is built, and why

Rebuilt 2026-09-09. Static HTML + CSS + a little vanilla JS. No build step, no
package.json, no framework, no animation library, no third-party script.

The design system lives in the **app repo**, and this page is a named consumer of
it (`docs/reference/design-language.md`, "Consumers"). It shares that document's
rules and the **values** in `tokens/design-tokens.json` — not the codebase. If a
token value changes there, change it in `assets/site.css` here too.

---

## Third pass — 2026-09-18 (take stock of all three versions; cut, then add)

Three versions existed at this point and the first job was to compare them:

| Version | State |
|---|---|
| **The live site** (`origin/main`) | Still the pre-refresh page: the rejected `#1a2e4a`/`#22c55e` pair, Inter and Inter Tight, and "pipeline" five times including in the `<title>`. 3,786px at 1280. |
| **`origin/site-refresh`** (Thomas) | Already on the correct brand, Schibsted Grotesk + Syne, **zero banned words**, and materially sharper customer copy than this branch had. Static — no motion at all — light-heavy, Google Fonts over CDN, and modal-gated forms. 6,102px. |
| **`main`** (this branch) | Dark-first, animated, correct — but 8,031px, unrelieved navy, and with an investor-flavoured tail a merchant has no use for. |

The conclusion was that Thomas's branch was **better on substance** and this one
was better on craft, so this pass cut the tail and adopted his substance.

### Cut, deliberately

- **The market-size section** (180,000 / 65,000 / 16,000). A merchant does not
  buy because the market is big; it is investor framing on a customer page, and
  it sat closest to the confidentiality line of anything here.
- **The roadmap as a full section.** "In build" and "Where it goes" were
  out-shouting the product that actually exists, and inviting *"so it does not
  do that yet"*. Now a three-item strip underneath the real screens.
- **The founder cards on `index`.** They are the whole point of `about.html`;
  repeating them cost ~500px for no new information. Replaced by one
  *Meet the founders* link.

### Adopted from `site-refresh`

- **"Live in weeks, not quarters. No data entry, ever."** — four rollout steps.
  The page had no answer at all to *what does this cost me to put in?*, which is
  the first question an IT manager asks.
- **"Built for trade suppliers"** — six verticals and three roles. The page never
  answered *is this me?*.
- **Quote creation now leads the product section** ("a quote used to take an
  afternoon, now it takes minutes"). It is half of what the product does and was
  buried inside the turn.

Both new sections are **light bands**, which also breaks up what had become an
unrelieved run of navy from the hero to the footer.

### Flair, and where it was spent

- **The hero now carries the product.** A live "Today" list sits beside the
  headline and assembles itself once — rows in, top row already actioned, one
  green sweep across the card. It is `aria-hidden`, because §product presents the
  same surfaces properly with text; this is the picture, not the content.
- **Kinetic headline** — each word rides up out of its own clipped box, 55ms
  apart. ⚠ The clip box is the **line box, not the glyph box**, so the first
  attempt sheared the descenders off "Putting" and "driving". Fixed with
  `padding-bottom: 0.2em` and a matching negative margin, so the rhythm is
  unchanged. 0.06em was not enough at 80px.
- **Hover lift** on the stat, flow, stage, role and step cards; step and vertical
  rows warm from `surface-subtle` to white.
- All of it is gated behind `html.js` and collapsed by `prefers-reduced-motion`.
  Nothing loops forever — something moving permanently in the corner of the eye
  makes a page harder to read, not more alive.

### 🔴 The focus-ring rule caught a live regression

The turn's active tab is a **green-filled control**, so it reproduced the exact
defect §3 below documents: the green ring measured **1.00:1 against the button's
own green fill**. `.duel__tab[aria-pressed="true"]` now takes `signal-deep` with
everything else brand-filled. This is the second time this class of control has
appeared and the second time it shipped wrong first — the rule is right, and it
needs to be applied whenever a control's fill is one of the brand pair.

### Measured after this pass

14 painted colours, all from the token set · **0 contrast failures** across both
pages at 375/768/1280 · 0 box-shadows · 0 radii over 12px that are not pills ·
0 text below 12px · every faded element sits at exactly 0, never partially ·
**axe-core 0 violations** on both pages at both widths · Lighthouse **100/100/
100/100 desktop, 99/100/100/100 mobile**. CLS moved 0 → 0.023 with the hero
animation, comfortably inside the 0.1 "good" band.

---

## Second pass — 2026-09-10 (the turn, and a tighter problem story)

Henry asked for two things on top of the first build: (1) a big focus on the
**problem → fix** arc the deck runs, and (2) an animated visual that takes a
business **from "as-is" to "supercharged"** — in a loop, on scroll, or on hover.
Thomas had also pushed a parallel `site-refresh` branch to the same repo, which
was mined for copy and for a couple of structural ideas, not adopted wholesale.

### What changed

- **§2 "The gap"** no longer carries the six-channel convergence diagram. It is
  now a sharp problem statement plus **four numbers** (deck slide 3, generalised):
  *Up to 3 hours* · *Under half* get a second contact · *Six* ways a request
  arrives · *None* chased by the ERP. The seven-days number stays in §3, where it
  is held as the single count-up moment — no figure appears twice.
- **§4 "How it works"** is now **the turn**: an animated `as-is → supercharged`
  before/after. It is the deck's slide 2 and slide 4 rendered as one morphing
  diagram — the same six channels, the same ERP, the same rep, but two states:
  `Today` (one rep, 2–3 hours by hand → ERP records it → *gone cold*) and
  `With QuoteTrack` (captures/prices/stages → ERP **unchanged** → *won*).
- **§7 "Why us"** gained the deck's positioning axis as a callout — *your ERP
  records what happened, QuoteTrack drives what happens next*.

### How the turn works

- Two self-contained `<figure>` panels share an identical skeleton (channels →
  engine → ERP → result). With JS + motion they overlap in one grid cell and
  **crossfade**; an auto-loop holds `Today` ~3.4s then `With QuoteTrack` ~4.4s,
  pausing on hover and on keyboard focus, and starting only once the section is
  in view. Two `aria-pressed` tabs let the reader flip it by hand.
- The inactive panel rests at `opacity: 0`, never at a partial value — text sits
  only at 1 or 0, so the "no faded text" rule holds. It is hidden with opacity +
  `pointer-events`, **not** `visibility`, so both states stay in the
  accessibility tree and screen readers read the before/after as one narrative.
- **Reduced motion / no JS** (`html.js` is not added either way): the two panels
  sit side by side (stacked under 760px) as a plain labelled comparison, and the
  tabs are hidden. Verified: the page reads complete, the loop simply never runs.
- The engine → ERP → result row lays out horizontally only when **the panel** is
  wide enough — a **container query** (`container-type: inline-size` on
  `.duel__panel`), not a viewport one. A viewport query crams three nodes into the
  side-by-side fallback, where each panel is only ~half as wide; measured
  2026-09-10: 1120px panel → 5 columns, 548px panel → one column.
- A thin green **sweep** crosses the stage on each flip, so the change reads as a
  transformation rather than a cut. Decorative, `aria-hidden`, and it can never
  run without `html.js` — so never under reduced motion.

### Confidentiality, checked against the deck

A term sweep of the deck's prospect names (Murdocks, Chadwicks, Kellihers, Rexel,
CORE, HPC, IPC, Beesley & Fildes, MD O'Shea, Trade Electric Group), the named
advisors, the €4.2m / €750k / price-ladder / ARR figures, and the named discovery
quotes returned **nothing** in either page or the CSS. Traction is shown through
the sanctioned signals only: *live in production with a paying design partner*,
South Coast Building Supplies, the Ignite programme, and *40+ discovery calls*.
Thomas's five discovery quotes (adopted in spirit) are all role-attributed.

---

## Files

| File | What it is |
|---|---|
| `index.html` | The page. Nine sections, in the deck's order. |
| `about.html` | Founders + the longer story. Same chrome. |
| `assets/site.css` | Everything. Tokens at the top, then chrome, then one block per section. |
| `assets/fonts/*.woff2` | **Schibsted Grotesk, self-hosted.** No Google Fonts request. |
| `assets/logo-*.svg`, `assets/icon.svg` | **Copied from the app repo's `public/` and `app/`.** Do not hand-edit — they are generated by `npm run logos:build` over there. |

---

## The three rules that shape almost every decision here

### 1. This page is dark-first. The app is not.

The deck palette was designed on navy: white is 18.70:1 on `#08131f`, the signal
green is 10.69:1, and the green is **1.75:1 on white — unusable**. The app has to
stay light because reps read dense quote data on it all day. A landing page is a
pitch, not a cockpit, so it is built on the ground the palette was designed for.

### 2. There are two greens and they are not interchangeable.

`--signal` `#39df79` on dark. `--signal-deep` `#1d733e` on light. The light
product panels in §5 ("Built and live") are genuinely light surfaces, so every
green inside them is `signal-deep` — that is why the mock "Won" button is a
different green from the rest of the page. This is correct, not a drift.

### 3. 🔴 The focus ring follows the ground it is drawn on — **and there is a third case**.

Two documented cases:

- **on a dark ground** → the green (10.69:1 on navy)
- **on a light ground** → the navy (18.70:1 on white)

The third case was measured on this page on 2026-09-09, and the first build got
it wrong exactly the way the app did. With `outline-offset: 0` the ring is drawn
on the page ground *and* hard against the control's own fill, so it has to clear
both. A control whose own fill is one of the brand pair defeats both rings:

| control | green ring | navy ring |
|---|---|---|
| green button on navy | **1.00:1 vs its own fill** — invisible | 1.00:1 vs the ground |
| navy button on light | 1.6:1 vs the ground | **1.00:1 vs its own fill** |

Every palette value was measured against both grounds. **`signal-deep` is the
only one that clears 3:1 in either direction** — 3.18:1 / 3.36:1 on dark, and
5.03:1 / 3.18:1 on light. Runner-up `#4e637a` is 3.02 / 3.54 and is not an
accent. So one rule covers both grounds and no third ring colour was invented:

```css
.btn--primary, .skip-link, .section--light .btn--primary {
  --focus-ring: var(--signal-deep);
}
```

`--focus-ring` is an inherited custom property, so a light panel flips the ring
for everything inside it with one declaration.

---

## Two findings that came out of building this

**`tabular-nums` in Schibsted Grotesk also tabularises the comma and the full
stop.** Measured: with `font-variant-numeric: tabular-nums`, `€47,000` renders as
`€47 , 000` and `seat.` renders as `seat .`. On this page tabular figures are
therefore applied **only to the two numbers that count up in place** (`.bignum`,
`.counter b`), where they stop the digits jittering. Everything else is
proportional. ⚠ The app applies `tabular-nums` globally on `body`, so it has this
artifact everywhere — visible in the committed baselines in `scripts/baselines/`.
Worth a look over there.

**An absolutely-positioned decorative overlay puts everything under it into an
automated-contrast blind spot.** The hero's tick rule started as an overlay
element; axe then reported `color-contrast` as *incomplete* for all 12 hero
elements — "background could not be determined because it is overlapped". The
most important text on the site could not be checked by any automated guard.
Moving the ticks to `.hero`'s own `background-image` cut that to 5. A
pseudo-element made it worse (back to 12). This is the category
`design-system-guards.md` is about: what a guard *cannot see*.

---

## Motion

Two mechanisms, split on a principle:

- **Content reveals use a one-shot IntersectionObserver.** Not a `view()`
  timeline. A view timeline makes visibility a function of scroll position, so
  text un-reveals on the way back up and anything below a deep link lands at
  opacity 0. Copy must never depend on where the scrollbar is.
- **The rails and the convergence draw use CSS scroll-driven animations**
  (`animation-timeline: scroll()` / `view()`), with an IntersectionObserver
  fallback. These genuinely represent scroll position, and all of them are
  `aria-hidden` decoration.

The hidden-until-revealed state is opted into by `html.js`, which is only added
when `IntersectionObserver` exists **and** motion is not reduced. So:

- **JS off** → no `.js` class → the page renders complete. Verified: identical
  `innerText` length (6788 chars) with JS on, JS off, and reduced motion.
- **`prefers-reduced-motion: reduce`** → no `.js` class, plus a `!important`
  block that collapses every animation and forces every rail and the convergence
  to their final state. The scroll rail is hidden.
- **Count-ups** carry their final value in the HTML; JS resets to 0 and animates.
  With JS off or motion reduced the numbers are simply correct and static.

UI transitions use the system's three durations only (`--motion-fast` 150ms,
`--motion-base` 250ms, `--motion-slow` 300ms). The scroll-linked timelines and
the two count-ups are longer, which the brief allows for a landing page.

---

## Copy rules that are not optional

- **Never "pipeline"** → "open quotes". Never "dormant" → "needs following up".
  The old page used "pipeline" five times including in the `<title>`.
- **Discovery quotes are attributed by role only** — never by name or company,
  unless that partner has agreed. South Coast Building Supplies is a public
  reference and may be named.
- Real typographic characters: `…` `—` and curly quotes, never ASCII lookalikes.
- The villain is the missing tooling, never the customer, their people or their
  systems. Don't attack ERPs or CRMs — they are future integration partners.

---

## Measured, 2026-09-09

- **14 painted colours**, all from the token set: `#08131f #111827 #121f2e
  #1d3046 #1d733e #24384e #31506c #39df79 #4e637a #8ca0b8 #aebfcf #b9c4d3
  #e8eef5 #ffffff`.
- **0 contrast failures** across both pages at 375/768/1280. Lowest text pair
  5.30:1 against a 4.5 floor.
- **0 box-shadows. 0 radii over 12px** that are not full pills.
- **0 text below 12px. 0 text resting under full opacity.**
- **axe-core: 0 violations**, both pages, both widths.
- **Lighthouse** — index desktop 100/100/100/100, index mobile 96/100/100/100,
  about desktop 100/100/100/100.
- **No horizontal overflow** at any of the three widths. The only horizontal
  scroll is the deliberate roadmap track in §6 below 900px, which is keyboard
  reachable (`tabindex="0"`, `role="group"`).

Re-measure rather than trusting these numbers cold.

**Re-verified 2026-09-10 after the second pass:** no console errors; no horizontal
overflow at 375/768/1280 (scrollWidth ≤ clientWidth); the duel panels overlap
pixel-exactly and the crossfade toggles opacity 1↔0; the auto-loop cycles
`Today → With QuoteTrack → Today`; no raw hex was added (still the 15 token
values); the banned words ("pipeline"/"dormant") remain absent from both pages.
The new text pairs all clear 4.5:1 by computation — the closest are the uppercase
kickers (`#8ca0b8` on `#08131f`, 6.98:1) and `#aebfcf` on `#121f2e` (8.84:1).
