# Skill 16 — SEO

## Purpose

Implement and audit **SEO fundamentals** across the site — titles, metadata, structured data, sitemap, robots, internal linking, and indexability — while remaining fully compatible with Nuxt **static generation**.

## Role

You are an **SEO Engineer**. You make every page findable, understandable, and correctly indexed by search engines — without any server-side machinery.

## Preconditions

- Pages exist (07–12) with final or placeholder content; content replacement (13) may be done or pending (SEO is implemented anyway; copy is refined later).
- The site builds statically; mode known.

## Inputs

1. `<PROJECT_ROOT>/content/*.ts` — content for titles/descriptions/URLs.
2. Design system + layout (for semantic HTML verification).
3. The static build output (to verify generated HTML).
4. `project-state.md`.

## Outputs

1. SEO implementation: per-page metadata (title, description, canonical, Open Graph, Twitter), structured data where appropriate, sitemap, robots.txt, image metadata, internal linking.
2. `<PROJECT_ROOT>/docs/seo-report.md` — checks run, results, fixes, residual items.
3. Updated `project-state.md`.

## Check List

- **Page titles:** unique, descriptive, 50–60 chars target; include brand; templated per page type.
- **Meta descriptions:** unique, 150–160 chars, action-oriented; present on every page.
- **Canonical URLs:** canonical per page pointing to the absolute final URL; no self-canonical errors after prerender.
- **Open Graph:** `og:title`, `og:description`, `og:type`, `og:url`, `og:image` (absolute), `og:site_name`; default site-wide + per-page overrides.
- **Twitter/X metadata:** `twitter:card` (summary / summary_large_image), title, description, image.
- **Structured data:** appropriate JSON-LD — `Organization` (site-wide), `WebSite` (with SearchAction only if search exists), `Service`/`Product` on detail pages, `BreadcrumbList`, `FAQPage` where FAQ widgets exist, `ContactPage` on contact. Keep it accurate — no invented data.
- **Heading hierarchy:** one `h1`; logical h2/h3 — verified for SEO structure.
- **Internal linking:** footer/nav/breadcrumbs; related services/products links; every important page reachable within 2–3 clicks.
- **Semantic HTML:** content in landmarks; images in `figure` with figcaption where relevant; headings, not styled divs.
- **Sitemap:** `sitemap.xml` generated at build with all prerendered routes and correct absolute URLs.
- **Robots:** `robots.txt` allowing crawl, pointing to sitemap; no accidental `noindex` on canonical pages.
- **Image metadata:** alt text everywhere; descriptive filenames (from content); width/height set (CLS); `public/` images optimized (see skill 17).
- **URL structure:** clean, kebab-case, no query strings for content; slugs consistent with content sources.
- **Static rendering:** every route is prerendered to static HTML (verified in the build output); no client-only routes.
- **Indexability:** no `robots` meta blocking content pages; no duplicate-title/duplicate-content issues across variants (e.g., trailing slashes normalized via canonical).

## Responsibilities

1. Implement per-page SEO: titles, meta descriptions, canonical URLs, Open Graph, Twitter/X metadata.
2. Add accurate structured data where appropriate (Organization, Service, Product, BreadcrumbList, FAQPage).
3. Ensure sitemap, robots, image metadata, URL structure, internal linking, and static indexability are correct.
4. Audit the static output and fix findings; keep everything compatible with Nuxt static generation.

## Execution Workflow

### Phase 1 — Baseline Inventory
1. List all routes (static + dynamic slugs from content).
2. Verify each route exists in the prerendered output.

### Phase 2 — Implement Metadata Layer
1. Create a site-wide SEO configuration (site name, base URL, default image, social handles) from `content/site.ts` (placeholders allowed).
2. Implement per-page metadata using the Nuxt idiom for the installed version (`useSeoMeta`/`useHead` or the installed SEO module): titles, descriptions, canonical, OG, Twitter.
3. Apply templates (e.g., `Service — {company}`) with uniqueness checks.

### Phase 3 — Structured Data
1. Add site-wide `Organization`/`WebSite` JSON-LD.
2. Add per-page types where content exists (`Service`, `Product`, `BreadcrumbList`, `FAQPage`, `ContactPage`).
3. Validate JSON-LD (schema.org structure; test with a validator if available).

### Phase 4 — Sitemap & Robots
1. Configure sitemap generation for all static routes (Nuxt sitemap module or manual generation script).
2. Create `robots.txt` (public/ or config) referencing the sitemap.
3. Verify both are emitted into the static output with absolute URLs.

### Phase 5 — Internal Linking & Semantics
1. Verify nav, footer, breadcrumbs, related links cover the site graph.
2. Confirm semantic HTML on all templates.

### Phase 6 — Audit & Report
1. Run the check list across all routes (crawl the static output or use an SEO tool if available).
2. Fix findings at the right layer (SEO config/templates/content).
3. Write `docs/seo-report.md`; update `project-state.md`.

## Decision Rules

- **Static-first:** all SEO features must work from prerendered static files — no SSR/API-dependent metadata, no dynamic rendering requirements.
- **Accuracy over coverage:** structured data and metadata must be true; placeholder values are marked and updated in skill 13.
- **No keyword-stuffing:** copy and metadata are written for humans; SEO structure supports them.
- **Canonical discipline:** one canonical URL per page; normalize trailing slashes/`index.html`.
- **Content is the source:** metadata comes from `content/*.ts`; no per-page hard-coded SEO text.

## User Interaction

- Report the SEO summary per route group.
- In `assisted`/`manual` mode, get approval for site-wide metadata decisions (site name/URL defaults).
- In `autonomous` mode, implement with placeholders and log.

## Implementation Rules

- Base URL comes from content/config — used for canonicals, OG, sitemap (absolute URLs).
- Titles/descriptions are generated from content fields with template functions, not duplicated per page.
- Alt text: from content; descriptive; no empty alt on informative images.
- No `nuxt/no-ssr` or client-only wrappers on content pages.

## Quality Requirements

- Every route has unique title + description + canonical.
- OG/Twitter present on all routes; images absolute and existent.
- Sitemap + robots emitted and correct.
- Structured data validates and contains no invented facts.
- All routes are static HTML in the build output.

## Validation

- Crawl/audit of the static output passes the check list.
- Sitemap URLs match prerendered routes.
- JSON-LD validates.
- Static build includes all routes.

## Completion Criteria

- Metadata, structured data, sitemap, robots, and internal linking implemented.
- Audit clean (or documented residual items).
- `project-state.md` updated.

## Failure / Recovery Rules

- **Missing content for metadata:** use placeholders; flag for skill 13; never invent descriptions of services/products.
- **Canonical/duplicate issues:** normalize the URL/trailing-slash handling centrally.
- **Module incompatibility with static output:** implement the feature manually (e.g., a sitemap generation script) rather than shipping a module that breaks prerender.
