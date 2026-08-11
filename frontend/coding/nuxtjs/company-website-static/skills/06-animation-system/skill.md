# Skill 06 — Animation System

## Purpose

Define and implement the **animation system**: a fixed vocabulary of reusable animation patterns, a motion hierarchy, and strict motion rules — so every animation on the site is purposeful, consistent, performant, and accessible.

## Role

You are an **Animation Engineer and Motion Designer**. You build the motion primitives and the rules that govern them. You do not decorate pages arbitrarily; you provide the toolkit pages use with discipline.

## Preconditions

- Skill 03 completed (motion duration/easing tokens exist in the design system).
- Skill 04 completed (layout primitives exist; animations attach to them).
- Mode known from `project-state.md`.

## Inputs

1. `<PROJECT_ROOT>/design-system/tokens.*` — motion tokens (durations, easings).
2. `<PROJECT_ROOT>/design-system/design-direction.md` — the approved motion character.
3. `creativity-rules.md` (03) — motion must serve communication.
4. `<PROJECT_ROOT>/project-state.md`.

## Outputs

1. Animation primitives in `<PROJECT_ROOT>/components/anim/` (or equivalents per installed Nuxt/Vue version):
   - `AnimReveal` (scroll-triggered reveal wrapper) and/or a `useReveal` composable.
   - Pattern components for the patterns below.
2. `<PROJECT_ROOT>/docs/animation-system.md` — pattern reference, motion hierarchy, durations/easings, reduced-motion rules.
3. Updated `project-state.md`.

## Animation Pattern Vocabulary

Implement the following reusable patterns:

```text
FadeIn        FadeUp         FadeDown       FadeLeft
FadeRight     ScaleIn        BlurIn         SlideIn
Reveal        Stagger        Parallax       Float
Marquee       ImageReveal    TextReveal     GradientShift
MagneticHover Tilt
```

Each pattern is implemented once, as a primitive, and reused everywhere. Pages and widgets reference patterns; they never re-implement motion.

## Motion Hierarchy

Define and document a hierarchy of motion intensity:

1. **Micro-interactions** — hover/focus/active responses (fastest, always local). Examples: link underline, button press, icon nudge.
2. **Entrance reveals** — scroll-triggered content entry (standard). Examples: FadeUp, Stagger, ImageReveal.
3. **Scene/story motion** — large scroll-linked effects (rarest, most purposeful). Examples: Parallax, Marquee, scroll storytelling.
4. **System transitions** — page/state transitions (modal, drawer, header states).

Rule: higher levels are used more sparingly. A page with everything animating at level 3 has no hierarchy.

## Motion Specifications

### Duration
- Use the design-system motion tokens (e.g., fast 120–180ms, standard 250–350ms, slow 500–700ms, story 800ms+). Never invent durations outside the tokens.
- Micro-interactions: fast. Entrance reveals: standard. Story effects: slow, rare.

### Easing
- Use the approved easing tokens (e.g., `ease-out` for entrances, `ease-in-out` for state changes, `ease` cubic-bezier for expressive motion). No random easings.

### Delay and Stagger
- Stagger increments come from the token scale (e.g., 60–120ms between items); total stagger never exceeds ~600ms without a reason.
- Delay is used only to sequence related elements — never to make pages feel slow.

### Hover Behavior
- Hover effects are micro-interactions: transform/opacity/color only, GPU-friendly, duration fast, and must not shift layout of surrounding elements.
- Every hover effect must also trigger on keyboard focus (focus-visible).

### Scroll Behavior
- Scroll-triggered reveals use IntersectionObserver (or the Nuxt/Vue idiom for the installed version), fire once by default (re-trigger optional and documented), and never block content: content is visible in DOM before animation (progressive enhancement).
- Parallax is subtle (e.g., 5–15% offset), transform-based, and disabled on `prefers-reduced-motion`.

### Reduced Motion
- **Mandatory:** everything respects `prefers-reduced-motion`. Implement a global guard: reduced-motion users get static content with no entrance/scroll/story motion and minimal transitions (opacity-only or none).
- Reduced-motion must be handled with a media-query-driven CSS approach first (works without JS).

### Mobile Behavior
- On small screens: disable or simplify Parallax, Marquee speed is reduced, MagneticHover and Tilt are disabled (no hover), touch targets unaffected by motion.
- Keep entrance reveals — they aid comprehension — but shorten durations.

### Performance Requirements
- Animate only `transform` and `opacity` (GPU-composited). Never animate `width`, `height`, `top/left`, `margin`, `box-shadow` in loops.
- No layout thrash: batch reads/writes, avoid forcing reflows.
- Total animation JS is tiny: one observer utility + CSS transitions/animations; prefer CSS-only where possible.
- No animation work on elements below the fold until they approach the viewport (lazy reveal).

## Responsibilities

1. Define the motion hierarchy and the animation pattern vocabulary.
2. Implement reusable animation primitives (reveal utility, pattern components) with performance and reduced-motion rules built in.
3. Document durations, easings, delays, stagger, hover/scroll behavior, mobile behavior, and performance requirements.
4. Guard the system: pages and widgets may only use the defined patterns; new patterns are added to the system before use.

## Execution Workflow

### Phase 1 — Read the Motion Tokens
1. Read duration/easing tokens from the design system; if absent, add them (recorded decision).
2. Read the approved direction's motion character (calm, energetic, editorial…).

### Phase 2 — Build Primitives
1. Implement the reveal primitive (`AnimReveal`/`useReveal`) with IntersectionObserver, one-shot default, reduced-motion guard, and configurable pattern/delay/stagger.
2. Implement the pattern vocabulary (FadeUp, Stagger, Parallax, Marquee, ImageReveal, TextReveal, GradientShift, MagneticHover, Tilt, etc.) on top of the primitives.
3. Implement micro-interaction utilities (hover/focus-visible helpers) for widgets.

### Phase 3 — Define the Rules Document
1. Write `docs/animation-system.md`: patterns, hierarchy, durations, easings, stagger table, reduced-motion policy, mobile policy, performance checklist.

### Phase 4 — Verify
1. Build a motion test page (temporary) exercising patterns at mobile + desktop and with reduced-motion emulation.
2. Confirm no layout shift and no animation of non-composited properties.
3. Update `project-state.md`.

## Decision Rules

- **Purpose test (creativity rule):** an animation must serve communication, hierarchy, brand, or interaction — otherwise it is removed.
- **Vocabulary-only:** pages/widgets may only use the defined patterns; new patterns are added to the system (and documented) before use.
- **Restraint:** if in doubt, less motion.
- **Never accessibility-hostile:** any animation that conflicts with reduced motion, contrast, or readability is removed.
- **Never performance-hostile:** any animation that costs layout or main-thread time for spectacle is removed.

## User Interaction

- In `assisted`/`manual` mode, propose the motion character and pattern set for approval (aligned with the approved design direction).
- In `autonomous` mode, implement the recommended motion language and log it.

## Implementation Rules

- CSS-first: transitions/animations in CSS with media queries for reduced motion; JS only for scroll observation and any pointer-dependent effects (Magnetic/Tilt).
- `@media (prefers-reduced-motion: reduce)` globally disables decorative animation.
- All patterns accept `disabled`/`reduced` overrides.
- No animation library dependency unless explicitly approved; hand-rolled primitives are preferred (performance + control).

## Quality Requirements

- Patterns are reusable and consistent (same timing language site-wide).
- Reduced motion passes: no entrance/parallax/marquee under the media query.
- No layout shift (CLS) from animations.
- All animated properties are `transform`/`opacity`.
- Total animation bundle weight is negligible.

## Validation

- Reduced-motion emulation: page renders statically with content fully visible.
- Mobile: parallax/tilt/magnetic disabled; reveals still work.
- Performance: no jank under a simple scroll test; no forced reflow warnings.
- Purpose test: every animated element on a sample page has a documented reason.

## Completion Criteria

- Pattern vocabulary implemented and documented.
- Motion hierarchy, durations, easings, stagger, reduced-motion, and mobile rules defined.
- Motion test passes.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Jank on a device:** remove or simplify the offending animation; never disable reduced-motion to "fix" jank.
- **Pattern missing:** add it to the system (vocabulary) before use; never inline ad-hoc animation in a page.
- **Conflict with direction:** if the motion character clashes with the approved direction, ask the user (or record an autonomous decision) and update `docs/animation-system.md`.
