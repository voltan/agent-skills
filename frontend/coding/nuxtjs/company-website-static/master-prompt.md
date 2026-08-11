# Master Prompt — Build the Nuxt.js Static Corporate Website Skill System

## Role

You are a **Principal Frontend Architect, Nuxt.js Architect, Design Systems Engineer, UX/UI Architect, Creative Web Designer, Animation Engineer, SEO Engineer, Performance Engineer, and AI Agent Skill Architect**.

Your task is **NOT to build a website**.

Your task is to create a complete, reusable **AI Skill System for designing and implementing modern static corporate websites with Nuxt.js**.

The Skill System will later be executed skill-by-skill to generate an actual website.

---

# 1. Working Directory

Work exclusively inside:

```text
frontend/coding/nuxtjs/company-website-static/
```

If the directory does not exist, create it.

Do not modify unrelated project directories.

---

# 2. Primary Objective

Create the complete directory and file structure for a reusable corporate website generation system.

The system must allow another AI agent to execute the skills sequentially and eventually generate a complete:

* Modern
* Creative
* Responsive
* Accessible
* SEO-friendly
* High-performance
* Static
* Nuxt.js-based
* Corporate website

The target website is assumed to be a **static corporate website with no backend service dependency**.

There must be no requirement for:

* API servers
* Databases
* Authentication
* CMS
* External business services

The website should be able to operate entirely from Nuxt static content.

---

# 3. CRITICAL RULE

At this stage:

**DO NOT BUILD THE WEBSITE.**

Do not create:

* Vue pages
* Vue components for the actual website
* CSS for the actual website
* JavaScript application logic
* Website content
* Images
* Logos
* Business-specific copy
* Business-specific design

Instead, create the **Skill System** that will later instruct an AI agent how to build those things.

---

# 4. First Task — Create the Complete Directory Structure

Create this exact structure:

```text
frontend/coding/nuxtjs/company-website-static/
│
├── README.md
│
├── 00-orchestrator/
│   └── skill.md
│
├── 01-project-init/
│   └── skill.md
│
├── 02-discovery/
│   └── skill.md
│
├── 03-design-system/
│   ├── skill.md
│   └── creativity-rules.md
│
├── 04-layout-system/
│   └── skill.md
│
├── 05-widget-library/
│   ├── skill.md
│   └── widget-catalog.md
│
├── 06-animation-system/
│   └── skill.md
│
├── 07-homepage/
│   └── skill.md
│
├── 08-about/
│   └── skill.md
│
├── 09-contact/
│   └── skill.md
│
├── 10-services/
│   ├── skill.md
│   └── service-detail.md
│
├── 11-products/
│   ├── skill.md
│   └── product-detail.md
│
├── 12-legal/
│   └── skill.md
│
├── 13-content-replacement/
│   └── skill.md
│
├── 14-responsive/
│   └── skill.md
│
├── 15-accessibility/
│   └── skill.md
│
├── 16-seo/
│   └── skill.md
│
├── 17-performance/
│   └── skill.md
│
└── 18-visual-qa/
    └── skill.md
```

---

# 5. Create Every File

Every file listed above must be created.

However, do not leave them empty.

Each file must contain a complete, production-quality Skill specification.

The files must be written in **English**.

The Skill System documentation itself must be highly structured and suitable for execution by AI coding agents.

---

# 6. README.md

Create a comprehensive README explaining:

* Purpose of this Skill System
* Target environment
* How the skills work
* Skill execution order
* Dependencies between skills
* Manual vs assisted vs autonomous execution
* Design workflow
* Content workflow
* QA workflow
* How visual references are handled
* How screenshots are analyzed
* How widgets are selected
* How animations are selected
* How real content replaces placeholder content
* How the final website is validated

Include the recommended execution sequence:

```text
00 → 01 → 02 → 03 → 04 → 05 → 06
→ 07 → 08 → 09 → 10 → 11 → 12
→ 13 → 14 → 15 → 16 → 17 → 18
```

Explain that some skills may be rerun when necessary.

---

# 7. 00-orchestrator/skill.md

Create the master orchestration skill.

Its responsibilities:

1. Understand the current project state.
2. Determine which skills have already been executed.
3. Determine the next appropriate skill.
4. Maintain consistency between all skills.
5. Prevent duplicated work.
6. Preserve the approved design system.
7. Preserve approved layout decisions.
8. Preserve approved widget decisions.
9. Coordinate visual QA.
10. Detect incomplete implementation.
11. Never silently override user-approved design decisions.

The orchestrator must support three execution modes:

### Autonomous

AI makes reasonable decisions without asking for approval.

### Assisted

AI proposes important decisions and asks for approval.

This should be the default.

### Manual

AI asks before major design decisions and page structure decisions.

---

# 8. 01-project-init/skill.md

Create a skill responsible for initializing a Nuxt.js static corporate website project.

The skill must define:

* Nuxt version strategy
* TypeScript
* Tailwind CSS
* ESLint
* Prettier
* Nuxt Image where appropriate
* SEO tooling
* Static generation
* Component architecture
* Layout architecture
* Asset organization
* Content organization
* Route organization
* Development conventions

It must not create business-specific pages.

It should establish a clean foundation for the later skills.

---

# 9. 02-discovery/skill.md

Create a discovery skill.

This skill must gather the information required before visual design begins.

It should ask about:

* Company type
* Industry
* Website purpose
* Target audience
* Required pages
* Services
* Products
* Brand identity
* Existing logo
* Existing colors
* Typography
* Visual preferences
* Design references
* Website references
* Screenshot references
* Image references
* Animation preferences
* Content availability

The skill must support visual references.

A user should be able to provide:

* Screenshot
* Image
* Existing website URL
* Design description
* Brand colors
* Logo
* Or simply ask the AI to create the design

The skill must produce a structured discovery result that later skills can consume.

---

# 10. 03-design-system/skill.md

Create a design-system generation skill.

It must create a reusable design system containing:

* Brand direction
* Color palette
* Semantic colors
* Typography
* Font hierarchy
* Spacing scale
* Container widths
* Grid system
* Border radius
* Shadows
* Borders
* Buttons
* Forms
* Cards
* Icons
* Image treatment
* Section spacing
* Visual hierarchy
* Responsive rules
* Animation principles

It must support three design directions before implementation.

Example:

```text
A. Premium Minimal
B. Modern Corporate
C. Creative Editorial
```

However, the choices must be dynamically generated according to the discovered company and visual references.

The user should be able to approve, modify, or regenerate the design direction.

---

# 11. 03-design-system/creativity-rules.md

Create detailed creativity rules.

The AI must avoid generic template-like websites.

Encourage appropriate use of:

* Asymmetrical layouts
* Editorial grids
* Oversized typography
* Negative space
* Layered compositions
* Bento layouts
* Split-screen layouts
* Full-bleed imagery
* Overlapping elements
* Floating cards
* Strong typography
* Visual storytelling
* Scroll storytelling
* Interactive sections
* Image masks
* Gradient surfaces
* Monochrome sections
* Subtle glass effects
* Horizontal compositions
* Sticky sections
* Creative navigation
* Micro-interactions

But establish a critical principle:

> Creativity must improve communication, hierarchy, brand identity, or interaction. Never add visual effects merely because they are technically possible.

Also explicitly prohibit repetitive generic SaaS layouts unless they are appropriate for the brand.

---

# 12. 04-layout-system/skill.md

Create the global layout-system skill.

Define reusable layout primitives such as:

```text
Container
Section
Stack
Grid
TwoColumn
ThreeColumn
SplitLayout
CenteredLayout
SidebarLayout
FullBleed
BentoGrid
EditorialGrid
StickyLayout
```

Define:

* Width rules
* Spacing
* Responsive behavior
* Alignment
* Grid behavior
* Content density
* Section rhythm

The layout system must be shared across all pages.

---

# 13. 05-widget-library/skill.md

Create the widget-library architecture.

The skill must define how AI selects and composes reusable website widgets.

Widgets must be:

* Reusable
* Configurable
* Responsive
* Accessible
* Theme-aware
* Animation-aware
* Content-independent where possible

The AI should prefer composition and variants instead of creating unnecessary duplicate components.

---

# 14. 05-widget-library/widget-catalog.md

Create a comprehensive widget catalog.

Include categories such as:

## Navigation

```text
HeaderSimple
HeaderTransparent
HeaderSticky
HeaderMegaMenu
MobileNavigation
Breadcrumbs
```

## Hero

```text
HeroCentered
HeroSplit
HeroImageLeft
HeroImageRight
HeroVideo
HeroMinimal
HeroEditorial
HeroBento
HeroProduct
```

## Content

```text
FeatureGrid
FeatureList
Benefits
Process
Steps
Timeline
Stats
Metrics
```

## Cards

```text
ServiceCard
ProductCard
TeamCard
TestimonialCard
CaseStudyCard
BlogCard
```

## Social Proof

```text
LogoCloud
Testimonials
ClientMarquee
TrustBadges
Awards
Certifications
```

## Interactive

```text
Accordion
Tabs
Carousel
Slider
Modal
Drawer
Comparison
BeforeAfter
InteractiveCards
```

## Business

```text
ServicesGrid
ProductsGrid
IndustriesGrid
CaseStudies
ProcessTimeline
CompanyStats
Team
Partners
```

## CTA

```text
CTASection
SplitCTA
BannerCTA
FloatingCTA
```

The catalog must describe for each widget:

* Purpose
* Appropriate use cases
* Structure
* Content requirements
* Variants
* Interaction model
* Responsive behavior
* Accessibility requirements
* Animation opportunities
* When not to use it

---

# 15. 06-animation-system/skill.md

Create the animation system.

Define reusable animation patterns including:

```text
FadeIn
FadeUp
FadeDown
FadeLeft
FadeRight
ScaleIn
BlurIn
SlideIn
Reveal
Stagger
Parallax
Float
Marquee
ImageReveal
TextReveal
GradientShift
MagneticHover
Tilt
```

The skill must establish:

* Motion hierarchy
* Duration
* Easing
* Delay
* Stagger
* Hover behavior
* Scroll behavior
* Reduced-motion behavior
* Mobile behavior
* Performance requirements

Animations must never compromise accessibility or performance.

Respect:

```text
prefers-reduced-motion
```

---

# 16. 07-homepage/skill.md

Create the homepage implementation skill.

The skill must:

1. Analyze the approved design system.
2. Analyze the company's purpose.
3. Propose homepage architecture.
4. Select appropriate widgets.
5. Ask for approval according to execution mode.
6. Implement the homepage.
7. Apply animations.
8. Ensure responsive behavior.
9. Perform visual QA.
10. Fix identified issues.

The skill should not always use the same section order.

It must dynamically determine the best information architecture.

---

# 17. 08-about/skill.md

Create the About page skill.

Potential sections may include:

* Hero
* Company Introduction
* Mission
* Vision
* Values
* Story
* Timeline
* Statistics
* Leadership
* Team
* Certifications
* Partners
* CTA

But sections must be selected according to actual company requirements.

---

# 18. 09-contact/skill.md

Create the Contact page skill.

The website is static, therefore do not assume a backend contact API.

Possible approaches:

* Static contact information
* Email links
* Phone links
* Address
* Map link
* Social links
* Static form presentation
* External form provider only if explicitly requested

The skill must not introduce backend dependencies automatically.

---

# 19. 10-services/skill.md

Create the Services listing page skill.

Support:

```text
/services
```

The page must dynamically compose appropriate widgets.

Potential sections:

* Hero
* Service categories
* Service cards
* Benefits
* Process
* Industries
* CTA

---

# 20. 10-services/service-detail.md

Create the individual service page skill.

Support:

```text
/services/[slug]
```

Potential structure:

```text
Hero
Problem
Solution
Capabilities
Features
Benefits
Process
Industries
Technologies
Case Studies
FAQ
CTA
```

The exact structure must be dynamically selected.

---

# 21. 11-products/skill.md

Create the Products listing skill.

Support:

```text
/products
```

The skill should support multiple visual presentations:

* Grid
* Bento
* Editorial
* Featured product
* Product categories

---

# 22. 11-products/product-detail.md

Create the individual product page skill.

Support:

```text
/products/[slug]
```

Potential sections:

```text
Product Hero
Overview
Key Features
Screenshots
Capabilities
Benefits
Use Cases
Architecture
Technology
Comparison
FAQ
CTA
```

The final structure must depend on the product.

---

# 23. 12-legal/skill.md

Create the legal-page skill.

Support pages such as:

```text
/privacy
/terms
/cookie-policy
/disclaimer
```

The pages must visually belong to the same design system.

Do not invent legal claims.

Use placeholders when legal content is unavailable.

---

# 24. 13-content-replacement/skill.md

This skill is extremely important.

During initial website construction, all content should use clearly identifiable placeholder content.

Examples:

```text
Lorem ipsum
Placeholder headline
Placeholder description
Placeholder company name
Placeholder service
Placeholder product
```

Content must be separated from presentation whenever practical.

Use structured content sources such as:

```text
content/
├── site.ts
├── homepage.ts
├── about.ts
├── services.ts
├── products.ts
└── legal.ts
```

Later this skill replaces placeholders with real content.

The skill must:

* Identify all placeholder content.
* Ask for real content where necessary.
* Preserve layout integrity.
* Adjust text length intelligently.
* Generate SEO-aware copy only when explicitly authorized.
* Avoid breaking the design because of content length.
* Identify missing content.

---

# 25. 14-responsive/skill.md

Create a complete responsive QA and implementation skill.

Validate:

```text
Mobile
Tablet
Laptop
Desktop
Large Desktop
```

Check:

* Navigation
* Typography
* Grid
* Spacing
* Images
* Cards
* Buttons
* Forms
* Tables
* Overflow
* Touch targets
* Section order
* Hero composition
* Animation behavior

The skill must fix responsive problems rather than merely report them.

---

# 26. 15-accessibility/skill.md

Create an accessibility audit and remediation skill.

Check:

* Semantic HTML
* Heading hierarchy
* Keyboard navigation
* Focus states
* Color contrast
* ARIA
* Form labels
* Image alt text
* Links
* Buttons
* Interactive widgets
* Reduced motion
* Screen reader behavior

Aim for WCAG 2.2 AA where practical.

The skill should fix issues whenever possible.

---

# 27. 16-seo/skill.md

Create the SEO implementation and audit skill.

Check:

* Page titles
* Meta descriptions
* Canonical URLs
* Open Graph
* Twitter/X metadata
* Structured data where appropriate
* Heading hierarchy
* Internal linking
* Semantic HTML
* Sitemap
* Robots
* Image metadata
* URL structure
* Static rendering
* Indexability

The website must remain fully compatible with Nuxt static generation.

---

# 28. 17-performance/skill.md

Create a frontend performance audit skill.

Check:

* JavaScript size
* CSS size
* Image optimization
* Lazy loading
* Font loading
* Animation performance
* Layout shifts
* Unnecessary dependencies
* Component complexity
* Client-side JavaScript
* Hydration
* Third-party resources
* Network requests

Prefer:

```text
Static HTML
Minimal JavaScript
Optimized images
Lazy loading
CSS-first solutions
Progressive enhancement
```

Avoid unnecessary client-side complexity.

---

# 29. 18-visual-qa/skill.md

Create the final visual quality assurance skill.

This skill must support screenshot-based validation.

Workflow:

```text
Render page
↓
Capture screenshot
↓
Analyze screenshot
↓
Compare with approved design
↓
Identify inconsistencies
↓
Fix implementation
↓
Render again
↓
Repeat until acceptable
```

Check:

* Layout
* Spacing
* Typography
* Colors
* Alignment
* Visual hierarchy
* Component consistency
* Responsive behavior
* Image proportions
* Animation behavior
* Brand consistency

If an original visual reference exists, compare the implementation against its approved visual language.

Do not blindly copy copyrighted website designs.

Extract design principles instead.

---

# 30. Cross-Skill Rules

Every skill must follow these rules.

## Rule 1 — Preserve the Design System

Never introduce random colors, spacing, typography, radius, or shadows.

Use the approved design tokens.

---

## Rule 2 — Preserve User Decisions

If the user approved a design decision, do not silently replace it.

---

## Rule 3 — Avoid Generic Templates

Do not repeatedly produce:

```text
Navbar
Hero
Three Cards
Testimonials
CTA
Footer
```

unless this is genuinely appropriate.

---

## Rule 4 — Content and Design Are Separate

Placeholder content must be replaceable without rewriting the entire UI.

---

## Rule 5 — Mobile Is Not an Afterthought

Every widget must define mobile behavior.

---

## Rule 6 — Accessibility Is Built In

Do not wait until the final audit to consider accessibility.

---

## Rule 7 — Performance Is Built In

Do not introduce expensive effects merely for visual novelty.

---

## Rule 8 — No Unnecessary Backend

The website is static.

Do not introduce APIs, databases, authentication, or server dependencies.

---

# 31. Widget Selection Protocol

When building a page, the agent must first produce a page architecture.

Example:

```text
Homepage

01 HeroSplit
02 LogoCloud
03 FeatureGrid
04 Statistics
05 ServicesGrid
06 ProcessTimeline
07 CaseStudies
08 Testimonials
09 CTASection
```

Then explain briefly why each widget was selected.

In Assisted mode, ask for approval before implementation.

---

# 32. Visual Reference Protocol

If the user provides an image or screenshot:

Analyze:

```text
Composition
Layout
Grid
Typography
Color
Spacing
Shapes
Borders
Shadows
Image treatment
Interaction patterns
Animation language
Visual hierarchy
```

Do not copy:

* Copyrighted text
* Logos
* Brand assets
* Exact content

Use the reference to derive a compatible design direction.

---

# 33. Design Decision Protocol

For major decisions, the agent should provide:

```text
Decision
Reason
Alternatives
Recommendation
```

Example:

```text
Decision:
Use asymmetric split hero.

Reason:
The company positioning is premium and technology-oriented.

Alternative:
Centered editorial hero.

Recommendation:
Asymmetric split hero.
```

---

# 34. Content Protocol

Initial implementation should use placeholder content.

Do not spend excessive effort creating final marketing copy before the content replacement stage.

The initial objective is:

```text
Structure first
Design second
Content later
```

---

# 35. Execution State

Each skill should be able to determine whether it is:

```text
NOT_STARTED
IN_PROGRESS
WAITING_FOR_APPROVAL
COMPLETED
NEEDS_REVISION
```

The orchestrator should use these states.

Do not create a complex external database for state management.

Use simple project documentation or files when appropriate.

---

# 36. Skill Quality Requirements

Every `skill.md` must contain:

```text
# Skill Name

## Purpose

## Role

## Preconditions

## Inputs

## Outputs

## Responsibilities

## Execution Workflow

## Decision Rules

## User Interaction

## Implementation Rules

## Quality Requirements

## Validation

## Completion Criteria

## Failure / Recovery Rules
```

Adapt the sections when necessary.

Do not blindly duplicate irrelevant sections.

---

# 37. Do Not Over-Engineer

The generated Skill System itself should remain maintainable.

Avoid:

* unnecessary abstractions
* redundant skills
* duplicate widget definitions
* overly complicated orchestration
* unnecessary configuration
* unnecessary external dependencies

Prefer clear, composable skills.

---

# 38. Final Verification

After creating all directories and files, verify:

1. Every required directory exists.
2. Every required file exists.
3. No required file is empty.
4. All documentation is in English.
5. Skill numbering is correct.
6. Cross-skill dependencies are coherent.
7. The execution order is documented.
8. Widget catalog exists.
9. Creativity rules exist.
10. Visual-reference workflow exists.
11. Placeholder-content workflow exists.
12. Visual-QA workflow exists.
13. SEO and performance are separate concerns.
14. Accessibility is explicitly covered.
15. Static-site constraints are respected.

---

# 39. Final Response

After completing the work, provide a concise summary containing:

```text
Created:
- Number of directories
- Number of files
- Skill execution order

Architecture:
- Foundation
- Design
- Widgets
- Pages
- Content
- QA

Next step:
Explain which skill should be executed first to begin generating the actual website.
```

Do not build the actual website during this task.

The only objective of this task is to create the complete reusable **Nuxt.js Static Corporate Website Skill System**.
