# 0004. Oxblood theme as the v1 visual identity

**Status**: accepted, 2026-05-02.

## Context

A pet project that lives on a resume succeeds or fails partly on first visual impression. A bland grey on white default Tailwind layout reads as unfinished; a distinctive theme reads as intentional. The design space was explored up front in a standalone HTML mockup covering all five screens, and that mockup became the reference the implementation was built against.

The chosen theme is named **Oxblood**: a dark surfaced palette built around oxblood `#C03A3A` as primary, sea green `#34D399` as accent, IBM Plex Serif italic for display, Newsreader for numerics, Manrope for UI text, and JetBrains Mono for code. Every design token is exposed as a CSS variable on `:root`, every visual region carries a `data-component` attribute, and the token set drops straight into `tailwind.config.ts`.

The implemented React frontend matches this visual personality rather than embedding the mockup directly.

## Decision

Adopt Oxblood as the v1 visual identity. The tokens are extracted to [`../design/tokens.md`](../design/tokens.md) and to `frontend/tailwind.config.ts` during Phase 0, and the React components are built against those tokens from Phase 3 onwards. Screen layout and interaction detail are recorded in [`../design/wireframes.md`](../design/wireframes.md), keyed to the same `data-component` names the components carry.

## Consequences

**Positive**:

* The token set is the single source of truth for visual decisions, defined once in `frontend/src/styles/tokens.css` and documented alongside it.
* Tailwind tokens, component anatomy, and screen layout are all written down, so implementation never has to guess at a value.
* Reskinning is a single `:root` edit rather than a hunt through component files.

**Negative**:

* The documented tokens must be kept in sync with the implemented React app whenever the design changes.
* A fresh visual exploration is a much heavier lift than a single token tweak.
* The theme is opinionated; a future contributor who dislikes oxblood as a primary color will need to reskin via the CSS variables.

The tradeoffs are accepted. v1 ships with Oxblood; future versions may revisit.
