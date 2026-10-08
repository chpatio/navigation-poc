# Salesforce Digital Marketing Design System

The internal UI Library that Salesforce's marketing and web teams use to compose salesforce.com-style marketing surfaces — marquees, blades, card grids, forms, chrome. Not the product-app Lightning Design System.

## 1 · Using it

Load `styles.css` for the tokens, then the compiled bundle for the components:

```html
<link rel="stylesheet" href="styles.css">
<script src="_ds_bundle.js"></script>
<script>const { CTAButton, Card, Blade } = window.SalesforceDigitalMarketingDesignSystem;</script>
```

`styles.css` pulls, in order: `tokens/` (palette, aliases, backgrounds, type, space, effects, fonts) → `base.css` (global CSS) → `blocks.css` (block layout cascades). The Figma mirror is **not** in that chain — see §2. Set the theme axes on `<html>`; everything else follows from the cascade.

## 2 · Sources & precedence

Three sources describe this system. When they disagree, this order decides — and every live conflict is tabulated in the **Sources → Source reconciliation** card rather than resolved silently.

1. **Figma — `SFDC_UI Library 2.0 Brand Refreshed.fig` → token VALUES.** The `Semantic Colors` (69), `Space` (77), `Shape` (22), `Elevation` (32) and `Typography` (40) variable collections are the numeric truth. Every variable's default mode is mirrored verbatim into `tokens/figma/fig-tokens.css`, which is a **reference file, not part of the cascade** — `styles.css` does not import it. It carries the kit's raw names (`--blue-blue-20`, `--yellow-95-2`, `--surface-container-1`), so importing it put every colour in the system under two or three live names at once, differing only by prefix or a `-2` suffix. Nothing read them. The curated `--color-*` layer is the single live source. The file's **alternate modes** — the legacy `#0176d3`-era `Themes` collection, the raw `night` mode, the RTL flag and the three breakpoint-metadata modes — are parked verbatim in `tokens/figma/modes.reference.md`, outside the cascade: as `:root[data-*]` rules they registered as switchable themes, and this system has exactly one theme axis.
2. **`dms-design-tokens` skill → NAMES, structure, rules.** Flat `--{family}-{role}` naming, the two-layer primitive → alias model, the theme axes, the hard rules and recipes.
3. **WES Components → component API.** Props, variants and state machines for `Button`, `ButtonLink`, `IconButton`, `Badge`, `Chip`, `Avatar`.

**Which source owns a component:** if the family exists in Figma, Figma owns its anatomy, variants and states. If it doesn't, WES owns the API. The skin always comes from the token layer — no component reads a WES `--dms-s-*` name or its own literal, so a WES drift can only ever be an API drift, never a visual one.

## 3 · Tokens

Two layers, flat-hyphenated, no prefix:

- **Layer 1 — primitives**: literal hex / rgba / gradient, named for what they are (`--color-electric-blue-50`, `--space-8`, `--radius-2`). The only place a literal appears, and the only live name for a given value — a colour has exactly one primitive, never a WES spelling and a Figma spelling alongside it. `tokens/palette.css` carries **every** colour ramp in the file — 245 steps across 15 families plus alphas.
- **Layer 2 — aliases**: `var()` references named for where they're used (`--color-fill-accent`, `--space-content-lg`, `--radius-card`, `--font-style-display-2-size`). **Components read only these.**

**One theme axis**, set on `<html>`: `data-theme` day | night. It re-binds colour aliases to different primitives and introduces no literal. `[data-theme="day"]` also works as a scope — put it on a card, header or footer that must stay light inside a Night page.

Spacing, type scale and radii are **fixed**: one ladder each, no alternate modes. Earlier `tight` / `comfy` / `modern` / `sharp` axes were removed — they surfaced as switchable options that nothing in the kit actually calls for, and a block is a component like any other, not a density surface.

**Semantics v2.0 is the only grammar.** Numbers count, words name: a numeric suffix is a pure increment (`surface-container-1`, `border-2`, `fill-disabled-3`), a word suffix carries meaning (`-hover`, `-active`, `-error`), and the resting slot has **no suffix** — the family name itself is the default. Bounded fill / on-fill / border families ship an explicit three-state ladder (`fill-accent` → `-hover` → `-active`); extensible surface-containers ship one base token and get their states from the `.has-state-layer` overlay. The v1 numbered-state ladder (`fill-accent-1..4`) is gone, and no alias carries two spellings for one role. Three collisions were retired to get here: `on-surface-subtle` (a second name for `on-surface-3`), the bare/`-1` twins on the feedback fills (`fill-error` vs `fill-error-1`, plus the unused `fill-success-1` / `-warning-1` / `-info-1`), and `special-use-case-fill-1..4`, a numeric ladder that was really encoding states. Error is the one two-step feedback family — `-1` subtle container, `-2` bold fill, each with its paired ink; success, warning and info are single-step and unsuffixed. The state layer runs Option α: achromatic overlay, black in Day and white in Night, and `active` means the latched ARIA state (`aria-pressed` / `-selected` / `-checked` / `:checked` / `aria-current`), not mid-click.

Files: `palette.css` (all ramps) · `colors.css` (semantic aliases + gradients) · `backgrounds.css` (blade surfaces) · `typography.css` · `spacing.css` · `effects.css` (radius, border widths, elevation, motion) · `fonts.css` · `figma/fig-tokens.css` (reference only).

## 4 · Foundations

- **Color** — a 9-step blue spine (Cloud Blue 95→68, Electric Blue 50→10) with Electric Blue 50 `#066afe` as the single accent; `blue-*` is the kit's second, cooler blue, used for chrome ink. Four speciality families (Teal, Yellow, Pink, Violet) as tints and gradient stops only, never large solid fields. A 19-step neutral ramp carries text and surfaces. Feedback intents each ship a Day ink, Night ink, 95% soft surface and a solid `*-2` step.
- **Type** — two families: Avant Garde For Salesforce (display, 600 only) and Salesforce Sans (body 400 / 400i / 700). One nine-step Display ladder (80px → 16px) and a five-step Body Copy ladder (20px → 12px), each Body step shipping a matched Bold cut at identical metrics. Line-height runs three multipliers by scale: 1.125 tight (40px+), 1.25 snug (24–32px), 1.5 normal (body and UI). Letter-spacing is em-based and tightens as size grows.
- **Headline tiers** — only the hero marquee reaches Display 2. Every body blade stops at Display 4; a blade that escalates reads as two heroes stacked.
- **Spacing** — a 17-step 4px ladder exposed as five semantic families: `interactive` (control padding, tuned to a 44px target), `content` (in-block rhythm), `surface` (band vertical padding), `container` (bounded elements), `layout` (margins, gutters, gaps). Grid geometry chains to the layout aliases — see §5 for the page grid itself.
- **Radii** — five tiers, nested smaller-inside-larger only: 16px hero containers, 12px cards, 8px small cards, 4px inputs, full pill for buttons, chips, avatars, badges. (`Chip` drew itself at the 4px input radius against that rule until it was corrected to `--radius-chip`.)
- **Elevation** — four levels mirrored from the Figma `Elevation` collection, built from its own X / Y / Blur / Spread variables: Level 1 inset chrome, Level 2 cards at rest, Level 3 hover and floating surfaces, Level 4 menus and overlays. Plus a press step and one decorative accent shadow (never on interactive or overlay chrome). Fills invert to white veils in Night.
- **CTA edge** — CTAs carry **no drop shadow**. The primary pill is two layers: an 85° gradient rim (`--gradient-cta-rim`) at full size with the solid fill 1.5px inside it, so the edge reads as light catching the pill rather than elevation. Free-trial green is header chrome only.
- **Motion** — 150ms ease-out for colour, border and shadow; a 2px lift + 1.02 scale on hover; 0.98 scale on press. No bounce, no springs, no long fades.
- **Backgrounds** — `tokens/backgrounds.css` is the catalogue of surfaces a blade may paint, as `--bg-*` aliases: **13 Day solids** (Neutral 100 plus the 95/90 tints of cloud-blue, blue, yellow, hot-orange, orange, pink, green, teal, violet, indigo, purple, aubergine), **16 Day gradients** (Morning, Midday, Dusk, and Teal / Pink / Violet / Yellow 1 and 2 — each `-1` also shipping a `-flipped` variant), **24 Day tint fades** (each of the 12 accent tints dissolving to transparent from the top or the bottom edge, for meeting a photograph or an adjacent band without a seam), **14 Night solids** (the 20/30/10 steps) and **5 Night gradients** (Evening plus Galaxy in four directions). One background per blade, never two stacked gradients, never a background on `body` or `main`; Night surfaces only inside `data-theme="night"`; solid accent tints are page-level moments, not card fills; flipped gradients exist so two adjacent gradient blades meet light-to-light; **tint fades end at `alpha 0`, not at white**, so they can sit over a photograph or the blade below.
- **Stacked rows carry their own rule** — a column of disclosure rows (FAQ, filter facets, vertical tabs) gets its rhythm from the row, not from a gap on the stack: each row is a 44px target with a hairline beneath, the stack has no gap, and the container switches the divider off on the last row so nothing hangs. A gapped stack with a divider on every child reads as a rule glued to each label followed by a hole.
- **Card rows are equal height** — every card in a row is as tall as the tallest card in that row, always. A row is a flex container (so the wrappers stretch) *and* the card fills its wrapper: `.sf-cards-row > *` becomes a flex box, `.sf-cards-row > * > *` grows into it. Never `height: 100%` on the card — it collapses inside a grid track and clips the Level 2 shadow. `CardGrid`, `CardCarousel` and `CardMosaic` all carry the rule; masonry is the one exemption, since it flows columns at natural heights by design.
- **Contours** — three bottom-edge shapes (asymmetric, concave, convex) cut a blade against the next band. A contour is a **cut-out, not a decoration**: paint it in the colour of the surface *below* so the two bands read as one continuous shape. **A contoured blade reserves the shape's height as bottom padding** (`--space-page-surface-padding` + `contourHeight`, 80px by default) — the contour is absolutely positioned over the band, so without the reservation it crops the last row of content: CTAs and asset panels get sliced by the wave. The Marketing landing template enforces this — `contourColor` is derived from the next band's fill rather than authored separately, so changing a background cannot leave a contour stranded in the wrong colour.
- **Night** — an opt-in `data-theme="night"` for bands that need a darker moment, not a full dark mode. Cards stay light islands on Night blades.

## 5 · Page grid

Every page is one grid. Five breakpoints, a 12-column field (6 on mobile), and **one content cap of 1280px that holds at every breakpoint** — the band paints edge to edge, the content inside it never exceeds 1280px and centres in whatever room is left.

| Breakpoint | Viewport | Figma frame | Columns | Margin | Gutter | Band padding | Content cap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Large desktop | ≥ 1600 | 1600 | 12 | 48 | 40 | 32 | 1280 |
| Desktop | ≥ 1440 | 1440 | 12 | 48 | 40 | 32 | 1280 |
| Tablet landscape | ≥ 1024 | 1024 | 12 | 48 | 32 | 32 | 1280 |
| Tablet portrait | ≥ 768 | 768 | 12 | 32 | 24 | 24 | 1280 |
| Mobile | < 768 | 375 | 6 | 24 | 24 | 24 | 1280 |

**Column width follows from the cap, not the viewport.** At desktop: (1280 − 11 × 40) ÷ 12 = **70px** per column, the fixed rhythm the kit is drawn on. Below the cap the columns go fluid. Compose any element's width as `N × column + (N−1) × gutter` — a half-blade is 6 columns, a 3-up card is 4.

**The four tokens a layout actually reads** resolve to the active breakpoint on their own, so no component and no block CSS repeats a breakpoint:

- `--space-page-margin` — outer inline padding on the band
- `--space-page-gutter` — space between columns and between cards
- `--space-page-surface-padding` — band vertical padding
- `--space-grid-max-content-width` — the 1280px cap, breakpoint-independent

The per-breakpoint `--space-grid-<breakpoint>-*` tokens stay as the published spec (and are what the table above lists), but layout code should not reach for them.

**A band is two elements.** The outer `<section>` carries the background and `padding-inline: var(--space-page-margin)`; the inner div carries `max-width: var(--space-grid-max-content-width); margin-inline: auto`. Never cap the outer element — the background has to reach the viewport edge.

**The 1280px cap applies to content bands only — global chrome is exempt.** `SiteHeader` and `SiteFooter` run their rows full-bleed inside `--space-page-margin` and carry no cap. Chrome is a different class of surface: a capped header bunches the nav and utility cluster in the middle of a wide screen while its border rule runs edge to edge, and it starves the nav of room, folding links into "More" hundreds of pixels before it needs to. Anything else — every blade, card band, form and content section — takes the cap.

Do: design to 12 columns (6 on mobile); take gutters from the alias slice; let a prose block nest a narrower reading measure inside the grid (`AccordionList` caps its copy at 820px). Don't: hardcode a px margin or gutter; invent an intermediate gutter (28, 36); cap the band instead of its content; design to 7, 9 or 11 columns.

**CTA separation** — a CTA block sits **48px** below the content above it (twice a frame's normal content gap); horizontally placed CTAs sit **24px** apart from each other. Use `.sf-cta-row`, which adds only the difference between 48px and the active content gap, so the measured distance holds at every breakpoint.

## 6 · Base and block CSS

- **`base.css`** — the only global CSS: link defaults, one focus ring, the `.sf-icon` mask utility for plain-HTML contexts, `.has-state-layer`. It does **not** restyle components.
- **`blocks.css`** — the page-section layout cascades, in one file because media queries can't live in an inline style. Breakpoints match the kit's grid: mobile <768 · tablet-portrait 768–1023 · tablet-landscape 1024–1439 · desktop ≥1440.

*(A `.sf-*` class layer mirroring the components used to exist and was removed: a parallel implementation of the same chrome is a drift risk, and this system is consumed through the component bundle.)*

## 7 · Components

Plain React function components under `components/<group>/`, styled entirely through the aliases — zero hex literals in component source. **67 public components** (79 compiled exports, the remainder internal helpers like `FieldShell` and `ctaSkin`).

**One CTA, not two.** `components/buttons/ctaSkin.jsx` holds the single construction, geometry and state ladder. `CTAButton`, `Button`, `ButtonLink` and `IconButton` all render through it; WES names resolve onto it (`destructive` → `danger`, size `default` → `medium`).

**One pagination control.** `PaginationButton` is it — the numbered/prev/next control, composed into a row by `DataTable` and `ListView`. A carousel's position dots are carousel chrome, private to `CardCarousel`, not a second component sharing the name.

**One field skeleton, one selection control.** `components/forms/FieldShell.jsx` holds the text-field matrix (1px resting / 2px engaged, inset focus ring, error / success / warning ladder) used by `TextInput`, `TextArea`, `Select`, `ComboBox`, `Password` and `PhoneInput`. `Checkbox` and `RadioButton` share one box construction — 16×16, inset ring, accent fill when selected, and the kit's two-stop offset halo on keyboard focus. Controls are fluid; the caller owns width.

**Primitives**

- **Buttons** — `CTAButton` (primary / secondary / tertiary, 3 sizes), `UtilityButton`, `CTATextLink`, `IDPButton`, `Button`, `ButtonLink`, `IconButton`
- **Forms** — `TextInput`, `TextArea`, `Select`, `ComboBox`, `ComboBoxListItem`, `Password`, `PhoneInput`, `Checkbox`, `CheckboxGroup`, `RadioButton`, `RadioGroup`, `Switch`, `FormElementGroup`, `DropdownLabel`, `ShowHide`
- **Navigation** — `Menu`, `MenuItem`, `Disclosure`, `CarouselButton`, `ScrollBar`
- **Feedback** — `Badge`, `Chip`, `Pill`, `Status`, `Tooltip`, `PaginationButton`
- **Icons** — `Icon`

**Composites**

- **Cards** — `Card`, `ComposableCard`
- **Content** — `HeaderBlock`, `CopyBlock`, `PersonalizedGreeting`
- **Media** — `AssetBlock`, `AssetPanel`, `Background`, `BottomContour`
- **People** — `ProfileAvatar`, `Avatar`, `AuthorByline`
- **Social** — `SocialShare`, `SocialButton`

**Blocks — page sections**

- **Bands** — `Blade` (generic / single-column / two-column), `Banner`, `Spacer`
- **Chrome** — `SiteHeader`, `SiteFooter` (both permanently Day-scoped; the header's nav collapses into a "More" menu rather than wrapping, and its green button is the system's Special CTA)
- **Card bands** — `CardGrid` (owns the four grid safety nets and the orphan-free column cascade), `CardCarousel` (carousel / ticker / dots), `CardMosaic` (inline-grid / masonry), `LogoWall` (wall / logo-grid)
- **Content sections** — `TabbedContent` (horizontal / vertical-tabs), `AccordionList`, `AgendaList`, `FormBlock`
- **Host slots** — `MfeSlot`, the mount band for a micro-frontend another team delivers (agenda builder, session calendar, pricing calculator, Agentforce panel). The host owns the band — background, width mode, reserved min height, intro copy; the MFE owns everything inside the frame. Four authored states — `placeholder` (the handoff artefact: slot id, owning team, reserved height and width stated on the page), `loading` (pulsing blocks at the reserved height), `error` (contained failure that names the section, not the stack, and says the rest of the page still works) and `ready` (children render untouched) — so a host page is reviewable before the MFE exists.
- **Data and filtering** — `DataTable`, `ListView`, `SearchBar` (bar / search-system / active-filters), `FilterPanel` (rail / floating), `Notifications`

Families that differ only in chrome or density share one component with a variant prop rather than duplicating code.

## 8 · Templates and UI kit

- `templates/` — five **whole-page** starting folders a consuming project copies, one per page intent. Each composes the real chrome and the real section blocks; none is a single-band demo (the earlier Hero Blade / Card Grid / Page Shell were exactly that, so they were removed — a one-band fragment belongs in a component card, not the template picker).
  - **Marketing landing page** — `SiteHeader` → contoured two-column hero marquee → 3-up `CardGrid` → proof band → `CardCarousel` → `AccordionList` → `SiteFooter`. Tweaks: a background per band, contour shape, card and carousel column counts.
  - **Resource library** — the search-and-browse utility intent. `SiteHeader` → a **centred search hero** on a Morning gradient (Display 2 headline, one line of framing, a pill search field with an inline Search CTA, and a centred wrapping row of category `Chip`s with one selected) → the library surface: `FilterPanel` rail ("Filter by", facets collapsed) beside a `count Results` + sort `Select` line and a 3-up result-card grid (16:9 image, `Badge`, title, copy, `CTATextLink` pinned to the card foot) with a pagination row → a closing "not finding it?" blade → `SiteFooter`. Tweaks: result count, rail vs floating facets, closing-band background.
  - **Form page** — conversion intent. `SiteHeader` → `FormBlock` above the fold → a three-column "what happens next / no obligation / your data" reassurance row → `LogoWall` → an objection-answering `AccordionList` → `SiteFooter`. Tweaks: submit label, split vs stacked form, FAQ background.
  - **Event landing page** — logistics intent. `SiteHeader` (CTA "Register") → contoured dated hero → a four-up facts band (days, sessions, format, price) → `AgendaList` → 4-up speaker `CardGrid` → registration `FormBlock` → `SiteFooter`. Tweaks: hero background and contour, speaker-band background, submit label.

  - **MFE host page** — the composition intent: page chrome around work another team owns. `SiteHeader` → contoured hero (chrome sets the promise and the one action that precedes the tool) → primary `MfeSlot` → optional second `MfeSlot` for a genuinely separate tool with its own owner → a `AccordionList` support band → `SiteFooter`. Tweaks: slot state, use case / slot id / owner, width mode, framed, reserved min height, second-slot toggle, band backgrounds. Use it for any page whose middle is a micro-frontend rather than authored bands; a page with two slots that split one experience should be one slot instead.

  Copy in all five is deliberately neutral placeholder text, so a consumer replaces words rather than untangling someone else's campaign.
- `ui_kits/marketing-site/` — a click-through marketing page that **composes** the components (header, marquee, product strip, card band, demo band, footer) rather than reimplementing them

## 9 · Iconography and assets

Single-colour glyphs render through `Icon` (React) or `.sf-icon` (HTML): a span whose `background` is `currentColor` and whose `mask-image` is the glyph, so it takes the ink of its container — white in a Primary CTA, Blue 30 in a Secondary, flipping automatically in Night. **166 glyphs** from the kit's General Use library are inlined as data URIs in `components/icons/icon-data.js` (split across four `icon-part-*.js` files for size). Mask URLs resolve against the document, so relative paths silently broke on pages at other depths and every icon rendered as a solid square — hence the inlining.

Never use `<img>` for a single-colour glyph. The `social-*` marks mask too, which is how the footer draws white marks on dark tiles — never `filter: invert()`, which turns the brand blue yellow. Multi-colour art (`product-*`, `eyebrow-*`) keeps its own palette and stays `<img>`.

The **Components → Icon library** card renders every glyph from the live data, so the gallery cannot drift from what ships. `Avatar` derives initials from `label` (or takes a `symbol`) because the kit ships no person glyph as a flat shape; the loading affordance is the kit's three-dot pulse, drawn rather than masked.

The **full-colour brand marks** (Salesforce cloud, Night cloud, compact bug) are inlined as data URIs in `components/icons/brand-marks.js` for the same reason as the glyphs: a relative `src` resolves against the *consuming document*, so global chrome silently lost its Salesforce mark on any page not exactly two levels deep. `SiteHeader` and `SiteFooter` accept `logoSrc` / `bugSrc` to override. Sample-imagery defaults on `Card`, `AssetBlock`, `AssetPanel` and `ProfileAvatar` are not chrome, so they resolve through `assetUrl()` in `components/asset-base.js`, which anchors on the `_ds_bundle.js` script location and honours `window.__SFDS_ASSET_BASE__`.

Brand webfonts are self-hosted from `fonts/` via `tokens/fonts.css`: Avant Garde For Salesforce (600) and Salesforce Sans (400 / 700 / 400-italic).

## 10 · Content rules

Sentence case everywhere; uppercase + letter-spacing only for eyebrows and badges. Second person, benefits-first headlines that lead with the reader's outcome, not the product name. CTA labels are short action phrases — "Get started", "Watch demo", "See details" — never "Click here". No emoji. Numbers only when sourced from real product data.

## 11 · Scope

**Tokens: complete** — all 844 Figma Variables accounted for (defaults mirrored in CSS, alternate modes in `modes.reference.md`), the curated layer reconciled against them.

**Components: 79 compiled; the rest of the kit's 2956 "families" is deliberately out of scope** and the count double-counts. The 2956 is every symbol in the file — 526 component sets plus 2510 standalone symbols — not 2956 things a designer would call a component. Skipped, by name:

1. `_`-prefixed Figma internals with no standalone use. The four that have one are built as `Status`, `FormElementGroup`, `Disclosure` and `ComboBoxListItem`; `_Single selection` and `_Menu Item with Checkmark` are covered by `RadioGroup` and `MenuItem`.
2. Duplicate inventory rows — `02 Atoms/CTA Button` appears twice for one built component, and several other `02 Atoms/*` and `03 Molecules/*` rows repeat.
3. `01 Base Styles/Iconography/General` and the Products Library — icons are assets, not components. 166 glyphs ship as masks; product and industry marks are image files (still to import: their artwork is layered across nested vector groups rather than one flat shape).
4. Pure example and demo frames.

**Known gaps** — no accessibility pass yet (no contrast audit, no `prefers-reduced-motion` despite the hover transforms); Night is tokenised but lightly exercised; the blocks were built from the recipe and the production CSS rather than verified variant-by-variant against Figma.

## 12 · File index

- `styles.css` — root stylesheet (import order above) · `base.css` · `blocks.css`
- `tokens/` — the canonical token set · `tokens/figma/` — the verbatim variable mirror + `modes.reference.md`
- `components/` — see §6 · `components/icons/` — the mask library
- `assets/` — logos, icons, contours, imagery · `fonts/` — brand webfonts
- `guidelines/` — foundation cards, incl. **Sources → Source reconciliation**
- `templates/` · `ui_kits/marketing-site/`
- `SKILL.md` — portable skill for Claude Code and other agent contexts
