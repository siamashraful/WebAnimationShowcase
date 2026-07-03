# Product

## Register

brand

## Users

Recruiters, hiring managers, and engineering/design peers who land here from a résumé, GitHub profile, or shared link. They arrive skeptical and time-poor (30–90 seconds of attention), usually on a laptop but sometimes a phone, evaluating one question: can this person actually build motion at this level? A secondary audience is developers who view source to see how the effects are made.

## Product Purpose

Motion Studio is a proof-of-craft: a single-file, zero-dependency animation showcase demonstrating that Framer-grade motion — spring physics, scroll choreography, 3D parallax, a live aurora, a remixable theme system — can be hand-built with vanilla CSS and one rAF loop. Success is the visitor being unable to resist playing: they chase the orb, press Remix, tilt the cards, and leave convinced of the author's capability. The "single file, no build step, no frameworks" constraint is not a limitation — it IS the brag, and it's stated on the page itself (stats section, footer fineprint).

## Brand Personality

Playful, precise, alive. Exuberant motion held together by visible engineering rigor — every effect is bouncy and inviting on the surface but frame-budgeted, write-gated, and physically coherent underneath. The tone is confident showmanship, never corporate: the page performs for you and reacts to you (cursor, scroll, remix), like a machine that is delighted you showed up.

## Anti-references

- **Template/SaaS landing look**: generic hero-metric blocks, identical icon-card grids that exist to fill space, stock gradient washes, anything that reads as a purchased theme. Every section here must earn its place by *demonstrating* a technique, not describing one.
- **Agency maximalism**: WebGL-everything, megabytes of libraries, scroll-jacking, spectacle that fights usability. The identity is the opposite — one file, zero dependencies, native scroll, 60fps on integrated graphics.

## Design Principles

1. **Show, don't tell** — every claim on the page is demonstrated by the page. The parallax card describes the band below it; the spring copy bounces; the counters count. Never add copy about a capability without the capability performing live.
2. **The constraint is the brag** — single-file, dependency-free, no build step is the product's identity. Rejecting a library is a feature decision, not a limitation.
3. **React to the visitor** — motion should answer cursor, scroll, and press. Ambient animation idles near-still (the aurora drifts, it doesn't wobble) so that reactivity is what reads.
4. **Rigor under the play** — exuberance never costs frames. The perf contract (no per-frame layout reads, write-gating, offstage skipping) is as much the showcase as the visuals.
5. **One click changes everything** — the remix system is the signature move. Every new tunable joins the re-roll; nothing on the page is exempt from the theme.

## Accessibility & Inclusion

Best effort, not a formal bar. It's a demo: fix issues when they're egregious (invisible text, unusable controls), keep keyboard operability where it's cheap (the dot, the night switch, and remix are already focusable), but don't hold the art direction hostage to strict AA contrast. `prefers-reduced-motion` support is currently absent; treat it as a nice-to-have candidate, not a blocker.
