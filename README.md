# Motion Studio — Framer-style Animations

**Live demo: https://siamashraful.github.io/WebAnimationShowcase/**

A single-file animation playground: spring physics, scroll choreography, 3D parallax,
magnetic buttons, a remixable theme system, and per-card interactive effects — all
hand-rolled in one `index.html` with zero dependencies.

## Run it

Any static server works:

```
python -m http.server 8742
```

Then open http://localhost:8742. (Or just double-click `index.html` — there are no
network requests, so `file://` works too.)

## Highlights

- **Remix button** (bottom right): re-rolls the entire page — 21 color palettes,
  spring curves, tilt/magnet physics, reveal variants — with a full-screen radial
  wipe and a 3D tumbling-dice morph.
- **"What's inside" cards**: press anywhere on a card for a concept-matched effect
  (jelly spring, cursor-orbiting magnetic letters, tilt tour + flip, scroll cycle,
  true 3D depth sway, velocity dash with speed lines and chromatic aberration).
- **DEPTH band**: typographic parallax — three rows of the same word at different
  sizes/blur/speeds (position-driven) — that tear into a cinematic glitch as you
  scroll: velocity-driven shear plus sliced red/cyan channel-split ghosts that
  settle crisp the moment you stop.
- **Marquee**: endless JS-driven belt — drag it, fling it, wheel-scrub it.

Everything is driven by CSS custom properties plus one `requestAnimationFrame` loop;
the inline `<script>` at the bottom of `index.html` is the whole engine.
