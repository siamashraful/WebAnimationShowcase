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

**1. CSS custom properties are the theme/physics surface.** `--accent` / `--accent-2` / `--accent-3`, `--paper`, `--radius`, `--spring`, `--ease-out` on `:root` parameterize nearly every rule. New UI must use these variables (not hard-coded colors or curves) or it won't respond to the remix system.

**2. One `requestAnimationFrame` loop (`frame()`) drives all continuous motion** — cursor trail, magnetic-button springs, scroll progress, frosted-nav toggle, the marquee belt, typographic parallax (position-driven), velocity skew lines (speed-driven), and the magnet-card letter orbit. Add new continuous effects inside this loop rather than starting another one; it computes a clamped frame-time delta (`dtf`) so motion stays time-accurate across tab switches. The loop has a strict perf contract: it never reads layout (geometry is cached by `measure()`, refreshed on resize — never call `getBoundingClientRect`/`scrollWidth`/`scrollHeight` per frame), it skips style writes when the rounded value hasn't changed (the `w*`-prefixed caches), and it skips sections flagged off-screen in the `onScreen` map by an IntersectionObserver, which also toggles `.offstage` to pause infinite CSS keyframes (hero blobs, scroll hint). The hidden remix tooltip's holo loops are paused via `.remix-tip:not(.show)`. New per-frame work must follow the same rules. Discrete entrances use IntersectionObserver instead: `.reveal` / `.stagger` (re-triggerable, with hysteresis and a -110px bottom rootMargin — the comment block above `io` explains why both numbers are what they are) and the animated counters.

**3. `remix()` re-rolls every animation parameter and replays everything**: 21 palettes, paper tints, corner radius, spring/ease curves, the live `cfg` physics object (`tilt`, `magnet`, `skewMax`, `skewVel`), blob sizes, marquee speed/direction, reveal variants, stagger delays, hero headline timing, counters. Any new tunable you add should be re-rolled here — the feature's promise is that one click changes the whole page. The button click choreographs: pill-to-die morph → confetti → radial wipe in the incoming palette → `remix(pal)` runs while the page is covered → fade → morph back.

### Card press effects (`fx-*`)

Clicking a "What's inside" card plays a concept-matched whole-card performance. The contract:

- JS adds `fx-<name>` (from the icon's `data-fx`) to the `.card`; CSS keyframes choreograph the card, icon, `h3`, and `p` together.
- All press-effect keyframes are named `fx-*` — the `animationend` handler removes the class only once no `fx-*` animation is still running, so a misnamed keyframe breaks cleanup.
- `fx-cooldown` disarms hover tilt until the pointer leaves and re-enters; tilt also pauses while any `fx-` class is on the card.
- The magnet card is the exception: a per-frame follow mode (`magState` in the rAF loop), not a keyframe animation. Its title is split into `.mag-w` / `.mag-l` spans at startup so letters can detach without reflowing the text.

### Conventions

- Behavior is wired through data attributes and marker classes: `data-hover` (cursor ring grow), `data-fx` (press effect), `data-speed` (parallax layer), `data-dir` (skew direction), `data-count` / `data-suffix` (counters), plus `.magnetic`, `.tilt`, `.reveal`, `.stagger`.
- The page hides the OS cursor (`cursor: none`) and draws its own — new interactive elements need `data-hover`; `@media (hover: none)` restores native behavior on touch.
- Comments in this file explain *why* (clip-path tradeoffs, easing-velocity continuity, observer hysteresis, inline-style-vs-class precedence), not what. Keep that bar when editing — many rules look removable but are load-bearing, and the adjacent comment usually says so.
- Single-file and dependency-free is the project's identity (see the footer fineprint and the stats section) — don't introduce external assets, frameworks, or a build step.
