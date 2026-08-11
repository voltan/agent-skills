# Project Configuration

> **How to use this file:** copy it as `project-config.md` into the website project root (inside the `project-config/` folder), then fill in every section you know. Leave unknown sections empty — the agent will propose them during Discovery (Skill 02) and Design System (Skill 03) and ask for approval before implementing.
>
> **Assets:** put logo/brand files in `project-config/brand/` and design sample images in `project-config/references/`, then reference them below by relative path (e.g. `brand/logo.svg`, `references/hero-style-01.jpg`).

---

## 1. Company & Brand Identity

- **Company name:** `[e.g. Acme Corporation]`
- **Tagline:** `[one line that summarizes the company]`
- **Industry / market:** `[e.g. B2B software, architecture, healthcare]`
- **Short company description (1–3 sentences):** `[what the company does, for whom]`
- **Brand personality (3–5 adjectives):** `[e.g. premium, trustworthy, innovative, warm, technical]`

## 2. Website Purpose & Audience

- **Primary purpose:** `[e.g. generate leads, showcase services, establish credibility]`
- **Target audience:** `[who visits the site and what they need]`
- **Required pages:** `[home, about, contact, services, products, legal — add or remove]`

## 3. Design Source (Figma or Screenshots)

Provide the design to implement. You can give a **Figma link**, **screenshots**, or both. If a Figma link is provided, the agent loads `skills/19-figma-to-nuxt/skill.md` and treats the Figma design as the **visual source of truth** (colors, typography, layout, components).

- **Figma URL:** `[paste the Figma link here — a public/shared link the agent can open, or a Dev Mode link]`
- **Figma scope:** `[FULL_WEBSITE / NEW_PAGE / PAGE_REDESIGN / COMPONENT_REDESIGN / DESIGN_SYSTEM_UPDATE — or leave empty]`
- **Screenshots / exported frames:** `[file names in references/, e.g. references/figma-home-desktop.png — add one per page/viewport]`
- **Access note:** `[if the Figma is not publicly viewable, note that the agent must work from the screenshots/exported assets only]`

Anything the design does not specify (or you leave out here) is proposed by the agent for approval.

## 4. Design Direction

- **Preferred direction (optional):** `[Premium Minimal / Modern Corporate / Creative Editorial / other — or leave empty and let the agent propose 3 options in Skill 03]`
- **Feel / mood (optional):** `[calm, energetic, luxurious, playful, technical, editorial…]`
- **Anything to avoid:** `[e.g. dark backgrounds, gradients, generic stock photos]`

## 5. Colors

Define the palette the site must use. Fill in hex values; leave rows empty if the agent should propose them. Mark which colors are **fixed brand colors** (must not change) vs. **suggestions**.

| Role | Hex | Fixed? | Notes |
| :--- | :--- | :--- | :--- |
| Primary | `#____` | ☐ | main brand color, buttons, links |
| Secondary | `#____` | ☐ | supporting color |
| Accent | `#____` | ☐ | highlights, CTA emphasis |
| Background | `#____` | ☐ | page background |
| Surface | `#____` | ☐ | cards, sections |
| Text | `#____` | ☐ | body text |
| Text muted | `#____` | ☐ | secondary text |
| Border | `#____` | ☐ | hairlines, dividers |
| Success | `#____` | ☐ | semantic |
| Warning | `#____` | ☐ | semantic |
| Danger | `#____` | ☐ | semantic |
| Focus | `#____` | ☐ | focus rings |

- **Dark mode:** `[not needed / needed / optional]` — if needed, provide or let the agent derive dark variants of the palette above.
- **Gradient / special colors (optional):** `[e.g. hero gradient from primary to accent]`

## 6. Typography

- **Heading font:** `[e.g. "Sora" via Google Fonts]`
- **Body font:** `[e.g. "Inter" via Google Fonts — or system stack]`
- **Weights used:** `[e.g. 400 / 600 / 700]`
- **Font fallbacks:** `[e.g. sans-serif, Arial]`
- **Tone / style:** `[e.g. tight tracking for headlines, generous line height for body]`

## 7. Layout & Shape Preferences

- **Spacing density:** `[compact / standard / spacious]`
- **Border radius style:** `[sharp / subtle / rounded / pill buttons]`
- **Grid character:** `[structured grid / asymmetric editorial / bento tiles / split-heavy]`
- **Container feel:** `[narrow centered / wide full-bleed]`
- **Anything else about structure:** `[e.g. strong hero on every page, sticky navigation]`

## 8. Logo & Brand Assets

- **Logo files:** `[e.g. brand/logo.svg, brand/logo-dark.svg, brand/favicon.svg]`
- **Logo usage rules:** `[e.g. white version on dark backgrounds; min size; clear space]`
- **Other brand assets:** `[icons, patterns, textures — paths into brand/]`

## 9. Design References (samples)

For each design sample image in `project-config/references/`, note what the agent should extract (layout, colors, typography, mood) — **principles only, never a pixel copy**.

| File | What to extract |
| :--- | :--- |
| `references/hero-style-01.jpg` | `[e.g. split hero, oversized type, muted palette]` |
| `references/card-layout.png` | `[e.g. card grid, spacing rhythm]` |

- **Reference websites (URLs):** `[optional — describe what you like about each]`

## 10. Animation Preferences

- **Motion character:** `[calm / energetic / playful / minimal — or leave empty]`
- **Wanted effects:** `[e.g. scroll reveals, marquee logos, hover micro-interactions]`
- **Avoid:** `[e.g. parallax, heavy motion, autoplaying anything]`

## 11. Content Notes

Provide anything the site must say — the agent uses this as primary content during Skill 13 and never invents the rest.

- **Services / offerings:** `[list with short descriptions]`
- **Products:** `[list with short descriptions]`
- **Key statistics / proof:** `[numbers, certifications, awards — only real ones]`
- **Contact data:** `[address, phone, email, social links, map link]`
- **Legal pages needed:** `[privacy / terms / cookie policy / disclaimer — which ones]`
- **Existing content or copy:** `[paths to provided text, or "none — placeholder content first"]`

## 12. Constraints & Notes

- **Technical constraints:** `[e.g. must stay fully static, no forms backend, specific domains]`
- **Design constraints:** `[e.g. must keep the existing logo, use only these fonts]`
- **Anything else the agent must know:** `[free text]`
