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

- **Remix button** (bottom right): idles as a Siri-style orb — a dark glass
  sphere with a hot core and palette-tinted blobs swirling at never-repeating
  periods — then re-rolls the entire page on press: 21 color palettes, spring
  curves, tilt/magnet physics, reveal variants, even the orb's own rhythm —
  with a full-screen radial wipe and a 3D tumbling-dice morph (the orb shrinks
  into the die's first pip; the die is frosted glass like the pill).
- **"What's inside" cards**: press anywhere on a card for a concept-matched effect
  (jelly spring, cursor-orbiting magnetic letters, tilt tour + flip, scroll cycle,
  true 3D depth sway, velocity dash with speed lines and chromatic aberration).
- **DEPTH band**: typographic parallax — three rows of the same word at different
  sizes/blur/speeds (position-driven) — that tear into a cinematic glitch as you
  scroll: velocity-driven shear plus sliced red/cyan channel-split ghosts that
  settle crisp the moment you stop.
- **Liquid-glass nav & buttons** (ported from a shadcn LiquidButton to vanilla
  CSS): the nav is one merged pill that splits into two on scroll, with a hover
  underline that glides between links and parts their neighbours. Tinted buttons
  frost, ghost buttons stay clear, all over a beveled glass lens (backdrop blur
  + tint + layered inset-shadow rims — one deterministic recipe that renders the
  same in every engine).
- **Night mode**: an iOS-style switch at the end of the nav pill — the sun knob
  slides on the page's spring, stretches while held, and eclipses into a
  cratered moon as stars blink into the track. Twelve charcoal paper tints
  mirror the twelve daylight ones, so Remix keeps re-tinting the room after
  dark; the choice is remembered, and first visits follow your OS setting.
- **Split-flap stats heading**: the "by the numbers" headline resolves with a
  letter scramble — each glyph spins through a reel and locks left-to-right with
  a soft blur/fade focus-in.
- **Marquee**: endless JS-driven belt — drag it, fling it, wheel-scrub it.

Everything is driven by CSS custom properties plus one `requestAnimationFrame` loop;
the inline `<script>` at the bottom of `index.html` is the whole engine.
