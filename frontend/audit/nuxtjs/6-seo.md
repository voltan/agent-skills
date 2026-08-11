# Nuxt SEO Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Verify that indexable pages expose complete, correct, server-rendered SEO metadata and content, and that technical SEO configuration (canonical, robots, sitemap, structured data, redirects, status codes) is correct.

## Scope

SSR HTML / initial HTML, title, description, canonical, robots, Open Graph, social metadata, structured data, sitemap, robots.txt, hreflang, pagination, redirects, status codes, duplicate content, dynamic metadata, indexability, `noindex`, client-only content, JavaScript-dependent content, canonical conflicts.

## Framework Context

Nuxt 4: `useHead`/`useSeoMeta` (composables), `app.head` (nuxt.config.ts), `unhead` under the hood; `@nuxtjs/seo` module ecosystem; `robots.txt`/`sitemap` via modules or `public/`; route rules (`crawler`, `ssr`) affect crawling. SEO-critical content must be present in the initial server-rendered HTML for indexable routes.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-06-seo-review.md`; shared log initialized.
3. A runnable/deployed app to inspect rendered HTML is strongly preferred.

## Audit Objectives

1. For every indexable route, verify the initial HTML contains title, meta description, canonical, OG tags, and the primary content.
2. Verify robots/sitemap/canonical/hreflang/redirects correctness.
3. Detect indexability defects (noindex leaks, client-only content, canonical conflicts, duplicate content).
4. Verify dynamic metadata (`useSeoMeta` per route) is actually rendered per page, not just configured.

## Audit Rules

1. **Initial HTML completeness (indexable pages):** verify title, meta description, canonical, Open Graph, and critical content text exist in the SSR HTML. If content is client-only (inside `<ClientOnly>`, fetched in `onMounted`, or `ssr: false` pages), flag HIGH for indexable routes — crawlers may not execute JS reliably.
2. **Missing/duplicate metadata:** flag pages without title/description; flag identical titles across routes (duplicate content signal).
3. **Canonical:** flag missing canonical, canonical pointing to non-canonical URLs (query-string variants, `#` fragments), or multiple conflicting canonicals. Verify canonical matches the page's effective URL (scheme/host/path).
4. **Robots & indexability:** verify `robots.txt` correctness (allow/disallow intent, sitemap reference); flag accidental `noindex` on pages that should be indexed and missing `noindex` on private/duplicate pages (e.g., auth, admin, filter-parameter spam). Verify `X-Robots-Tag` headers where used.
5. **Open Graph / social metadata:** verify `og:title`, `og:description`, `og:image`, `og:url` presence and correctness on shareable pages; flag OG images that 404 or are enormous.
6. **Structured data:** verify JSON-LD presence and validity for content types that warrant it (articles, products, FAQ, breadcrumbs); validate against schema.org. Invalid/duplicate JSON-LD is a finding.
7. **Sitemap:** verify sitemap generation (`@nuxtjs/sitemap` or equivalent), that it lists only indexable canonical URLs, excludes `noindex` pages, and is referenced in robots.txt. Missing/empty/stale sitemap on content-heavy sites is MEDIUM+.
8. **hreflang:** for multilingual apps, verify `hreflang` alternates (including `x-default`) and that each locale's canonical set is consistent; missing hreflang on localized content is MEDIUM.
9. **Pagination & duplicate content:** verify rel canonical on paginated series, sensible prev/next, and that filter/sort combinations don't create indexable duplicate content (canonicalization or noindex).
10. **Redirects & status codes:** verify server-side redirects (Nitro route rules) return proper codes (301 permanent / 302 temporary); flag client-side `navigateTo` used where a server redirect is needed; flag broken internal links to moved URLs (no redirect). Verify 404s return 404 (Nitro default) and that catch-all error pages don't 200.
11. **Dynamic metadata rendering:** verify `useSeoMeta`/`useHead` per-route data is reflected in the rendered HTML (execute the app and inspect), not just present in source.
12. **Client-only content on indexable pages:** flag SEO-critical content only rendered client-side (JS-dependent content) — HIGH for indexable routes (see `5-ssr.md` strategy verdicts).

## Detection Logic

1. Extract route map; identify indexable routes (public, no `noindex`, not auth/admin).
2. For each indexable route: inspect `useSeoMeta`/`useHead` calls and page content rendering.
3. Capture rendered HTML for representative routes; verify metadata and content presence.
4. Inspect `robots.txt`, sitemap output, route rules (`crawler`, redirects), `public/`.
5. Validate structured data JSON-LD with a validator.

## Evidence Requirements

- Captured `<head>` output for the audited routes (title/description/canonical/OG).
- The source file/line producing each metadata item.
- robots.txt/sitemap content excerpts.
- Redirect/status-code observations (curl `-I`).

## Severity

- HIGH: indexable route without SSR metadata/content; conflicting canonicals; incorrect 200-on-error behavior; sitemap poisoning (listing `noindex` pages).
- MEDIUM: missing OG image, broken structured data, missing hreflang, pagination canonical defects.
- LOW/INFO: minor metadata quality (short titles, duplicate descriptions).

## False Positives

- SPA-only apps (Nuxt with `ssr: false` globally) are not indexable by default — do not report missing SSR content unless the app is intended to be indexed (CSR-only indexing via Googlebot JS rendering is possible but not guaranteed; evaluate intent).
- `noindex` on genuinely private pages is correct.
- Metadata in `app.head` as defaults is fine when per-route overrides exist.

## Remediation

- Ensure indexable routes render metadata and content server-side; move client-only content server-renderable or adjust route rules.
- Fix canonical/hreflang/robots/sitemap configuration; validate structured data.
- Replace client-side redirects with Nitro route-rule redirects (301/302); ensure error pages return proper status codes.

## Validation

Re-capture the rendered HTML/headers after fixes; run a structured-data validator; verify robots/sitemap reflect the changes.

## Cross-Layer Considerations

- Redirect targets and status codes may be managed by the backend/CDN — coordinate (`Cross-Layer`, `8-infrastructure.md`, backend infra skills).
- Indexable content depends on the rendering strategy (SSR vs CSR) — reconcile with `5-ssr.md`.
- Dynamic data-driven pages (product/listing) rely on backend data availability at render time — reference backend API/DB skills for empty/error states.

## References

- https://nuxt.com/docs/4.x/recipes/seo-meta (or current), https://nuxt.com/modules/seo
- https://developers.google.com/search/docs (Google Search Central)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-06-seo-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

SEO audits fail when they inspect source config instead of rendered output — the mandatory rendered-HTML evidence rule is the antidote. The indexable-route classification (route map + intent) prevents both under- and over-reporting, and the explicit SPA caveat keeps the skill honest about Nuxt apps that legitimately disable SSR.
