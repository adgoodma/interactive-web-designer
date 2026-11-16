# Transitions Playbook

A working catalogue of the web transitions used in award-winning sites and design-showcase
reels — with what each looks like, how it's built, the library to reach for, and its
reduced-motion fallback. Pair with the main skill's motion and delight guidance. Every
transition below must ship a `prefers-reduced-motion` alternative (usually an instant cut or a
short fade) and animate `transform`/`opacity`/`clip-path` for 60fps.

---

## How to reverse-engineer any transition you saw

When replicating a transition from a video, screenshot, or live site, decode it in five passes —
then rebuild it with the matching tool below:

1. **Trigger** — what starts it? Route/page change, scroll position, click/tap, hover, or load.
2. **Elements** — what actually moves? The whole page, one section, an image, text, a UI element,
   or a full-screen overlay panel.
3. **Properties** — what changes? Position (`translate`), scale, rotation, opacity, `clip-path`,
   `mask`, colour, blur, or a WebGL displacement.
4. **Timing & easing** — how long (usually 0.3–1.2s), and what curve? Snappy `power3/expo.out`,
   physical spring/overshoot, or linear (scroll-scrubbed). Easing carries most of the "feel".
5. **Choreography** — one move or many? Stagger, overlap, and the order of enter/exit (does the
   old element leave before the new arrives, or do they cross?).

Then match it to a family below. Most "wow" transitions are a **timeline of simple moves**
(clip + translate + stagger), not one exotic effect.

---

## A. Page & route transitions (between pages/views)

| Transition | Looks like | Build it with |
|-----------|-----------|---------------|
| Crossfade | Old page fades out as new fades in | View Transitions API; Barba.js / Swup + GSAP |
| Cover & reveal | A colour/image panel slides up to cover, then slides away to reveal the new page | GSAP timeline; Barba enter/leave hooks |
| Curtain / columns | Vertical strips wipe across the screen in sequence | GSAP stagger on column divs; clip-path |
| Clip-path wipe | Circle/iris/rectangle grows to reveal the next page | `clip-path` + GSAP; View Transitions |
| Shared element (hero) | A thumbnail expands into the next page's hero image | GSAP **Flip**; View Transitions `view-transition-name`; Framer Motion `layoutId` |
| Liquid / gooey | An organic SVG blob morphs between pages | SVG `feGaussianBlur`+`feColorMatrix` filter + path morph (GSAP) |

**Cross-document** transitions (multi-page sites) work natively with the **View Transitions API**
(`@view-transition { navigation: auto }`) in supported browsers; use **Barba.js** or **Swup** to
intercept navigation and run a GSAP timeline everywhere else. Always keep a preloader/overlay so
the incoming page's assets are ready before the reveal.

## B. Section & scroll transitions (within one page)

| Transition | Looks like | Build it with |
|-----------|-----------|---------------|
| Reveal on scroll | Elements fade/slide/mask up as they enter the viewport | GSAP ScrollTrigger; CSS `animation-timeline: view()`; IntersectionObserver |
| Pinned scene change | A section sticks while its contents swap/animate on scroll | ScrollTrigger `pin: true` + scrub |
| Horizontal panels | Vertical scroll drives sideways movement through panels | ScrollTrigger + `translateX` |
| Scroll-snap swap | Full-screen sections snap one at a time | CSS `scroll-snap-type` + transitions |
| Parallax layers | Layers move at different speeds, implying depth | GSAP/Lenis; CSS scroll-driven animations |
| Scrubbed media | An image sequence or video scrubs frame-by-frame with scroll | ScrollTrigger + `<video>` `currentTime` / canvas frames |

Pair scroll transitions with **Lenis** for smooth, programmable scrolling so scrubbed values feel
controllable rather than jumpy.

## C. Image & media transitions

| Transition | Looks like | Build it with |
|-----------|-----------|---------------|
| Crossfade / dissolve | One image melts into another | opacity on stacked images; GSAP |
| Clip / shutter reveal | Image revealed by a wipe, iris, or venetian-blind shutter | `clip-path` + GSAP stagger |
| Mask sweep | A gradient mask sweeps to reveal the image | `mask-image` + `mask-position` (GSAP) |
| Displacement / distortion | Ripple, RGB-shift, or liquid warp on hover or swap | WebGL shader — curtains.js, Three.js, OGL |
| Tile shatter | Image splits into a grid of tiles that flip/fade in sequence | CSS grid of clipped clones + GSAP stagger |
| Ken Burns | Slow zoom/pan that keeps stills alive | CSS `transform: scale()` + long ease |

## D. Text & typography transitions

| Transition | Looks like | Build it with |
|-----------|-----------|---------------|
| Line/word/char stagger | Text reveals piece by piece (fade-up, blur-in) | SplitType / Splitting.js + GSAP stagger |
| Mask slide-up | Lines slide up from behind an invisible mask edge | wrap lines in `overflow:hidden` + `translateY` |
| Scramble / decode | Characters shuffle then resolve into the word | GSAP ScrambleText plugin |
| Typewriter | Characters type in one at a time | GSAP TextPlugin; JS interval |
| Variable-font morph | Weight/width animates between states | `font-variation-settings` + transition |

## E. Element & UI micro-transitions

| Transition | Looks like | Build it with |
|-----------|-----------|---------------|
| State morph (FLIP) | An element smoothly animates between two layouts/sizes | GSAP Flip; Framer Motion `layout` |
| Card → detail expand | A card grows into a full detail view | Flip / shared-element / View Transitions |
| Menu open | Full-screen overlay with staggered links, clip or slide reveal | GSAP timeline + stagger; clip-path |
| Accordion / tab swap | Panels crossfade and resize | height/`grid-template-rows` + opacity |
| Hover states | Underline draw, background wipe, magnetic pull, icon morph | CSS transitions; GSAP for magnetic follow |

## F. Full-screen / creative transitions

| Transition | Looks like | Build it with |
|-----------|-----------|---------------|
| Curtain wipe | Solid colour panel(s) sweep across between states | GSAP + full-bleed divs |
| Split-screen part | Two halves slide apart to reveal content | GSAP on two panels |
| Iris from cursor | A circle expands from the click point | `clip-path: circle()` at pointer coords + GSAP |
| Glitch / pixelate / dither | Digital distortion between states | CSS/SVG filters; canvas/WebGL shader |
| 3D flip / cube / page-peel | Panels rotate in 3D or peel like paper | CSS 3D transforms (`perspective`, `rotateY`); Three.js |

---

## Motion craft (applies to every transition)

- **Easing is the feel.** Snappy exits (`expo.out`, `power3.out`), gentle settles (custom
  cubic-bezier), or overshoot/spring for playful UIs. Avoid default `ease` for hero moments.
- **Duration.** Micro-interactions 0.15–0.3s; section reveals 0.4–0.8s; page transitions
  0.6–1.2s. Slower reads as premium only if the easing is right.
- **Stagger & overlap.** Offset grouped elements (0.03–0.08s each) and overlap enter/exit on a
  timeline so the transition feels composed, not sequential.
- **Orchestrate with a timeline.** Use one GSAP timeline (or an `AnimatePresence`/View Transition)
  per transition so enter and exit are coordinated and reversible.
- **Performance.** Animate only `transform`, `opacity`, `clip-path`, and `filter`; add
  `will-change` sparingly and remove it after; debounce scroll/resize; lazy-load heavy media.
- **Accessibility (non-negotiable).** Wrap every non-essential transition in
  `@media (prefers-reduced-motion: reduce)` with an instant cut or short fade; keep focus order
  correct through route/menu transitions; never trap keyboard users behind an animation.

## Library cheat-sheet

- **GSAP** (+ ScrollTrigger, Flip, Observer) — the default engine for timelines, scroll, and FLIP.
- **Lenis** — smooth, programmable scrolling to pair with scroll transitions.
- **View Transitions API** — native same- and cross-document page/shared-element transitions.
- **Barba.js / Swup** — intercept navigation to run custom page transitions on multi-page sites.
- **Framer Motion** — declarative `layout`/`layoutId` + `AnimatePresence` in React.
- **curtains.js / Three.js / OGL + GLSL** — WebGL image displacement and 3D transitions.
- **SplitType / Splitting.js** — split text for staggered line/word/char reveals.
- **CSS** — `clip-path`, `mask-image`, scroll-driven animations (`animation-timeline`), 3D
  transforms, and `scroll-snap` cover a surprising amount with zero JS.
