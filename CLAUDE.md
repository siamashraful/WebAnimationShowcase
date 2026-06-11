# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file animation playground ("Motion Studio") — Framer-style spring physics, scroll choreography, 3D parallax, magnetic buttons, and a remixable theme system. The entire site is `index.html`: a `<style>` block plus one inline IIFE `<script>` at the bottom. Zero dependencies, no build step, no tests, no lint. Live demo: https://siamashraful.github.io/WebAnimationShowcase/

## Run it

Any static server works:

```
python -m http.server 8742
```

There are no network requests, so opening `index.html` via `file://` also works. Verify changes by loading the page and checking the browser console — there is no other tooling.

## Architecture

Three cooperating systems, all inside `index.html`:

**1. CSS custom properties are the theme/physics surface.** `--accent` / `--accent-2` / `--accent-3` (default: Cobalt — deep cobalt → azure → sky), `--paper`, `--radius`, `--spring`, `--ease-out`, plus the two liquid-glass lens-bevel shadow stacks `--glass-rim-dark` / `--glass-rim-light`, all on `:root`, parameterize nearly every rule. New UI must use these variables (not hard-coded colors or curves) or it won't respond to the remix system.

**2. One `requestAnimationFrame` loop (`frame()`) drives all continuous motion** — cursor trail, magnetic-button springs, scroll progress, the nav `.scrolled` toggle, the marquee belt, typographic parallax (position-driven) plus the DEPTH band's velocity glitch (speed-driven shear, toggling the `.glitching` class that spawns the sliced RGB ghost copies), and the magnet-card letter orbit. Add new continuous effects inside this loop rather than starting another one; it computes a clamped frame-time delta (`dtf`) so motion stays time-accurate across tab switches. The loop has a strict perf contract: it never reads layout (geometry is cached by `measure()`, refreshed on resize — never call `getBoundingClientRect`/`scrollWidth`/`scrollHeight` per frame), it skips style writes when the rounded value hasn't changed (the `w*`-prefixed caches), and it skips sections flagged off-screen in the `onScreen` map by an IntersectionObserver, which also toggles `.offstage` to pause infinite CSS keyframes (hero blobs, scroll hint). The hidden remix tooltip's holo loops are paused via `.remix-tip:not(.show)`. New per-frame work must follow the same rules. Discrete entrances use IntersectionObserver instead: `.reveal` / `.stagger` (re-triggerable, with hysteresis and a -110px bottom rootMargin — the comment block above `io` explains why both numbers are what they are) and the animated counters.

**3. `remix()` re-rolls every animation parameter and replays everything**: 21 palettes, paper tints, corner radius, spring/ease curves, the live `cfg` physics object (`tilt`, `magnet`), blob sizes, marquee speed/direction, reveal variants, stagger delays, hero headline timing, counters. Any new tunable you add should be re-rolled here — the feature's promise is that one click changes the whole page. The button click choreographs: pill-to-die morph → confetti → radial wipe in the incoming palette → `remix(pal)` runs while the page is covered → fade → morph back.

### Card press effects (`fx-*`)

Clicking a "What's inside" card plays a concept-matched whole-card performance. The contract:

- JS adds `fx-<name>` (from the icon's `data-fx`) to the `.card`; CSS keyframes choreograph the card, icon, `h3`, and `p` together.
- All press-effect keyframes are named `fx-*` — the `animationend` handler removes the class only once no `fx-*` animation is still running, so a misnamed keyframe breaks cleanup.
- `fx-cooldown` disarms hover tilt until the pointer leaves and re-enters; tilt also pauses while any `fx-` class is on the card.
- The magnet card is the exception: a per-frame follow mode (`magState` in the rAF loop), not a keyframe animation. Its title is split into `.mag-w` / `.mag-l` spans at startup so letters can detach without reflowing the text.

### Nav (merge/split + hover underline)

`nav` is just a layout rail (`pointer-events: none`); only the two glass pills (`.logo`, `.links`) catch the cursor. Their resting layout is `space-between` — the *split* state, pinned to the edges. The merged-at-top look is an override (`nav:not(.scrolled)`) that slides them to meet at screen centre with `transform: translateX(calc(50vw - 100% …))`, where `100%` resolves to the element's own width, so the maths is font-/remix-width-agnostic and they always abut exactly at centre (inner corners squared to read as one segmented bar). The `.scrolled` class — toggled in the rAF loop past 60px — drops the transform, so the pills spring back apart on scroll; do **not** drive this from a second scroll listener. Logo and links heights are tuned to match while merged (the links pill's height comes from the anchors' own padding *plus* the container padding — don't add extra vertical padding to "match," it overshoots).

Link hover is a single underline element, `.nav-ind`, prepended into `.links` and placed/sized in JS from the hovered link's offset box. Layout reads happen only on the one-off `pointerenter` (never per frame — same perf contract as the loop). Moving between links re-aims it with `opacity` left up, so it glides rather than blinks; `pointerleave` on the container fades it and resets the spread. Hovering also parts the neighbours via `translateX` (nearest most, tapering) — transforms don't change `offsetLeft`, so the underline stays aligned to the hovered word regardless. The whole block is guarded behind `matchMedia('(hover: hover)')` so touch is inert. The per-link `data-hover` cursor-ring grow is independent of all this. The pills themselves use the liquid-glass treatment (below).

### Liquid glass (nav pills + buttons)

Ported from a shadcn `LiquidButton` into vanilla CSS. The look is two parts. A **lens bevel** — a stack of tight inset shadows held in `--glass-rim-dark` (black insets, for *clear* glass over light content: nav pills, the ghost button) and `--glass-rim-light` (white specular insets, for *frosted* glass on tinted/dark material). And a **backdrop blur**. Tinted buttons (the primary CTA, the Remix button) are frosted: a translucent slab of their own colour (≈90% via `color-mix`) + blur + the light rim, so they keep CTA punch while reading as glass. Non-tinted surfaces (nav pills, ghost button) are clear: a near-transparent paper tint + blur + the dark rim.

**No backdrop refraction.** An earlier version layered an SVG `feTurbulence → feDisplacementMap` filter onto `backdrop-filter` for a liquid warp, but `url()` on `backdrop-filter` is non-interoperable — Blink renders it as visible waves, WebKit applies it so softly the glass looked transparent — and the per-engine workarounds (two filter strengths, a JS-set `html.webkit` class, a heavier WebKit frost) only piled on fragility. It was all removed in favour of **one deterministic recipe that renders identically in every engine**: a `blur(10px) saturate(1.7)` backdrop + a `16%` paper tint (raised to `50%` when `.scrolled`) + the bevel. If you ever reintroduce a refraction, do it as progressive enhancement and don't branch on the engine.

### Letter-scramble heading

The stats heading carries `.scramble`; when it reveals it resolves with a split-flap letter shuffle. `scrambleIn(el)` splits the text into per-word / per-char spans, freezes each glyph's slot width (so the shuffle can't reflow the line), and spins each letter through a case-matched reel that locks left-to-right with a blur/fade focus-in (`.ch.shuf`), then restores the plain text node so kerning is pixel-perfect once settled. It fires from the `io` reveal observer on the transition into view (a `wasIn` check), guarded by a `_scrambling` flag and a `_gen` token so it plays once per entry and re-triggers on remix. The heading's reveal is fade-only (`.reveal.scramble { transform: none }`) so the moving box doesn't re-trip the observer mid-shuffle.

### Conventions

- Behavior is wired through data attributes and marker classes: `data-hover` (cursor ring grow), `data-fx` (press effect), `data-speed` (parallax layer), `data-count` / `data-suffix` (counters), plus `.magnetic`, `.tilt`, `.reveal`, `.stagger`.
- The page hides the OS cursor (`cursor: none`) and draws its own — new interactive elements need `data-hover`; `@media (hover: none)` restores native behavior on touch.
- Comments in this file explain *why* (clip-path tradeoffs, easing-velocity continuity, observer hysteresis, inline-style-vs-class precedence), not what. Keep that bar when editing — many rules look removable but are load-bearing, and the adjacent comment usually says so.
- Single-file and dependency-free is the project's identity (see the footer fineprint and the stats section) — don't introduce external assets, frameworks, or a build step.
