# Widget Catalog

The complete catalog of reusable widgets for the static corporate website. Widgets are implemented by skill 05 and composed by page skills 07–12.

**Contracts (all widgets):**

- **Content:** received via props from the page (which reads `content/*.ts`) — widgets never import content directly.
- **Theme:** tokens + component recipes from the design system only.
- **Responsive:** mobile-first; every widget defines its mobile behavior.
- **Accessible:** semantic HTML, keyboard operable, focus-visible states, ARIA where needed.
- **Animation:** uses the animation system's primitives (skill 06); `prefers-reduced-motion` respected.
- **Variant strategy:** a variant prop tunes the look; composition with layout primitives handles structural differences.

Legend for attributes per widget: **Purpose** · **Use cases** · **Structure** · **Content requirements** · **Variants** · **Interaction** · **Responsive** · **Accessibility** · **Animation** · **When NOT to use**.

---

## Navigation

### HeaderSimple
- **Purpose:** Primary site header with logo + inline nav links.
- **Use cases:** Default header for most pages; content sites with few nav items.
- **Structure:** `header > Container > [logo] [nav] [CTA button]`.
- **Content requirements:** Logo (or placeholder), 3–7 nav links, optional CTA label.
- **Variants:** `sticky` off/on, `transparent` over hero, `elevated` with border/shadow, `dark`/`light` surface.
- **Interaction:** Hover/focus on links; scroll behavior (see variants).
- **Responsive:** Nav collapses to a mobile trigger at the configured breakpoint.
- **Accessibility:** `header` landmark, `nav` with aria-label, visible focus, skip-link target provided by layout.
- **Animation:** Header slide/fade on scroll; link underline micro-interaction.
- **When NOT to use:** When the site needs mega-menu or multi-level navigation (use HeaderMegaMenu).

### HeaderTransparent
- **Purpose:** Header that overlays the hero with no background, becoming solid on scroll.
- **Use cases:** Full-bleed image/video heroes where the header sits on the image.
- **Structure:** `header` (position absolute) > `Container` > [logo] [nav] [CTA].
- **Content requirements:** Same as HeaderSimple; plus a solid-header threshold.
- **Variants:** `auto-solid` (gains background on scroll), `always-transparent` (dark hero only), `dark-text`/`light-text`.
- **Interaction:** Scroll-driven state change.
- **Responsive:** Mobile trigger must remain usable over imagery (scrim behind).
- **Accessibility:** Contrast guaranteed in both states; ensure readable text over images.
- **Animation:** Background fade-in on scroll.
- **When NOT to use:** On pages with light, short heroes where contrast cannot be guaranteed.

### HeaderSticky
- **Purpose:** Header pinned to the viewport while scrolling.
- **Use cases:** Long pages where persistent navigation aids wayfinding.
- **Structure:** `header` (position sticky) > `Container` > [logo] [nav] [CTA].
- **Content requirements:** Same as HeaderSimple.
- **Variants:** `always-visible`, `hide-on-scroll-down` (reveal on scroll-up).
- **Interaction:** Scroll-aware show/hide.
- **Responsive:** Same mobile collapse as HeaderSimple.
- **Accessibility:** Never trap content; keep skip-link functional with sticky header.
- **Animation:** Slide up/down transitions, no jank (use `transform`, not layout props).
- **When NOT to use:** When combined with a full-screen overlay nav on mobile (avoid double stickiness).

### HeaderMegaMenu
- **Purpose:** Header with a large multi-column dropdown for rich navigation.
- **Use cases:** Sites with many sections (services + products + industries + resources).
- **Structure:** `header` > `Container` > [logo] [nav with trigger buttons] [CTA]; megamenu panel per top-level item.
- **Content requirements:** Nav taxonomy (groups, links, featured items, descriptions).
- **Variants:** `grid-panel`, `list-panel`, `featured-card` panel, `full-width` vs `container-width`.
- **Interaction:** Hover and click/keyboard open; outside-click close; escape close.
- **Responsive:** Megamenu collapses into the mobile navigation.
- **Accessibility:** Full keyboard support (arrow keys), `aria-expanded`, `aria-controls`, focus trapping within the open panel, no hover-only activation.
- **Animation:** Panel fade/slide with small stagger.
- **When NOT to use:** For 3–5 simple links (HeaderSimple is better); on mobile-only sites.

### MobileNavigation
- **Purpose:** The mobile menu: trigger button + full-screen (or drawer) nav.
- **Use cases:** Every site with a header; required when nav exceeds the compact layout.
- **Structure:** `button[aria-expanded]` + overlay panel containing nav links, CTA, contact info.
- **Content requirements:** Same nav taxonomy as desktop + optional secondary links.
- **Variants:** `full-screen`, `drawer-left`, `drawer-right`, `sheet-bottom`.
- **Interaction:** Tap trigger; close on link click, overlay click, Escape; lock body scroll while open.
- **Responsive:** Hidden above the breakpoint; full behavior below it.
- **Accessibility:** Focus moves into the panel, trapped while open, restored on close; `aria-expanded`; reduced-motion respected.
- **Animation:** Panel slide/fade; staggered link reveal.
- **When NOT to use:** When the nav fits in the header at all breakpoints.

### Breadcrumbs
- **Purpose:** Hierarchical trail of parent pages.
- **Use cases:** Detail pages (`/services/[slug]`, `/products/[slug]`), legal pages, deep content.
- **Structure:** `nav[aria-label="Breadcrumb"]` > `ol` > `li` links + current page.
- **Content requirements:** Page hierarchy labels.
- **Variants:** `minimal` (chevrons), `with-home`, `collapsed-mobile`.
- **Interaction:** Standard link navigation.
- **Responsive:** May truncate intermediate levels on mobile.
- **Accessibility:** Correct `nav` landmark, ordered list semantics, current page not a link.
- **Animation:** None required; subtle fade allowed.
- **When NOT to use:** On the homepage; on flat sites with no hierarchy.

---

## Hero

### HeroCentered
- **Purpose:** Symmetric, centered headline + subcopy + CTAs.
- **Use cases:** Brand-led homepages, product launches, minimal corporate positioning.
- **Structure:** `section` > `Container` (centered) > [eyebrow] [h1] [p] [CTA group].
- **Content requirements:** Eyebrow, headline, subcopy, 1–2 CTAs.
- **Variants:** `with-backdrop-image`, `with-gradient`, `minimal` (no image), `dark`.
- **Interaction:** CTA links; scroll cue optional.
- **Responsive:** Type scales down; CTAs stack full-width on mobile.
- **Accessibility:** Single `h1`, sufficient contrast, meaningful link text.
- **Animation:** Staggered fade-up on load.
- **When NOT to use:** When asymmetric or imagery-led composition communicates better (see creativity rules).

### HeroSplit
- **Purpose:** Asymmetric two-column hero: content × media.
- **Use cases:** Premium/tech positioning; when an image or illustration carries meaning.
- **Structure:** `section` > `SplitLayout` (content | media).
- **Content requirements:** Headline, subcopy, CTAs, media asset + alt.
- **Variants:** `media-right`, `media-left`, `ratio` control, `overlap` (media crosses boundary).
- **Interaction:** CTA; optional media hover/parallax.
- **Responsive:** Stacks to content-then-media (or media-first) on mobile; media aspect preserved.
- **Accessibility:** Media has alt/role appropriate; contrast maintained.
- **Animation:** Content fade-up; media reveal (ImageReveal); optional parallax.
- **When NOT to use:** When the company's message is text-only and symmetry is calmer.

### HeroImageLeft / HeroImageRight
- **Purpose:** Full-bleed image hero with overlay copy on one side.
- **Use cases:** Photography-led brands, agencies, architecture, travel.
- **Structure:** `section` (full-bleed media) > overlay > `Container` > content column.
- **Content requirements:** Headline, subcopy, CTA, image + alt (or decorative role).
- **Variants:** `left`/`right` column, `scrim-strength`, `dark`/`light` overlay.
- **Interaction:** CTA; scroll cue.
- **Responsive:** Overlay content stays readable; image crops via `object-fit` at each breakpoint.
- **Accessibility:** Decorative background images `aria-hidden`; real content never inside the image.
- **Animation:** Image slow zoom/kine; content fade.
- **When NOT to use:** When the image is meaningless stock — a type-led hero is stronger.

### HeroVideo
- **Purpose:** Video background or side video with headline overlay.
- **Use cases:** Product/agency sites where motion demonstrates capability.
- **Structure:** `section` > video (muted, loop, playsinline) + overlay + content.
- **Content requirements:** Video asset (static poster fallback), headline, CTA.
- **Variants:** `background-video`, `split-video`, `poster-only` (no autoplay).
- **Interaction:** Video autoplay muted; optional play/pause control.
- **Responsive:** Video disabled/replaced by poster on mobile if bandwidth-sensitive.
- **Accessibility:** No autoplay with sound; `prefers-reduced-motion` → poster only; captions only if video carries meaning.
- **Animation:** Subtle zoom; content stagger.
- **When NOT to use:** When a static image communicates equally well — video adds weight (performance rule).

### HeroMinimal
- **Purpose:** Type-only hero with generous negative space.
- **Use cases:** Premium minimal brands, design studios, consultancies.
- **Structure:** `section` > `Container` > oversized headline + subcopy + single CTA.
- **Content requirements:** Strong headline, brief subcopy, one CTA.
- **Variants:** `left-aligned`, `centered`, `with-stat-row`.
- **Interaction:** Minimal — scroll.
- **Responsive:** Type scales fluidly; whitespace preserved.
- **Accessibility:** Single `h1`, contrast, no decorative clutter.
- **Animation:** Very restrained fade/rise.
- **When NOT to use:** When the brand needs imagery or storytelling to communicate.

### HeroEditorial
- **Purpose:** Magazine-style hero: oversized type, multiple columns, meta lines.
- **Use cases:** Creative agencies, media brands, editorial-style companies.
- **Structure:** `section` > `EditorialGrid` > [display headline] [intro column] [meta/meta-links].
- **Content requirements:** Display headline, intro paragraph, optional meta (date/tags/credits).
- **Variants:** `type-led`, `type+image`, `rotated/nested type`.
- **Interaction:** Scroll; optional linked meta.
- **Responsive:** Columns collapse; type scales down but keeps presence.
- **Accessibility:** Heading order intact despite the creative composition.
- **Animation:** Line-by-line text reveal (TextReveal); subtle parallax.
- **When NOT to use:** For conservative B2B audiences expecting calm hierarchy.

### HeroBento
- **Purpose:** Hero built as a bento composition of media and type tiles.
- **Use cases:** Product/platform companies showing many facets at once.
- **Structure:** `section` > `BentoGrid` of mixed tiles (headline, image, stat, card).
- **Content requirements:** Headline tile + 2–5 supporting tiles with assets/stats.
- **Variants:** `asymmetric-bento`, `regular-bento`, `with-featured-tile`.
- **Interaction:** Hover micro-interactions on tiles.
- **Responsive:** Bento reflows to single column; tiles reorder sensibly.
- **Accessibility:** Tab order follows visual order (source order); heading hierarchy preserved.
- **Animation:** Staggered tile reveal; hover lifts.
- **When NOT to use:** When content doesn't fill multiple tiles — sparse bento looks accidental.

### HeroProduct
- **Purpose:** Product-focused hero with product imagery and feature bullets.
- **Use cases:** Product pages, product-led homepages.
- **Structure:** `section` > `SplitLayout` (copy | product visual) + feature strip.
- **Content requirements:** Headline, subcopy, key features (3), CTA, product visual.
- **Variants:** `screenshot-hero`, `mockup-hero`, `3d/art-hero`.
- **Interaction:** CTA; optional gallery thumbnails.
- **Responsive:** Product visual scales/crops; bullets stack.
- **Accessibility:** Screenshot alt describes purpose; no text-in-image without alt.
- **Animation:** Product reveal; floating accents.
- **When NOT to use:** On non-product sites.

---

## Content

### FeatureGrid
- **Purpose:** Grid of feature blocks (icon + title + text).
- **Use cases:** Capabilities, why-us, key features, offering overview.
- **Structure:** `section` > heading block + `Grid` of feature items.
- **Content requirements:** Section heading + 3–8 features (icon, title, 1–2 sentence description).
- **Variants:** `2/3/4-col`, `carded`/`bordered`/`plain`, `icon-top`/`icon-left`.
- **Interaction:** None required; optional hover.
- **Responsive:** Columns collapse to 1–2; content density controlled.
- **Accessibility:** Icons decorative (`aria-hidden`); text provides meaning; headings h3.
- **Animation:** Staggered fade-up on scroll.
- **When NOT to use:** When a single hero feature or editorial layout is more distinctive.

### FeatureList
- **Purpose:** Vertical list of features with supporting text (checkmark style).
- **Use cases:** Comparison-adjacent lists, long-form detail, product pages.
- **Structure:** `section` > heading + ordered/unordered feature list.
- **Content requirements:** 4–10 items (title + optional detail).
- **Variants:** `checklist`, `numbered`, `with-description`, `two-column-list`.
- **Interaction:** None; optional expandable items (see Accordion).
- **Responsive:** Single column; hanging indents preserved.
- **Accessibility:** Semantic list; checkmarks `aria-hidden` with text labels.
- **Animation:** List item stagger.
- **When NOT to use:** When grid presentation communicates faster.

### Benefits
- **Purpose:** Outcome-oriented benefits with emphasis on value.
- **Use cases:** Services/products "why choose us" sections.
- **Structure:** `section` > heading + grid/list of benefit blocks (metric or outcome).
- **Content requirements:** 3–6 benefits (title, description, optional metric).
- **Variants:** `metric-led`, `story-led`, `carded`.
- **Interaction:** None; optional hover.
- **Responsive:** Standard grid collapse.
- **Accessibility:** Metrics are text (not images); heading hierarchy.
- **Animation:** Staggered reveal; counters (see Stats) if metrics included.
- **When NOT to use:** When the same content reads better as features or process.

### Process
- **Purpose:** High-level process overview (phases of engagement).
- **Use cases:** Agencies, consultancies, service firms.
- **Structure:** `section` > heading + horizontal/vertical phase list (number, title, text).
- **Content requirements:** 3–6 phases.
- **Variants:** `numbered-row`, `timeline`, `carded`.
- **Interaction:** None; optional expand.
- **Responsive:** Horizontal phases stack vertically on mobile.
- **Accessibility:** Ordered semantics (`ol`) where order matters.
- **Animation:** Sequential reveal tied to scroll.
- **When NOT to use:** When a simple FeatureGrid suffices.

### Steps
- **Purpose:** Sequential steps (how it works).
- **Use cases:** Onboarding explanation, delivery steps, installation.
- **Structure:** `section` > heading + step list with connectors.
- **Content requirements:** 3–6 steps with descriptions.
- **Variants:** `vertical-connector`, `horizontal-cards`, `numbered`.
- **Interaction:** None; optional sticky step highlighting.
- **Responsive:** Connectors become vertical on mobile.
- **Accessibility:** Real order semantics; numbers not duplicated in text.
- **Animation:** Scroll-linked reveal; connector draw.
- **When NOT to use:** When steps are just features (use FeatureGrid).

### Timeline
- **Purpose:** Chronological history/story timeline.
- **Use cases:** About page company story, milestones, roadmap.
- **Structure:** `section` > heading + vertical timeline (date + title + text).
- **Content requirements:** 4–10 dated entries.
- **Variants:** `center-line`, `left-line`, `alternating`.
- **Interaction:** None; optional expandable detail.
- **Responsive:** Alternating collapses to single side on mobile.
- **Accessibility:** Semantic list; dates in `time` where applicable.
- **Animation:** Scroll reveal per entry.
- **When NOT to use:** When chronology isn't the message.

### Stats
- **Purpose:** Key numbers/statistics.
- **Use cases:** Homepage and about credibility sections.
- **Structure:** `section` > heading + stat grid (value + label).
- **Content requirements:** 3–6 stats (number, unit, label, optional context).
- **Variants:** `count-up`, `static`, `bordered`, `on-dark`.
- **Interaction:** None (count-up is animation, not interaction).
- **Responsive:** Grid collapses; values scale.
- **Accessibility:** Numbers as text with real content; no fabricated stats (content gap = placeholder).
- **Animation:** Count-up on scroll into view (respect reduced motion → static).
- **When NOT to use:** When numbers don't exist yet — never invent statistics.

### Metrics
- **Purpose:** Richer metrics with trend/context (deltas, comparisons).
- **Use cases:** Performance/product results sections.
- **Structure:** `section` > heading + metric cards (value, label, context/trend).
- **Content requirements:** Metrics with source/context.
- **Variants:** `delta`, `benchmark`, `sparkline` (static/SVG).
- **Interaction:** None; optional tooltip.
- **Responsive:** Cards collapse to single column.
- **Accessibility:** Context in text; color not the only channel for trends (arrows + text).
- **Animation:** Count-up + fade; reduced-motion safe.
- **When NOT to use:** When Stats suffice.

---

## Cards

### ServiceCard
- **Purpose:** Card summarizing a service, linking to its detail page.
- **Use cases:** `/services` listing, homepage services preview.
- **Structure:** `article` > [icon/image] [title] [description] [link].
- **Content requirements:** Service title, 1–2 sentence summary, icon or image, slug link.
- **Variants:** `icon-top`, `image-top`, `bordered`, `hover-lift`, `numbered`.
- **Interaction:** Whole-card link (with nested link semantics), hover.
- **Responsive:** Grid collapse; card content truncates gracefully.
- **Accessibility:** Link has descriptive text; card is `article`; focus ring on whole card.
- **Animation:** Hover lift + arrow micro-interaction; scroll reveal.
- **When NOT to use:** When the service is better told as a feature or process section.

### ProductCard
- **Purpose:** Card summarizing a product, linking to detail.
- **Use cases:** `/products` listing.
- **Structure:** `article` > [image] [title] [tagline] [tags] [link].
- **Content requirements:** Product image (or placeholder), name, tagline, category tags.
- **Variants:** `grid-card`, `bento-card`, `editorial-card`, `featured`.
- **Interaction:** Whole-card link, hover.
- **Responsive:** Image aspect preserved; card grid collapses.
- **Accessibility:** Image alt meaningful; card link descriptive.
- **Animation:** Image zoom on hover; reveal.
- **When NOT to use:** When product detail is thin — a simple list is better.

### TeamCard
- **Purpose:** Team member card.
- **Use cases:** About/team sections.
- **Structure:** `figure`/`article` > [photo] [name] [role] [bio] [links].
- **Content requirements:** Photo, name, role, short bio, social/profile links.
- **Variants:** `photo`, `initials-avatar`, `compact`.
- **Interaction:** Hover; links.
- **Responsive:** Grid collapse; photo aspect consistent.
- **Accessibility:** Photo alt = person's name; links labeled.
- **Animation:** Photo reveal/grayscale→color; hover.
- **When NOT to use:** When team info isn't available (placeholders would be fabricated).

### TestimonialCard
- **Purpose:** Single customer quote.
- **Use cases:** Testimonials sections.
- **Structure:** `blockquote`/`figure` > quote + attribution (name, role, company, optional photo).
- **Content requirements:** Verbatim quote (placeholder until real), attribution.
- **Variants:** `carded`, `featured-large`, `bordered`.
- **Interaction:** None; optional link to case study.
- **Responsive:** Quote scales; attribution wraps.
- **Accessibility:** Real `blockquote`; attribution not part of quote; no fabricated testimonials (placeholder rule).
- **Animation:** Fade-up reveal.
- **When NOT to use:** When no real testimonials exist — placeholders must be flagged, never invented.

### CaseStudyCard
- **Purpose:** Card linking to a case study/story.
- **Use cases:** Case studies grids on home/services.
- **Structure:** `article` > [image] [industry tag] [title] [summary] [link].
- **Content requirements:** Image, tag, title, 1–2 line summary, link.
- **Variants:** `image-top`, `minimal`, `editorial`.
- **Interaction:** Whole-card link.
- **Responsive:** Grid collapse; image aspect.
- **Accessibility:** Descriptive link text; alt for image.
- **Animation:** Image zoom; reveal.
- **When NOT to use:** When there are no case studies yet — use placeholders, not fake ones.

### BlogCard
- **Purpose:** Article/news teaser card.
- **Use cases:** News or insights sections (if the site includes one).
- **Structure:** `article` > [image] [category] [title] [excerpt] [date] [link].
- **Content requirements:** Image, category, title, excerpt, date, URL.
- **Variants:** `featured`, `grid`, `list`.
- **Interaction:** Link hover.
- **Responsive:** Grid collapse; date formatting responsive.
- **Accessibility:** Date in `time`; descriptive link.
- **Animation:** Reveal + image zoom.
- **When NOT to use:** When the site has no articles (omit the widget entirely).

---

## Social Proof

### LogoCloud
- **Purpose:** Row of client/partner logos.
- **Use cases:** Homepage credibility, about page.
- **Structure:** `section` > heading (optional) + logo row/grid.
- **Content requirements:** Logo images with alt text (client names) or text wordmarks.
- **Variants:** `centered-row`, `grid`, `grayscale-with-color-hover`, `marquee`.
- **Interaction:** Hover color/opacity; links if logos link out.
- **Responsive:** Row wraps/scrolls; logo sizes scale.
- **Accessibility:** Alt = company name; decorative repetition `aria-hidden`.
- **Animation:** Grayscale→color; optional marquee (paused on hover/reduced motion).
- **When NOT to use:** When logos don't exist — use placeholder wordmarks, clearly marked.

### Testimonials
- **Purpose:** Grouped collection of quotes (carousel or grid).
- **Use cases:** Homepage/social proof sections.
- **Structure:** `section` > heading + grid or carousel of TestimonialCards.
- **Content requirements:** 3–8 real quotes.
- **Variants:** `grid`, `carousel`, `featured`.
- **Interaction:** Carousel controls (prev/next, dots) if carousel variant.
- **Responsive:** Grid stacks; carousel becomes swipeable.
- **Accessibility:** Carousel: keyboard controls, pause on hover/focus, `aria-roledescription`, no auto-rotate without pause.
- **Animation:** Fade/slide transitions; stagger.
- **When NOT to use:** Without real quotes — never fabricate testimonials.

### ClientMarquee
- **Purpose:** Continuously scrolling logo/wordmark strip.
- **Use cases:** High-energy credibility band.
- **Structure:** `section` > CSS marquee track of logos.
- **Content requirements:** Logo set (same as LogoCloud).
- **Variants:** `linear`, `alternating`, `masked-edges`.
- **Interaction:** Pause on hover; links optional.
- **Responsive:** Speed adjusts; duplicates removed on small screens.
- **Accessibility:** `prefers-reduced-motion` → static row; content fully readable (no aria-live spam).
- **Animation:** Infinite CSS transform loop.
- **When NOT to use:** For conservative brands; when reduced-motion compliance matters more than motion.

### TrustBadges
- **Purpose:** Badges/certifications strip (ISO, compliance, memberships).
- **Use cases:** Service/consulting sites, compliance-heavy industries.
- **Structure:** `section` > badge row (icons + labels).
- **Content requirements:** Real certifications only (placeholder until verified).
- **Variants:** `icon-row`, `carded`, `inline`.
- **Interaction:** None; optional link to certificate page.
- **Responsive:** Wrap; scale.
- **Accessibility:** Text labels with icons; no claim-only images.
- **Animation:** Subtle reveal.
- **When NOT to use:** Without real certifications — inventing badges is a compliance risk.

### Awards
- **Purpose:** Awards/awards list.
- **Use cases:** Credibility sections.
- **Structure:** `section` > heading + award list/grid (year, award, issuer).
- **Content requirements:** Real awards (year, name, issuer).
- **Variants:** `list`, `grid`, `logo-led`.
- **Interaction:** None; optional links.
- **Responsive:** List/grid collapse.
- **Accessibility:** Data as text; chronological order semantics.
- **Animation:** Reveal.
- **When NOT to use:** When no awards exist.

### Certifications
- **Purpose:** Certifications display (distinct from badges: document-led).
- **Use cases:** About/services pages for regulated industries.
- **Structure:** `section` > heading + certification entries (name, issuer, validity, link to PDF/statement).
- **Content requirements:** Real certifications with verifiable details.
- **Variants:** `carded`, `table`, `list`.
- **Interaction:** Links to statements.
- **Responsive:** Table scrolls horizontally or becomes cards on mobile.
- **Accessibility:** Links labeled; dates valid.
- **Animation:** Reveal.
- **When NOT to use:** When data isn't verifiable.

---

## Interactive

### Accordion
- **Purpose:** Collapsible content sections.
- **Use cases:** FAQ, service detail details, product specs.
- **Structure:** `section` > list of `button + region` pairs.
- **Content requirements:** Short titles + expandable bodies.
- **Variants:** `single-open`, `multi-open`, `bordered`, `carded`.
- **Interaction:** Click/keyboard toggle; one-at-a-time or multiple.
- **Responsive:** Native behavior at all sizes.
- **Accessibility:** `aria-expanded`, `aria-controls`, keyboard operable, focus visible; no layout shift from expanding (smooth, reduced-motion aware).
- **Animation:** Height transition (transform/`grid-template-rows` technique), fade body.
- **When NOT to use:** When the content is short enough to show fully (accordions hide content).

### Tabs
- **Purpose:** Switch between related content panels.
- **Use cases:** Product features, service capabilities, comparison views.
- **Structure:** `div` > `tablist` (buttons) + `tabpanel`s.
- **Content requirements:** 2–6 tabs with distinct content.
- **Variants:** `underline`, `pill`, `vertical`, `icon-tabs`.
- **Interaction:** Click/keyboard (arrows), `tabindex` management.
- **Responsive:** Tabs wrap or become accordion/stacked on mobile.
- **Accessibility:** Full WAI-ARIA tabs pattern: roles, `aria-selected`, `aria-controls`, keyboard arrows, focus into panels.
- **Animation:** Panel fade/slide; indicator movement.
- **When NOT to use:** When all content fits on one scroll — tabs hide content from search.

### Carousel
- **Purpose:** Slide show of cards/images.
- **Use cases:** Testimonials, case studies, gallery.
- **Structure:** `div` (role region) > track (slides) + controls.
- **Content requirements:** 4+ slides.
- **Variants:** `fade`, `slide`, `coverflow`, `auto-advance` (with pause).
- **Interaction:** Prev/next, dots, swipe, keyboard; pause on hover/focus.
- **Responsive:** Slides per view varies by breakpoint.
- **Accessibility:** `aria-roledescription="carousel"`, `aria-label`, controls labeled, no un-pausable auto-advance, reduced-motion → static grid.
- **Animation:** Transform-based slide transitions.
- **When NOT to use:** When a static grid is simpler and content doesn't need sequential focus.

### Slider
- **Purpose:** Single-value range/visual slider (image comparison, value selector).
- **Use cases:** Before/after, configurators.
- **Structure:** `input[type=range]` or custom handle + layers.
- **Content requirements:** Two layers (e.g., images) + label.
- **Variants:** `before-after`, `range-selector`.
- **Interaction:** Drag + keyboard (arrows) via range input.
- **Responsive:** Handle size meets touch targets.
- **Accessibility:** Use native range input semantics; labels; keyboard operable.
- **Animation:** Handle/layer transition (transform only).
- **When NOT to use:** When a static comparison image suffices.

### Modal
- **Purpose:** Focused overlay dialog.
- **Use cases:** Quick info, form presentation, video launch, image zoom.
- **Structure:** `div[role=dialog]` + backdrop + close button; rendered via portal/teleport.
- **Content requirements:** Title, content, close affordance.
- **Variants:** `centered`, `sheet`, `video-modal`, `image-lightbox`.
- **Interaction:** Open trigger; close on Escape/backdrop/button.
- **Responsive:** Full-screen sheet on mobile.
- **Accessibility:** Focus trapped, focus restored, `aria-modal`, labelled by title, scroll lock, `inert` on background.
- **Animation:** Scale/fade in; respect reduced motion.
- **When NOT to use:** For content that belongs inline (prefer progressive disclosure).

### Drawer
- **Purpose:** Slide-in side panel.
- **Use cases:** Mobile nav, filters, detail panes.
- **Structure:** `aside[role=dialog]` sliding from an edge + backdrop.
- **Content requirements:** Panel content + close.
- **Variants:** `left`, `right`, `bottom-sheet`.
- **Interaction:** Trigger, Escape, backdrop close.
- **Responsive:** Full-height on mobile.
- **Accessibility:** Same dialog pattern as Modal (focus trap, restore, labels).
- **Animation:** Slide transform.
- **When NOT to use:** When Modal is the right pattern (drawers imply persistent side context).

### Comparison
- **Purpose:** Feature-by-feature comparison table/cards.
- **Use cases:** Product vs alternatives, plan comparison.
- **Structure:** `section` > table or card grid with attribute rows.
- **Content requirements:** Options + attributes + values (real data only).
- **Variants:** `table`, `card-grid`, `highlight-column`.
- **Interaction:** Scroll (sticky header/column), optional toggle.
- **Responsive:** Table scrolls horizontally on mobile or converts to stacked cards.
- **Accessibility:** Real table semantics (`th`/`scope`) or card semantics; no color-only highlighting.
- **Animation:** Reveal; row highlight on hover.
- **When NOT to use:** When comparison data is fictional — placeholders must be flagged.

### BeforeAfter
- **Purpose:** Interactive before/after comparison of a single image pair.
- **Use cases:** Renovations, transformations, product improvements.
- **Structure:** Two layered images + divider handle (range).
- **Content requirements:** Before/after images with alts.
- **Variants:** `slider`, `click-toggle`.
- **Interaction:** Drag/arrows/click.
- **Responsive:** Handle touch-sized.
- **Accessibility:** Keyboard operable (range input); both images described.
- **Animation:** Divider movement (transform).
- **When NOT to use:** Without a real before/after pair.

### InteractiveCards
- **Purpose:** Cards with rich hover/interaction states (flip, tilt, expand).
- **Use cases:** Feature showcases, team, portfolio.
- **Structure:** `article` with interactive layer (flip/tilt/expand).
- **Content requirements:** Front content (+ back/expanded content if used).
- **Variants:** `flip`, `tilt`, `expand`, `spotlight`.
- **Interaction:** Hover/focus/click.
- **Responsive:** Interaction must work on touch (tap), not hover-only.
- **Accessibility:** Content not hidden behind hover-only interactions; keyboard focus triggers the effect; reduced motion → static.
- **Animation:** CSS 3D/transform effects, GPU-only properties.
- **When NOT to use:** When effects obscure content or content is critical on mobile.

---

## Business

### ServicesGrid
- **Purpose:** Composed grid of ServiceCards.
- **Use cases:** `/services` listing, homepage preview.
- **Structure:** `section` > heading + `Grid` of ServiceCards.
- **Content requirements:** Service list from `content/services.ts`.
- **Variants:** `2/3-col`, `bento`, `grouped-by-category`.
- **Interaction:** Card links.
- **Responsive:** Grid collapse; category grouping stacks.
- **Accessibility:** Card `article` semantics; list semantics optional.
- **Animation:** Staggered reveal.
- **When NOT to use:** When a single feature section tells the story better.

### ProductsGrid
- **Purpose:** Composed grid of ProductCards.
- **Use cases:** `/products` listing.
- **Structure:** `section` > heading + `Grid`/`BentoGrid` of ProductCards.
- **Content requirements:** Product list from `content/products.ts`.
- **Variants:** `grid`, `bento`, `featured-first`.
- **Interaction:** Card links, category filter (optional).
- **Responsive:** Bento degrades to uniform grid on mobile.
- **Accessibility:** Filter controls labeled; results announced.
- **Animation:** Reveal; filter transitions.
- **When NOT to use:** Without real products (use placeholders, flagged).

### IndustriesGrid
- **Purpose:** Industries served, as card/icon grid.
- **Use cases:** Services/homepage.
- **Structure:** `section` > heading + grid of industry entries (icon + name + optional blurb).
- **Content requirements:** Industry list.
- **Variants:** `icon-grid`, `card-grid`, `list`.
- **Interaction:** Optional links to related services.
- **Responsive:** Grid collapse.
- **Accessibility:** Icons decorative; text meaningful.
- **Animation:** Staggered reveal.
- **When NOT to use:** When the company doesn't segment by industry.

### CaseStudies
- **Purpose:** Grouped CaseStudyCards.
- **Use cases:** Home/services credibility.
- **Structure:** `section` > heading + grid/list of CaseStudyCards.
- **Content requirements:** Real case studies.
- **Variants:** `grid`, `featured+grid`, `editorial-list`.
- **Interaction:** Card links.
- **Responsive:** Grid collapse.
- **Accessibility:** Article semantics, descriptive links.
- **Animation:** Reveal.
- **When NOT to use:** Without real case studies (flag placeholders).

### ProcessTimeline
- **Purpose:** The company's engagement/development process as a timeline.
- **Use cases:** Services pages, about.
- **Structure:** `section` > heading + Timeline of process phases.
- **Content requirements:** 3–6 phases with descriptions.
- **Variants:** `center`, `left`, `horizontal`.
- **Interaction:** None; optional expand.
- **Responsive:** Vertical stack on mobile.
- **Accessibility:** Ordered list semantics.
- **Animation:** Scroll-linked draw + reveal.
- **When NOT to use:** When the process is simple (Steps suffices).

### CompanyStats
- **Purpose:** Company credibility statistics band.
- **Use cases:** Homepage, about.
- **Structure:** `section` > heading + Stats band.
- **Content requirements:** Real, verified numbers.
- **Variants:** `on-dark`, `bordered`, `full-bleed`.
- **Interaction:** None.
- **Responsive:** Band stacks on mobile.
- **Accessibility:** Numbers as text.
- **Animation:** Count-up (reduced-motion safe).
- **When NOT to use:** When numbers aren't real.

### Team
- **Purpose:** Full team section (grid of TeamCards).
- **Use cases:** About page.
- **Structure:** `section` > heading + Grid of TeamCards.
- **Content requirements:** Real team members.
- **Variants:** `grid`, `carousel`, `list`.
- **Interaction:** Card links/hover.
- **Responsive:** Grid collapse.
- **Accessibility:** Photos alt = name.
- **Animation:** Reveal.
- **When NOT to use:** When team data is unavailable or fabricated.

### Partners
- **Purpose:** Partner/integration showcase.
- **Use cases:** About/services/home.
- **Structure:** `section` > heading + partner grid (logo/name + relationship).
- **Content requirements:** Real partners.
- **Variants:** `logo-grid`, `card-grid`, `tiered`.
- **Interaction:** Optional links.
- **Responsive:** Grid collapse.
- **Accessibility:** Alt text = partner name.
- **Animation:** Reveal/grayscale hover.
- **When NOT to use:** Without real partners.

---

## CTA

### CTASection
- **Purpose:** Full section call-to-action (headline + action).
- **Use cases:** End of most pages.
- **Structure:** `section` > centered/left content + CTA buttons.
- **Content requirements:** Headline, supporting line, 1–2 actions.
- **Variants:** `centered`, `split`, `on-gradient`, `on-image`, `dark`.
- **Interaction:** CTA links.
- **Responsive:** Buttons stack on mobile.
- **Accessibility:** Meaningful link text; contrast.
- **Animation:** Reveal; subtle background motion.
- **When NOT to use:** When a page already has a strong hero CTA (avoid CTA fatigue).

### SplitCTA
- **Purpose:** Two-sided CTA (copy × media/action card).
- **Use cases:** Pages wanting a designed closing moment.
- **Structure:** `section` > `SplitLayout` (copy | action/media).
- **Content requirements:** Headline, copy, actions, optional media.
- **Variants:** `with-form` (static presentation), `with-image`, `with-contact-card`.
- **Interaction:** Actions/form presentation.
- **Responsive:** Stacks on mobile.
- **Accessibility:** Form presentation follows form accessibility rules.
- **Animation:** Reveal.
- **When NOT to use:** When a simple CTASection is cleaner.

### BannerCTA
- **Purpose:** Slim horizontal promo banner.
- **Use cases:** Mid-page promos, page-top alerts.
- **Structure:** `section`/`div` > [message] [link/button].
- **Content requirements:** Short message + action.
- **Variants:** `top-banner`, `inline-banner`, `dismissible`.
- **Interaction:** Link; optional dismiss.
- **Responsive:** Message truncates/wraps gracefully.
- **Accessibility:** Dismissible banners announce removal appropriately; banner landmark semantics.
- **Animation:** Entrance slide/fade.
- **When NOT to use:** For important content users shouldn't dismiss.

### FloatingCTA
- **Purpose:** Persistent floating action (e.g., "Contact us").
- **Use cases:** Mobile-focused conversion prompts.
- **Structure:** `div` fixed-position button/link.
- **Content requirements:** Short label + link.
- **Variants:** `fab`, `bottom-bar`, `chat-style`.
- **Interaction:** Tap/click.
- **Responsive:** Primarily a mobile pattern (hide on desktop if redundant).
- **Accessibility:** Never overlaps content or focus; keyboard accessible; contrast; not animated aggressively.
- **Animation:** Subtle entrance; reduced-motion safe.
- **When NOT to use:** When it covers content or duplicates persistent navigation.
