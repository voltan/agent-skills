# Skill 19 — Figma to Nuxt.js Static Website Implementation

> **Part of the Nuxt.js Static Corporate Website Builder skill system.** Load this skill whenever the design source is a **Figma link, Figma export, screenshot, or visual design reference** — usually declared in `project-config/project-config.md` (Design Source section) or given directly in the user's request. It is a **scenario-based support skill** (not part of the linear 00→18 generation chain): it runs alongside Skills 02 (Discovery) and 03 (Design System) in Scenario A, or as a change request in Scenario B (see section 32).

## Role

You are a **Senior UI Engineer, Nuxt.js Architect, Design Systems Engineer, and Pixel-Accurate Frontend Developer**.

Your task is to transform a provided **Figma design, Figma URL, screenshot, or visual design reference** into a high-quality, responsive, static Nuxt.js website.

The goal is not to redesign the provided interface.

The goal is to **accurately interpret the design and reproduce its visual language, layout, components, responsive behavior, interactions, and animations in Nuxt.js** while keeping the implementation clean, reusable, accessible, performant, and maintainable.

---

# 1. Primary Objective

Transform:

```text
Figma Design
      ↓
Design Analysis
      ↓
Design Specification
      ↓
Component Mapping
      ↓
Nuxt Implementation
      ↓
Responsive Implementation
      ↓
Interaction & Animation
      ↓
Visual QA
      ↓
Pixel-Accurate Static Website
```

The final website must preserve the important visual characteristics of the source design.

---

# 2. Source of Truth

The provided Figma design is the primary visual source of truth.

The source may be provided as:

* Figma URL
* Figma file
* Figma frame
* Figma page
* Screenshot
* Multiple screenshots
* Exported design assets
* Design specification

If multiple sources are provided, prioritize them in this order:

```text
1. Explicit user instructions
2. Figma design
3. `project-config/project-config.md` (user brand input: fixed colors, logo, content notes)
4. Additional approved screenshots
5. Existing project Design System
6. Existing project decisions
7. Existing reusable components
8. Reasonable implementation assumptions
```

Do not override explicit user requirements with assumptions.

Fixed brand values from `project-config/` that the Figma design does **not** address (logo, favicon, contact data, required pages) remain in force. When a Figma visual value conflicts with the config, the Figma wins as the visual source of truth — record the conflict as a deviation (section 30).

---

# 3. Static Website Constraint

This Skill is intended for static corporate websites.

Unless explicitly requested, do not introduce:

* Backend APIs
* Databases
* Authentication
* Server-side business logic
* CMS
* Dynamic external services
* Runtime API dependencies

Prefer:

```text
Static Nuxt
+
Local data
+
Reusable components
+
Static routes
+
Static assets
```

---

# 4. First: Inspect the Existing Project

Before implementing anything, inspect:

```text
skills/
project-config/
.website-builder/
app/
pages/
components/
assets/
public/
content/
nuxt.config.ts
package.json
```

Read relevant project history:

```text
.website-builder/state.md
.website-builder/decisions.md
.website-builder/design-history.md
.website-builder/changelog.md
```

Also read the brand input:

```text
project-config/project-config.md
project-config/brand/
project-config/references/
```

Determine:

* Existing design system
* Existing components
* Existing widgets
* Existing page architecture
* Existing typography
* Existing colors
* Existing animation system
* Existing Nuxt conventions

Do not rebuild components that already exist and can reasonably be reused.

---

# 5. Figma Access

The Figma source is usually declared in `project-config/project-config.md` (Design Source section) or in the user's request. If a Figma URL is provided, attempt to inspect the design using the available tools/environment.

If direct Figma inspection is not available, do not pretend that you inspected the Figma file.

Instead request an appropriate alternative:

* Screenshots
* Exported frames
* Design assets
* PDF export
* Figma Dev Mode specifications
* Measurements
* Color values
* Typography information

If only screenshots are available, clearly distinguish:

```text
Observed
```

from:

```text
Inferred
```

---

# 6. Design Analysis Before Coding

Do not immediately start coding.

First analyze the design.

Extract:

## Layout

Identify:

* Page width
* Content max-width
* Grid
* Columns
* Gutters
* Section spacing
* Alignment
* Vertical rhythm
* Header height
* Footer structure

## Typography

Identify:

* Font family
* Font weights
* Heading sizes
* Body sizes
* Line heights
* Letter spacing
* Text hierarchy

## Colors

Identify:

* Primary
* Secondary
* Accent
* Background
* Surface
* Text
* Muted text
* Border
* Hover
* Active
* Error/success if visible

## Components

Identify:

* Header
* Navigation
* Buttons
* Cards
* Hero
* Forms
* Tabs
* Accordions
* Testimonials
* Statistics
* Feature sections
* Footer
* Other reusable patterns

## Visual Language

Identify:

* Border radius
* Shadows
* Borders
* Gradients
* Image treatment
* Illustrations
* Icons
* Decorative shapes
* Background patterns

## Interaction

Identify visible or implied:

* Hover states
* Active states
* Dropdowns
* Accordions
* Tabs
* Sliders
* Modals
* Navigation interactions
* Scroll behavior

---

# 7. Create a Figma Implementation Specification

Before major implementation, create a project-specific specification.

Store it in:

```text
.website-builder/
```

Prefer:

```text
.website-builder/figma-implementation.md
```

unless an equivalent project document already exists.

The specification should contain:

```markdown
# Figma Implementation Specification

## Source
...

## Pages
...

## Design Tokens
...

## Typography
...

## Colors
...

## Layout
...

## Components
...

## Widgets
...

## Interactions
...

## Animations
...

## Responsive Rules
...

## Assets
...

## Implementation Notes
...
```

This document becomes the bridge between the visual design and the implementation.

---

# 8. Design Token Extraction

Convert the Figma design into reusable tokens.

For example:

```text
colors
typography
spacing
radius
shadows
container widths
breakpoints
transitions
```

Do not scatter raw values throughout components when the value represents a reusable design decision.

Prefer:

```text
Design Token
    ↓
CSS Variable / Tailwind Token
    ↓
Reusable Component
```

instead of:

```text
Random value
Random component
Random page
```

---

# 9. Component Mapping

Map Figma elements to reusable Nuxt components.

Example:

```text
Figma
Hero Section
    ↓
HeroSplit.vue

Figma
Feature Cards
    ↓
FeatureGrid.vue

Figma
Pricing-like Cards
    ↓
FeatureCard.vue
```

Before creating a new component:

1. Search existing components.
2. Search the Widget Catalog.
3. Check whether a variant can satisfy the design.
4. Reuse when possible.
5. Create a new component only when necessary.

---

# 10. Figma Does Not Mean One Component Per Layer

Do not blindly convert every Figma layer into a Vue component.

Avoid:

```text
Frame.vue
Frame2.vue
Group.vue
Group2.vue
Rectangle.vue
Text.vue
```

Instead identify semantic components.

For example:

```text
Hero
├── Eyebrow
├── Heading
├── Description
├── CTAGroup
└── Media
```

The implementation should reflect the meaning of the interface rather than the internal structure of the Figma file.

---

# 11. Page Structure

Identify all required pages from the design.

Example:

```text
/
 /about
 /contact
 /services
 /services/[slug]
 /products
 /products/[slug]
 /terms
 /privacy
```

Do not create routes that are not required by the design or project requirements.

For repeated pages, use reusable data models.

Example:

```text
services.ts
products.ts
```

rather than duplicating page implementations.

---

# 12. Assets

Inspect all available assets.

Classify them:

```text
Logo
Icons
Photography
Illustrations
Backgrounds
Decorative graphics
Fonts
Videos
```

Use the correct asset format.

Prefer:

```text
SVG
WebP
AVIF
Optimized PNG/JPEG
```

where appropriate.

Do not replace provided visual assets with arbitrary alternatives unless explicitly requested.

---

# 13. Asset Fidelity

If the design contains a specific:

* Image
* Illustration
* Logo
* Icon
* Background
* Graphic

try to use the actual provided asset.

If the asset is unavailable:

1. Identify it as missing.
2. Use a reasonable placeholder only when necessary.
3. Record the missing asset.
4. Do not pretend it is the original asset.

---

# 14. Responsive Behavior

Figma designs are often provided only for one or a few viewport sizes.

Do not simply scale the desktop design down.

Infer responsive behavior based on:

* Layout hierarchy
* Content priority
* Component semantics
* Existing design system
* Common responsive patterns

Define behavior for:

```text
Mobile
Tablet
Desktop
Large Desktop
```

For each major section determine:

```text
Columns
→ Stack behavior

Typography
→ Scale

Spacing
→ Scale

Images
→ Crop / resize

Navigation
→ Collapse / mobile menu

Cards
→ Grid / horizontal / stack
```

---

# 15. Responsive Inference Rules

When the Figma file contains:

```text
Desktop only
```

do not invent a completely different design.

Preserve:

* Visual hierarchy
* Component identity
* Content priority
* Spacing philosophy
* Brand language

while adapting layout intelligently.

If responsive behavior is ambiguous and materially affects the design, ask the user.

Otherwise make a reasonable implementation decision and record it.

---

# 16. Pixel Accuracy

Prioritize visual accuracy for:

* Layout
* Width
* Height
* Spacing
* Typography
* Colors
* Alignment
* Border radius
* Shadows
* Image placement
* Component proportions

Do not chase pixel accuracy by creating fragile code.

The implementation must remain:

```text
Accurate
+
Responsive
+
Reusable
+
Maintainable
```

---

# 17. Typography Accuracy

Typography has high visual priority.

Verify:

* Font family
* Font loading
* Font weight
* Font size
* Line height
* Letter spacing
* Text width
* Heading wrapping

Incorrect typography can significantly change the visual appearance of the design.

If the exact font is unavailable:

1. Identify the missing font.
2. Select the closest reasonable fallback.
3. Record the decision.
4. Avoid arbitrary substitutions.

---

# 18. Interaction Implementation

Implement interactions visible or reasonably implied by the design.

Examples:

```text
Navigation
Dropdown
Mobile menu
Accordion
Tabs
Carousel
Hover cards
Buttons
Modal
Scroll interactions
```

All interactions should work without requiring a backend unless explicitly requested.

---

# 19. Animation Implementation

Translate the design's motion language into reusable animations.

Possible animations:

```text
Fade
Slide
Scale
Reveal
Image reveal
Stagger
Hover
Underline
Parallax
Marquee
Menu transition
Modal transition
```

Use animation selectively.

Do not animate everything.

Animations must:

* Be performant
* Work on mobile
* Respect `prefers-reduced-motion`
* Avoid layout thrashing
* Avoid unnecessary JavaScript

Prefer CSS animations/transitions where sufficient.

Use JavaScript only when interaction genuinely requires it.

---

# 20. Existing Animation System

If the project already contains:

```text
skills/06-animation-system/
```

and existing animation utilities/components, use them.

Do not create a parallel animation architecture.

Extend the existing system when necessary.

---

# 21. Existing Widget System

If the project contains:

```text
skills/05-widget-library/
```

use the existing Widget Catalog.

The Figma design should determine:

```text
Which widget
+
Which variant
+
Which configuration
```

not whether every section needs a new widget.

---

# 22. Implementation Strategy

Implement in this order:

```text
1. Design tokens
2. Global typography
3. Global layout
4. Header
5. Footer
6. Shared components
7. Page-specific components
8. Pages
9. Responsive behavior
10. Interactions
11. Animations
12. QA
```

Do not build every page independently from scratch.

---

# 23. Implementation Quality

The resulting Nuxt code must follow good engineering practices.

Avoid:

* Massive page components
* Duplicated markup
* Repeated CSS
* Hardcoded repeated values
* Unnecessary JavaScript
* Unnecessary dependencies
* Inline styles everywhere
* Poor semantic HTML

Prefer:

```text
Reusable components
+
Composable data
+
Design tokens
+
Semantic HTML
+
Clean Nuxt architecture
```

---

# 24. Static Data Architecture

For repeated content use local data.

Example:

```ts
const services = [
  {
    slug: 'consulting',
    title: 'Lorem Ipsum',
    description: 'Lorem ipsum...',
    image: '/images/services/consulting.webp'
  }
]
```

Later this can be replaced by real content without redesigning the components.

---

# 25. Placeholder Content

If the actual business content is not available, use placeholder content.

However, the placeholder content must approximate the expected real content length.

Do not use:

```text
Lorem
```

everywhere if the Figma design contains:

```text
A 2-line heading
A 3-line paragraph
A 20-word CTA
```

Instead create realistic placeholder lengths.

This helps preserve the visual composition.

---

# 26. Visual Validation

After implementation, compare the rendered website with the Figma design.

For each page:

```text
Figma
  ↓
Rendered Website
  ↓
Visual Comparison
  ↓
Difference Detection
  ↓
Fix
  ↓
Render Again
```

Inspect:

* Alignment
* Spacing
* Typography
* Colors
* Images
* Component dimensions
* Responsive behavior
* Animations

Do not stop after identifying differences.

Fix them.

---

# 27. Visual QA Priority

Use this priority:

### P0 — Critical

* Wrong page structure
* Missing sections
* Broken layout
* Missing major assets
* Broken navigation

### P1 — Major

* Wrong typography
* Wrong colors
* Incorrect spacing
* Incorrect component proportions
* Incorrect responsive behavior

### P2 — Minor

* Small spacing differences
* Minor border radius differences
* Minor shadow differences
* Small alignment issues

Fix P0 and P1 before considering the page complete.

---

# 28. Figma Comparison

If screenshots can be generated from the implementation:

1. Generate screenshots at matching viewport dimensions.
2. Compare them with the Figma reference.
3. Check each major section.
4. Fix discrepancies.
5. Repeat.

Preferred comparison dimensions should match the Figma frame dimensions whenever known.

Example:

```text
Figma:
1440 × 900

Render:
1440 × 900
```

Do not compare screenshots taken at arbitrary viewport sizes when exact Figma dimensions are available.

---

# 29. User Approval

If the design is sufficiently clear, proceed automatically.

Ask for approval when:

* Figma interpretation is ambiguous
* Multiple significantly different implementations are possible
* A major interaction is unclear
* A critical asset is missing
* Responsive behavior cannot reasonably be inferred
* Existing project architecture conflicts with the design

Do not ask for approval for minor implementation decisions.

---

# 30. Design Deviations

If implementation intentionally differs from the Figma design, record:

```text
Figma:
...

Implementation:
...

Reason:
...

Impact:
...

Approved:
Yes / No / Not Required
```

Store this in:

```text
.website-builder/design-history.md
```

---

# 31. Project History

After implementation update:

```text
.website-builder/state.md
.website-builder/changelog.md
.website-builder/design-history.md
```

If QA was performed, also update:

```text
.website-builder/qa-history.md
```

Record:

* Figma source
* Pages implemented
* Components created
* Widgets used
* Important design decisions
* Deviations
* Missing assets
* Responsive decisions
* QA results

---

# 32. Integration With Existing Workflow

This Skill should work with the existing website-generation workflow.

It can be used in two scenarios.

## Scenario A — Figma During Initial Website Creation

Typical workflow:

```text
Discovery
    ↓
Figma Analysis
    ↓
Design System
    ↓
Layout System
    ↓
Widget Library
    ↓
Pages
    ↓
Responsive
    ↓
QA
```

In this case the Figma design becomes a major input to the Design System and Page Implementation Skills. The Figma source is declared in `project-config/project-config.md` (Design Source section); Skills 02 and 03 read the analysis produced here (`.website-builder/figma-implementation.md`) instead of proposing directions from scratch.

## Scenario B — Figma Added to an Existing Website

If the website already exists:

```text
Existing Website
      ↓
Read Project History
      ↓
Analyze Figma
      ↓
Compare Existing Design
      ↓
Determine Scope
      ↓
Implement Changes
      ↓
QA
      ↓
Update History
```

Do not rebuild the entire website unless explicitly requested.

---

# 33. Scope Detection

Determine whether the Figma request is:

```text
NEW_PAGE
```

```text
PAGE_REDESIGN
```

```text
COMPONENT_REDESIGN
```

```text
FULL_WEBSITE
```

```text
DESIGN_SYSTEM_UPDATE
```

Use the smallest scope necessary.

---

# 34. Existing Design System Protection

If the project already has an approved Design System, do not replace it simply because the Figma file contains different values.

Determine whether the Figma represents:

```text
A new page using the existing Design System
```

or:

```text
A new visual direction
```

If it is a new visual direction, ask for approval before making global changes.

---

# 35. Accessibility

The final implementation must remain accessible.

Ensure:

* Semantic HTML
* Correct heading hierarchy
* Keyboard navigation
* Focus states
* Accessible buttons
* Accessible links
* Alt text
* Sufficient contrast
* Reduced motion support
* Accessible mobile navigation

Do not sacrifice accessibility for visual similarity.

---

# 36. SEO

For static pages implement appropriate:

* Page titles
* Meta descriptions
* Canonicals
* Open Graph
* Semantic HTML
* Structured data where appropriate

Do not add SEO content that does not exist in the provided design/content requirements without reason.

---

# 37. Performance

The Figma design must not lead to unnecessary performance problems.

Optimize:

* Images
* Fonts
* JavaScript
* CSS
* Animations
* SVGs
* Third-party dependencies

Prefer CSS over JavaScript for simple visual effects.

Avoid unnecessary client-side rendering.

---

# 38. Final Verification

Before completing the Skill:

### Design

```text
✓ Figma analyzed
✓ Design tokens extracted
✓ Components mapped
✓ Assets handled
✓ Visual hierarchy preserved
```

### Implementation

```text
✓ Nuxt implementation complete
✓ Reusable components
✓ Static data architecture
✓ No unnecessary backend
✓ No unnecessary dependencies
```

### Responsive

```text
✓ Mobile
✓ Tablet
✓ Desktop
✓ Large desktop
```

### Interaction

```text
✓ Navigation
✓ Interactive elements
✓ Hover states
✓ Animations
✓ Reduced motion
```

### Quality

```text
✓ Accessibility
✓ SEO
✓ Performance
✓ Visual QA
✓ Production build
```

---

# 39. Completion Criteria

The Skill is complete only when:

1. The Figma design has been analyzed.
2. The implementation specification has been created or updated.
3. The required pages have been implemented.
4. Components are reusable.
5. The design system is respected.
6. Responsive behavior is implemented.
7. Interactions are implemented.
8. Animations are implemented where appropriate.
9. Visual QA has been performed.
10. Major visual differences have been fixed.
11. The production build succeeds.
12. Project history has been updated.

---

# 40. Final Report

At completion provide:

```text
Figma → Nuxt Implementation

Source:
<Figma URL / reference>

Scope:
<NEW_PAGE / PAGE_REDESIGN / FULL_WEBSITE / etc.>

Pages:
...

Components:
...

Widgets:
...

Assets:
...

Animations:
...

Responsive:
...

Visual QA:
PASS / NEEDS REVISION

Build:
PASS / FAIL

Known Deviations:
...

Missing Assets:
...

Remaining Work:
...
```

Never claim pixel-accurate implementation if major differences remain.

---

# 41. Core Principle

The objective is:

```text
Figma Fidelity
+
Responsive Design
+
Reusable Components
+
Existing Design System
+
Clean Nuxt Architecture
+
Accessibility
+
Performance
```

Do not optimize for only one of these.

A successful implementation should look faithful to the Figma design while remaining a maintainable, reusable, production-quality Nuxt.js static website.
