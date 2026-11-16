# Liquid Mode — Liquid Glass & Glassmorphism Design System

The detailed system behind the skill's **Liquid mode**. Use it whenever the user's request contains
the word **"Liquid"** (any case — "Liquid", "make it liquid", "liquid glass", "Liquid dashboard") or
asks for **glassmorphism**. Apply it to whatever they asked for: a landing page, dashboard, app UI,
card, button set, toolbar, widget grid, and so on. Everything else in the main skill still applies
(Phase 0 alignment, design-system-first, states-complete, Vision QA, delivery via the html skill).

---

## 0. Inspiration sources (cite, don't copy)

These were supplied by the user as the reference register. Recreate the *look and behaviour*
originally — never lift screenshots, icons or proprietary assets.

1. **[liquidglassdesign.com](https://liquidglassdesign.com/)** — a gallery of 170+ liquid glass &
   glassmorphism examples, each paired with an AI style prompt to recreate the look. Its framing:
   Liquid Glass is the translucent interface material Apple introduced at WWDC 2025 and shipped
   across iOS 26, iPadOS 26 and macOS 26 Tahoe. Rather than simply blurring what is behind it, a
   Liquid Glass surface *refracts* content like a real lens, picks up *specular highlights* as the
   device moves, and *adapts its tint* to what passes underneath. Glassmorphism (the 2020 trend) is
   a static recipe — background blur, hairline border, soft shadow; Liquid Glass is simulated per
   frame and reacts. Gallery subjects: liquid glass buttons, app icons, UI cards, colour pickers,
   smart-home apps, a voice recorder, visionOS 3D-motion UI, iOS 26 buttons, gradient art, text
   effects. (Section 9's prompt template mirrors its "style prompt per example" approach.)
2. **[Glassmorphism Dashboard UI Kit (Figma Community)](https://www.figma.com/community/file/1514405085901665002/glassmorphism-dashboard-ui-kit)**
   by Mike ([miketopus.com](https://miketopus.com)), licensed **CC BY 4.0** — a macOS-style
   dashboard inspired by Apple's Liquid Glass: clean lines, soft blurs, a modern glassmorphism
   aesthetic and sleek, minimalist interfaces. *If any asset from the kit is reused, credit
   "Glassmorphism Dashboard UI Kit by Mike (miketopus.com), CC BY 4.0" in the deliverable.*
3. **[Dribbble — #liquid-glass](https://dribbble.com/tags/liquid-glass)** — 800+ shots. Recurring
   patterns: iOS home UI with liquid-glass cards, a Liquid Glass Finder (macOS window chrome),
   smart-home control panels, AI SaaS landing pages, DeFi/crypto landing pages and mobile apps, AI
   nutrition-tracker dashboards, fitness/running tracker apps, UI component sheets, an EV-charger
   app, a photo-edit app, and AI-agent visual design.

"Liquid Glass" is Apple's name for its material: describe the deliverable as "a liquid-glass
style", not as an Apple product, and never ship Apple's icons, SF Symbols or wallpapers.

---

## 1. Pick the tier (per request)

| Tier | What it is | Choose it when |
|------|-----------|----------------|
| **Liquid Glass** *(default)* | Blur **+ real refraction** (SVG displacement), **specular highlight** that follows pointer/tilt, **adaptive tint**, springy liquid motion | Hero moments, landing pages, toolbars, button sets, app chrome, widgets, a handful of feature cards |
| **Glassmorphism** *(lighter fallback)* | Static recipe: backdrop blur + saturate, hairline gradient border, inner + soft drop shadow. No displacement, no per-frame work | Dense dashboards (many tiles/charts/tables), long scrolling lists, low-end/mobile performance budgets, or when the user says "subtle" / "performance matters" |

Mixed is normal: a **Liquid** shell (nav, toolbar, dock, primary CTA, hero card) over
**Glassmorphism** content tiles. State the tier choice in the design direction.

---

## 2. Design tokens

```css
:root {
  /* Glass surface */
  --lg-fill:          rgba(255,255,255,.18);   /* light: .12–.25 */
  --lg-fill-strong:   rgba(255,255,255,.28);   /* text-heavy panels, popovers */
  --lg-fill-solid:    rgba(246,247,250,.94);   /* reduced-transparency / no-backdrop fallback */
  --lg-blur:          24px;                    /* 12–40px */
  --lg-blur-light:    14px;                    /* glassmorphism tier / small chips */
  --lg-sat:           180%;                    /* 160–200% */
  --lg-border:        rgba(255,255,255,.42);   /* hairline .35–.5 */
  --lg-edge: linear-gradient(135deg,
               rgba(255,255,255,.85) 0%, rgba(255,255,255,.18) 28%,
               rgba(255,255,255,0) 55%, rgba(255,255,255,.35) 100%); /* top-left highlight edge */
  --lg-inner:  inset 0 1px 0 rgba(255,255,255,.6), inset 0 -1px 0 rgba(0,0,0,.1);
  --lg-shadow: 0 10px 40px -8px rgba(16,20,40,.25), 0 2px 8px rgba(16,20,40,.08);
  --lg-tint:   transparent;                    /* set by adaptive tint (section 5) */

  /* Shape — large continuous radii */
  --lg-r-xs: 10px; --lg-r-sm: 16px; --lg-r: 24px; --lg-r-lg: 32px; --lg-r-pill: 999px;

  /* Type */
  --lg-font: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text",
             "Segoe UI Variable", "Segoe UI", Inter, system-ui, sans-serif;
  --lg-text:   #0b0d12;
  --lg-text-2: rgba(11,13,18,.66);
  --lg-fs-xs: .75rem; --lg-fs-sm: .875rem; --lg-fs: 1rem; --lg-fs-lg: 1.25rem;
  --lg-fs-xl: clamp(1.75rem, 1.2rem + 2vw, 2.5rem); --lg-fs-hero: clamp(2.75rem, 1.5rem + 5vw, 5.5rem);

  /* Spacing scale (4px base) */
  --sp-1: 4px; --sp-2: 8px; --sp-3: 12px; --sp-4: 16px; --sp-5: 24px;
  --sp-6: 32px; --sp-7: 48px; --sp-8: 64px; --sp-9: 96px;

  /* Motion */
  --lg-spring: cubic-bezier(.34,1.56,.64,1);   /* jelly overshoot */
  --lg-ease:   cubic-bezier(.2,.8,.2,1);       /* calm settle */
  --lg-dur: 420ms; --lg-dur-fast: 220ms;

  /* Pointer specular (JS updates) */
  --mx: 30%; --my: 0%;
}

@media (prefers-color-scheme: dark) {
  :root {
    --lg-fill:        rgba(20,20,30,.35);     /* dark: .25–.45 */
    --lg-fill-strong: rgba(20,20,30,.5);
    --lg-fill-solid:  rgba(24,25,32,.95);
    --lg-border:      rgba(255,255,255,.35);
    --lg-edge: linear-gradient(135deg,
                 rgba(255,255,255,.55) 0%, rgba(255,255,255,.1) 30%,
                 rgba(255,255,255,0) 55%, rgba(255,255,255,.2) 100%);
    --lg-inner:  inset 0 1px 0 rgba(255,255,255,.25), inset 0 -1px 0 rgba(0,0,0,.3);
    --lg-shadow: 0 12px 48px -8px rgba(0,0,0,.55), 0 2px 8px rgba(0,0,0,.3);
    --lg-text:   #f5f6fa;
    --lg-text-2: rgba(245,246,250,.7);
  }
}
```

Font note: SF Pro is only *referenced* through the local system stack (`-apple-system`) — never
embed it as a web font (Apple's licence restricts it to Apple platforms). Windows gets
Segoe UI Variable; everything else Inter / system-ui.

### Base glass surface (both tiers)

```css
.glass {
  position: relative; isolation: isolate;
  color: var(--lg-text); font-family: var(--lg-font);
  background: linear-gradient(var(--lg-tint), var(--lg-tint)), var(--lg-fill);
  -webkit-backdrop-filter: blur(var(--lg-blur)) saturate(var(--lg-sat));
          backdrop-filter: blur(var(--lg-blur)) saturate(var(--lg-sat));
  border: 1px solid var(--lg-border);
  border-radius: var(--lg-r);
  box-shadow: var(--lg-inner), var(--lg-shadow);
}
/* Gradient top-left highlight edge (sits on the hairline) */
.glass::before {
  content: ""; position: absolute; inset: -1px; border-radius: inherit; padding: 1px;
  background: var(--lg-edge); pointer-events: none;
  -webkit-mask: linear-gradient(#000 0 0) content-box, linear-gradient(#000 0 0);
  -webkit-mask-composite: xor; mask-composite: exclude;
}
/* Specular highlight — Liquid tier only (follows --mx/--my) */
.glass--liquid::after {
  content: ""; position: absolute; inset: 0; border-radius: inherit; pointer-events: none;
  background: radial-gradient(120% 80% at var(--mx) var(--my),
              rgba(255,255,255,.38), rgba(255,255,255,.08) 35%, transparent 60%);
  mix-blend-mode: soft-light; transition: opacity var(--lg-dur) var(--lg-ease);
}
.glass--light { -webkit-backdrop-filter: blur(var(--lg-blur-light)) saturate(160%);
                        backdrop-filter: blur(var(--lg-blur-light)) saturate(160%); }

/* No backdrop-filter at all → near-solid surface, never unreadable */
@supports not ((backdrop-filter: blur(1px)) or (-webkit-backdrop-filter: blur(1px))) {
  .glass { background: var(--lg-fill-solid); }
}
```

**Backdrop-root gotcha:** an ancestor with `filter`, `opacity < 1`, `mask`, `mix-blend-mode` or its
own `backdrop-filter` becomes a *backdrop root* — glass inside it only sees that ancestor, not the
page background. Keep glass surfaces as siblings over the background, not nested inside other
filtered layers (this is also why you cap stacked blurred layers at 3).

---

## 3. Backgrounds — glass needs something to refract

**Never glass on flat white (or flat anything).** Pick one:

- **Mesh / aurora gradient** — 3–5 overlapping radial gradients in a vivid palette.
- **Blurred colour blobs / animated orbs** — 2–4 large circles, heavily blurred, slowly drifting.
- **Photography** — rich, mid-contrast imagery (landscape, product, abstract 3D render).
- **Gradient art / 3D render** — the "wallpaper" register of iOS/macOS home screens.

```html
<div class="lg-bg" aria-hidden="true">
  <span class="orb o1"></span><span class="orb o2"></span><span class="orb o3"></span>
</div>
```
```css
.lg-bg { position: fixed; inset: 0; z-index: -1; overflow: hidden;
  background:
    radial-gradient(60% 50% at 15% 20%, #7aa2ff 0%, transparent 60%),
    radial-gradient(50% 45% at 85% 15%, #ff8ad8 0%, transparent 60%),
    radial-gradient(55% 55% at 70% 85%, #5ef0d0 0%, transparent 60%),
    linear-gradient(160deg, #1b1f3b, #2a1450 55%, #0f2a3d); }
.orb { position: absolute; width: 42vmax; aspect-ratio: 1; border-radius: 50%;
  filter: blur(60px); opacity: .75; will-change: transform;
  animation: drift 28s var(--lg-ease) infinite alternate; }
.o1 { background: #6c8cff; top: -10%; left: -8%; }
.o2 { background: #ff6fb5; top: 30%; right: -12%; animation-duration: 34s; }
.o3 { background: #36e2b4; bottom: -15%; left: 25%; animation-duration: 40s; }
@keyframes drift { to { transform: translate3d(8vmax, -6vmax, 0) scale(1.15); } }
@media (prefers-reduced-motion: reduce) { .orb { animation: none; } }
```

Light-mode palette idea: pale sky → peach → lilac gradients with a saturated accent orb; dark
mode: deep indigo/plum base with neon-ish orbs. Keep one accent colour for CTAs.

---

## 4. Real refraction (Liquid tier)

Blur alone is glassmorphism. Liquid Glass *bends* what is behind it — strongest at the rim
(edge-lensing), almost clear in the centre.

**Support reality:** `backdrop-filter: url(#svg-filter)` renders only in **Chromium** (Chrome,
Edge, Opera, Arc). Safari and Firefox ignore or drop it, and `@supports` cannot be trusted to
detect it (some engines report support but render nothing). So: **CSS gives everyone the blur
(section 2); JS upgrades Chromium to refraction.**

### 4a. Edge-lensing displacement map (per element)

```html
<svg width="0" height="0" style="position:absolute" aria-hidden="true" focusable="false">
  <defs id="lg-filters"></defs>
</svg>
```
```js
// Liquid Glass refraction — Chromium only, graceful no-op elsewhere.
const LG_CHROMIUM = !!navigator.userAgentData?.brands?.some(b => /Chromium/.test(b.brand));
const LG_REDUCED  = matchMedia('(prefers-reduced-transparency: reduce)').matches;

function lensMap(w, h, r, band) {               // radial edge-lens displacement map
  const c = document.createElement('canvas'); c.width = w; c.height = h;
  const ctx = c.getContext('2d'), img = ctx.createImageData(w, h), d = img.data;
  for (let y = 0; y < h; y++) for (let x = 0; x < w; x++) {
    const px = x + .5 - w / 2, py = y + .5 - h / 2;
    const qx = Math.abs(px) - (w / 2 - r), qy = Math.abs(py) - (h / 2 - r);
    const outside = Math.hypot(Math.max(qx, 0), Math.max(qy, 0));
    const sdf = outside + Math.min(Math.max(qx, qy), 0) - r;   // <0 inside rounded rect
    let nx = 0, ny = 0;
    if (qx > 0 && qy > 0) { nx = qx / outside; ny = qy / outside; }   // corner normal
    else if (qx > qy) nx = 1; else ny = 1;
    nx *= Math.sign(px); ny *= Math.sign(py);
    const depth = -sdf;                                              // px from the rim
    const k = depth > 0 && depth < band ? (1 - depth / band) ** 2 : 0;
    const i = (y * w + x) * 4;
    d[i]     = 128 - nx * k * 127;   // R → x displacement (sample inward)
    d[i + 1] = 128 - ny * k * 127;   // G → y displacement
    d[i + 2] = 128; d[i + 3] = 255;
  }
  ctx.putImageData(img, 0, 0);
  return c.toDataURL();
}

function liquify(el, { scale = 70, band = 28, blur = 3, chroma = false } = {}) {
  if (!LG_CHROMIUM || LG_REDUCED) return;            // others keep the CSS blur tier
  const { width: w, height: h } = el.getBoundingClientRect();
  const W = Math.round(w), H = Math.round(h);
  const r = Math.min(parseFloat(getComputedStyle(el).borderTopLeftRadius) || 0, W / 2, H / 2);
  const id = el.dataset.lgId ||= 'lg-' + Math.random().toString(36).slice(2, 8);
  const map = lensMap(W, H, r, band);
  const disp = s => `<feDisplacementMap in="SourceGraphic" in2="map" scale="${s}"
                       xChannelSelector="R" yChannelSelector="G"`;
  const body = !chroma
    ? `${disp(scale)}/>`
    : `${disp(scale)} result="dR"/>
       <feColorMatrix in="dR" type="matrix" values="1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0" result="r"/>
       ${disp(scale * .94)} result="dG"/>
       <feColorMatrix in="dG" type="matrix" values="0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 1 0" result="g"/>
       ${disp(scale * .88)} result="dB"/>
       <feColorMatrix in="dB" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 1 0" result="b"/>
       <feBlend in="r" in2="g" mode="screen" result="rg"/><feBlend in="rg" in2="b" mode="screen"/>`;
  document.getElementById(id)?.remove();
  document.getElementById('lg-filters').insertAdjacentHTML('beforeend',
    `<filter id="${id}" x="0" y="0" width="${W}" height="${H}" filterUnits="userSpaceOnUse"
             color-interpolation-filters="sRGB">
       <feImage href="${map}" x="0" y="0" width="${W}" height="${H}" preserveAspectRatio="none" result="map"/>
       ${body}
     </filter>`);
  el.style.backdropFilter = `url(#${id}) blur(${blur}px) saturate(var(--lg-sat))`;
}

// Apply + keep in sync with size changes (debounced by the observer itself)
const ro = new ResizeObserver(es => es.forEach(e => liquify(e.target, e.target._lgOpts)));
document.querySelectorAll('[data-liquid]').forEach(el => {
  el._lgOpts = { chroma: el.dataset.liquid === 'chroma' };
  ro.observe(el);
});
```

Tuning: `scale` 40–90 (strength), `band` 16–40px (rim width; ~radius for pills), `blur` 0–6px
in the refracting tier (refraction does the heavy lifting). Flip the sign in `lensMap` for an
outward "bulge". The optional **chromatic fringe** (`data-liquid="chroma"`) splits R/G/B with
slightly different scales — use on one hero object, not on every chip.

### 4b. Liquid wobble (optional, turbulence)

For a molten, "poured" surface (hero blob, loading state) rather than a clean lens:

```html
<filter id="lg-wobble" color-interpolation-filters="sRGB">
  <feTurbulence type="fractalNoise" baseFrequency="0.008 0.012" numOctaves="2" seed="7" result="n"/>
  <feDisplacementMap in="SourceGraphic" in2="n" scale="18" xChannelSelector="R" yChannelSelector="G"/>
</filter>
```
Animating `baseFrequency`/`seed` repaints every frame — keep it to one small element, pause it
off-screen (IntersectionObserver) and disable it under reduced motion.

---

## 5. Specular highlight, tilt, parallax & adaptive tint (reactivity)

```js
// Pointer-tracked specular (rAF-throttled). Uses --mx/--my from section 2.
const RM = matchMedia('(prefers-reduced-motion: reduce)').matches;
document.querySelectorAll('.glass--liquid').forEach(el => {
  let raf = 0;
  el.addEventListener('pointermove', e => {
    cancelAnimationFrame(raf);
    raf = requestAnimationFrame(() => {
      const r = el.getBoundingClientRect();
      el.style.setProperty('--mx', ((e.clientX - r.left) / r.width * 100).toFixed(1) + '%');
      el.style.setProperty('--my', ((e.clientY - r.top) / r.height * 100).toFixed(1) + '%');
    });
  });
  el.addEventListener('pointerleave', () => { el.style.removeProperty('--mx'); el.style.removeProperty('--my'); });
});

// Mobile tilt → global highlight + subtle parallax (iOS needs a user-gesture permission)
async function enableTilt() {
  if (RM) return;
  if (typeof DeviceOrientationEvent?.requestPermission === 'function') {
    if (await DeviceOrientationEvent.requestPermission() !== 'granted') return;
  }
  addEventListener('deviceorientation', ({ beta = 0, gamma = 0 }) => {
    const gx = Math.max(-30, Math.min(30, gamma)) / 30, gy = Math.max(-30, Math.min(30, beta - 45)) / 30;
    const root = document.documentElement.style;
    root.setProperty('--mx', (50 + gx * 40) + '%');
    root.setProperty('--my', (50 + gy * 40) + '%');
    root.setProperty('--px', (gx * 6) + 'px');     // parallax offset for .lg-parallax
    root.setProperty('--py', (gy * 6) + 'px');
  }, { passive: true });
}
// e.g. call enableTilt() from the first tap on a "✨ Motion" toggle — never auto-prompt.
```
```css
.lg-parallax { transform: translate3d(var(--px,0), var(--py,0), 0); transition: transform .2s linear; }
.lg-bg .orb  { translate: calc(var(--px,0) * -3) calc(var(--py,0) * -3); } /* background moves opposite */
```

**Adaptive tint** — the glass reads what passes underneath and flips text/tint light ↔ dark:

```js
// (a) Sections over known backgrounds: tag them, let the floating bar follow.
// <section data-tone="light">…</section>  <nav class="glass glass--liquid lg-nav">…</nav>
const nav = document.querySelector('.lg-nav');
const io = new IntersectionObserver(es => es.forEach(e => {
  if (e.isIntersecting) nav.dataset.tone = e.target.dataset.tone;   // section under the top 10% band
}), { rootMargin: '0px 0px -90% 0px' });
document.querySelectorAll('section[data-tone]').forEach(s => io.observe(s));

// (b) Photo backgrounds: sample luminance under an element (same-origin / CORS-enabled images).
function toneUnder(el, img) {
  const c = Object.assign(document.createElement('canvas'), { width: 24, height: 24 });
  const ctx = c.getContext('2d', { willReadFrequently: true });
  const r = el.getBoundingClientRect(), ir = img.getBoundingClientRect();
  const sx = (r.left - ir.left) / ir.width * img.naturalWidth, sy = (r.top - ir.top) / ir.height * img.naturalHeight;
  ctx.drawImage(img, sx, sy, r.width / ir.width * img.naturalWidth, r.height / ir.height * img.naturalHeight, 0, 0, 24, 24);
  const d = ctx.getImageData(0, 0, 24, 24).data; let L = 0;
  for (let i = 0; i < d.length; i += 4) {
    const [R, G, B] = [d[i], d[i+1], d[i+2]].map(v => (v /= 255) <= .03928 ? v / 12.92 : ((v + .055) / 1.055) ** 2.4);
    L += .2126 * R + .7152 * G + .0722 * B;
  }
  return L / (d.length / 4) > .4 ? 'light' : 'dark';
}
```
```css
[data-tone="light"] { --lg-tint: rgba(255,255,255,.12); --lg-text: #0b0d12; --lg-text-2: rgba(11,13,18,.66); }
[data-tone="dark"]  { --lg-tint: rgba(10,12,20,.22);   --lg-text: #f5f6fa; --lg-text-2: rgba(245,246,250,.72); }
.lg-nav { transition: background-color var(--lg-dur) var(--lg-ease), color var(--lg-dur) var(--lg-ease); }
```

---

## 6. Motion — liquid, springy, physical

| Behaviour | Recipe |
|-----------|--------|
| **Spring / jelly easing** | `cubic-bezier(.34,1.56,.64,1)` (`--lg-spring`) for enter/press-release; `--lg-ease` for colour/opacity |
| **Press squish** | `:active` → `scale(.96)`; on release play a jelly keyframe (stretch then settle) |
| **Gooey merge** | Blob layer under adjacent buttons with `feGaussianBlur` + `feColorMatrix` so shapes melt together on hover/expand |
| **Flowing tab indicator** | A floating capsule that stretches toward the target, then snaps (spring) — never a teleport |
| **Sheet from trigger** | Modals/sheets scale out of the button that opened them (`transform-origin` = trigger centre) and collapse back into it |

```css
.lg-press { transition: transform var(--lg-dur-fast) var(--lg-ease); }
.lg-press:active { transform: scale(.96); }
.lg-press.is-released { animation: lg-jelly 520ms var(--lg-ease); }
@keyframes lg-jelly {
  0% { transform: scale(.96); } 35% { transform: scale(1.045, .965); }
  65% { transform: scale(.99, 1.015); } 100% { transform: scale(1); }
}
```
```js
document.addEventListener('pointerup', e => {
  const b = e.target.closest('.lg-press'); if (!b || RM) return;
  b.classList.remove('is-released'); void b.offsetWidth; b.classList.add('is-released');
});
```

**Gooey blob merge** — put the filter on a *decorative blob layer*, never on text or on a parent
of glass (a filtered parent becomes a backdrop root and kills refraction):

```html
<svg width="0" height="0" style="position:absolute" aria-hidden="true">
  <filter id="lg-goo">
    <feGaussianBlur in="SourceGraphic" stdDeviation="10" result="b"/>
    <feColorMatrix in="b" type="matrix" values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 20 -9" result="g"/>
    <feComposite in="SourceGraphic" in2="g" operator="atop"/>
  </filter>
</svg>
<div class="goo-group">
  <div class="goo-blobs" aria-hidden="true"><i></i><i></i><i></i></div>
  <div class="goo-buttons"><button class="lg-btn">Play</button><button class="lg-btn">Next</button><button class="lg-btn">Share</button></div>
</div>
```
```css
.goo-group { position: relative; display: inline-grid; }
.goo-blobs, .goo-buttons { grid-area: 1 / 1; display: flex; gap: 6px; }
.goo-blobs { filter: url(#lg-goo); }
.goo-blobs i { width: 88px; height: 44px; border-radius: 999px; background: rgba(255,255,255,.55);
  transition: transform var(--lg-dur) var(--lg-spring); }
.goo-group:hover .goo-blobs i:nth-child(1) { transform: translateX(10px); }
.goo-group:hover .goo-blobs i:nth-child(3) { transform: translateX(-10px); }
.goo-buttons .lg-btn { width: 88px; background: transparent; border-color: transparent; box-shadow: none; }
```

**Flowing tab-bar indicator** (WAAPI stretch → spring):

```js
function flowTo(bar, btn) {
  const pill = bar.querySelector('.lg-tab-pill'), p = bar.getBoundingClientRect(), r = btn.getBoundingClientRect();
  const from = pill.getBoundingClientRect(), x0 = from.left - p.left, x1 = r.left - p.left;
  const left = Math.min(x0, x1), span = Math.max(x0 + from.width, x1 + r.width) - left;
  bar.querySelectorAll('[role="tab"]').forEach(t => t.setAttribute('aria-selected', t === btn));
  const end = { transform: `translateX(${x1}px)`, width: r.width + 'px' };
  if (RM) return Object.assign(pill.style, end);
  pill.animate([
    { transform: `translateX(${x0}px)`, width: from.width + 'px' },
    { transform: `translateX(${left}px)`, width: span + 'px', offset: .45 },   // stretch across
    end
  ], { duration: 520, easing: 'cubic-bezier(.34,1.56,.64,1)' }).onfinish = () => Object.assign(pill.style, end);
}
```

**Sheet expanding from its trigger** (native `<dialog>`):

```js
function openSheet(trigger, dlg) {
  const t = trigger.getBoundingClientRect();
  dlg.showModal();
  const d = dlg.getBoundingClientRect();
  dlg.style.transformOrigin = `${t.left + t.width / 2 - d.left}px ${t.top + t.height / 2 - d.top}px`;
  if (!RM) dlg.animate([{ transform: 'scale(.15)', opacity: 0, borderRadius: '999px' },
                        { transform: 'scale(1)',   opacity: 1 }],
                       { duration: 480, easing: 'cubic-bezier(.34,1.56,.64,1)' });
  dlg.addEventListener('close', () => trigger.focus(), { once: true });
}
```

---

## 7. Component recipes

All components extend `.glass` (section 2). Add `.glass--liquid` + `data-liquid` for the Liquid
tier, `.glass--light` for the glassmorphism tier.

```css
/* Card */
.lg-card { padding: var(--sp-5); border-radius: var(--lg-r); }
.lg-card h3 { font: 600 var(--lg-fs-lg)/1.25 var(--lg-font); letter-spacing: -.01em; margin: 0 0 var(--sp-2); }
.lg-card p  { color: var(--lg-text-2); margin: 0; }

/* Pill buttons */
.lg-btn { appearance: none; cursor: pointer; display: inline-flex; align-items: center; gap: var(--sp-2);
  min-height: 44px; padding: 0 var(--sp-5); border-radius: var(--lg-r-pill);
  font: 600 var(--lg-fs)/1 var(--lg-font); }                                    /* + .glass .lg-press */
.lg-btn--primary { background: linear-gradient(180deg, rgba(255,255,255,.35), rgba(255,255,255,0)) , var(--accent, #3b6cff);
  color: #fff; border-color: rgba(255,255,255,.5);
  box-shadow: var(--lg-inner), 0 8px 24px -6px color-mix(in srgb, var(--accent, #3b6cff) 60%, transparent); }
.lg-btn--secondary { /* plain .glass fill */ }

/* Focus ring visible on any backdrop: dark core + light halo */
.glass:focus-visible, .lg-btn:focus-visible, .lg-field:focus-within {
  outline: 2px solid #0b0d12; outline-offset: 2px; box-shadow: 0 0 0 5px rgba(255,255,255,.9), var(--lg-shadow); }

/* Segmented control / tab bar with floating capsule */
.lg-tabs { position: relative; display: inline-flex; padding: 4px; border-radius: var(--lg-r-pill); }
.lg-tabs [role="tab"] { position: relative; z-index: 1; min-height: 36px; padding: 0 var(--sp-4); border: 0;
  background: none; border-radius: var(--lg-r-pill); font: 600 var(--lg-fs-sm) var(--lg-font); color: var(--lg-text-2); }
.lg-tabs [aria-selected="true"] { color: var(--lg-text); }
.lg-tab-pill { position: absolute; top: 4px; bottom: 4px; left: 0; border-radius: var(--lg-r-pill);
  background: rgba(255,255,255,.55); box-shadow: var(--lg-inner), 0 4px 14px rgba(0,0,0,.12); }

/* Floating nav / toolbar */
.lg-nav { position: fixed; top: var(--sp-4); left: 50%; translate: -50% 0; z-index: 50;
  display: flex; align-items: center; gap: var(--sp-2); padding: 6px; border-radius: var(--lg-r-pill); }
.lg-toolbar { display: flex; gap: 2px; padding: 4px; border-radius: var(--lg-r-sm); }
.lg-toolbar button { width: 36px; height: 36px; border: 0; border-radius: 10px; background: none; }
.lg-toolbar button:hover { background: rgba(255,255,255,.3); }

/* macOS Finder-style window + sidebar */
.lg-window { display: grid; grid-template-columns: 220px 1fr; min-height: 560px; border-radius: var(--lg-r-sm); overflow: hidden; }
.lg-window__chrome { grid-column: 1 / -1; display: flex; align-items: center; gap: 8px; height: 44px; padding: 0 14px; }
.lg-traffic span { width: 12px; height: 12px; border-radius: 50%; display: inline-block; margin-right: 6px; }
.lg-traffic span:nth-child(1){background:#ff5f57} .lg-traffic span:nth-child(2){background:#febc2e} .lg-traffic span:nth-child(3){background:#28c840}
.lg-sidebar { margin: 8px; padding: var(--sp-2); border-radius: 14px; }        /* inset floating glass sidebar */
.lg-sidebar h4 { font: 600 var(--lg-fs-xs) var(--lg-font); color: var(--lg-text-2); margin: var(--sp-3) var(--sp-2) var(--sp-1); }
.lg-sidebar a { display: flex; gap: var(--sp-2); align-items: center; padding: 6px var(--sp-2); border-radius: 8px; color: inherit; text-decoration: none; }
.lg-sidebar a[aria-current="page"] { background: rgba(255,255,255,.4); }

/* Modal / sheet */
dialog.lg-sheet { border: 1px solid var(--lg-border); padding: var(--sp-6); width: min(520px, 92vw); border-radius: var(--lg-r-lg); color: var(--lg-text); }
dialog.lg-sheet::backdrop { background: rgba(10,12,20,.25); backdrop-filter: blur(4px); }
@media (max-width: 600px) { dialog.lg-sheet { margin: auto 0 0; width: 100%; border-radius: var(--lg-r-lg) var(--lg-r-lg) 0 0; } }

/* Toggle */
.lg-toggle { appearance: none; width: 52px; height: 32px; border-radius: 999px; position: relative; cursor: pointer;
  background: rgba(120,120,128,.32); border: 1px solid var(--lg-border); transition: background var(--lg-dur-fast); }
.lg-toggle::after { content: ""; position: absolute; top: 2px; left: 2px; width: 26px; height: 26px; border-radius: 50%;
  background: #fff; box-shadow: 0 2px 6px rgba(0,0,0,.25); transition: transform var(--lg-dur) var(--lg-spring), width .2s; }
.lg-toggle:active::after { width: 32px; }                                         /* liquid stretch on press */
.lg-toggle:checked { background: var(--accent, #34c759); }
.lg-toggle:checked::after { transform: translateX(20px); }
.lg-toggle:checked:active::after { transform: translateX(14px); }

/* Slider */
.lg-range { appearance: none; width: 100%; height: 28px; background: transparent; --v: 50%; }
.lg-range::-webkit-slider-runnable-track { height: 6px; border-radius: 99px;
  background: linear-gradient(90deg, var(--accent, #3b6cff) var(--v), rgba(255,255,255,.35) var(--v)); }
.lg-range::-webkit-slider-thumb { appearance: none; width: 28px; height: 28px; margin-top: -11px; border-radius: 50%;
  background: rgba(255,255,255,.9); border: 1px solid var(--lg-border); box-shadow: 0 2px 10px rgba(0,0,0,.25);
  transition: transform var(--lg-dur-fast) var(--lg-spring); }
.lg-range:active::-webkit-slider-thumb { transform: scale(1.25, 1.1); }
.lg-range::-moz-range-track { height: 6px; border-radius: 99px; background: rgba(255,255,255,.35); }
.lg-range::-moz-range-progress { height: 6px; border-radius: 99px; background: var(--accent, #3b6cff); }
.lg-range::-moz-range-thumb { width: 28px; height: 28px; border-radius: 50%; background: #fff; border: 1px solid var(--lg-border); }
/* JS: range.addEventListener('input', e => e.target.style.setProperty('--v', (e.target.value - e.target.min) / (e.target.max - e.target.min) * 100 + '%')) */

/* Search field */
.lg-field { display: flex; align-items: center; gap: var(--sp-2); height: 40px; padding: 0 var(--sp-3); border-radius: var(--lg-r-pill); }
.lg-field input { flex: 1; border: 0; background: none; outline: none; font: var(--lg-fs) var(--lg-font); color: inherit; }
.lg-field input::placeholder { color: var(--lg-text-2); }

/* KPI tile */
.lg-kpi { padding: var(--sp-4) var(--sp-5); border-radius: var(--lg-r); display: grid; gap: var(--sp-1); }
.lg-kpi__label { font: 500 var(--lg-fs-sm) var(--lg-font); color: var(--lg-text-2); }
.lg-kpi__value { font: 700 var(--lg-fs-xl)/1.1 var(--lg-font); letter-spacing: -.02em; font-variant-numeric: tabular-nums; }
.lg-kpi__delta { font: 600 var(--lg-fs-xs) var(--lg-font); padding: 2px 8px; border-radius: 99px; justify-self: start;
  background: rgba(52,199,89,.2); color: #0f7a33; }                                /* check contrast on dark */
.lg-kpi__delta.is-down { background: rgba(255,59,48,.2); color: #b3261e; }

/* Chart container — glass frame, near-solid plot area for legibility */
.lg-chart { padding: var(--sp-5); border-radius: var(--lg-r); }
.lg-chart__plot { margin-top: var(--sp-4); border-radius: var(--lg-r-sm); background: var(--lg-fill-strong); padding: var(--sp-3); }
.lg-chart svg { width: 100%; height: auto; display: block; }   /* gridlines rgba(text,.12); series = vivid accents */

/* Toast */
.lg-toast { position: fixed; bottom: var(--sp-5); left: 50%; translate: -50% 0; padding: var(--sp-3) var(--sp-5);
  border-radius: var(--lg-r-pill); animation: lg-toast-in var(--lg-dur) var(--lg-spring); }
@keyframes lg-toast-in { from { transform: translateY(24px) scale(.9); opacity: 0; } }

/* App icon tile + dock */
.lg-icon { width: 64px; aspect-ratio: 1; border-radius: 22.5%; display: grid; place-items: center; font-size: 28px;
  background: linear-gradient(160deg, rgba(255,255,255,.55), rgba(255,255,255,.12)), var(--icon-c, #6c8cff); }
.lg-dock { display: flex; gap: var(--sp-3); padding: var(--sp-3); border-radius: 28px; align-items: end; }
.lg-dock .lg-icon { width: 56px; transition: transform var(--lg-dur) var(--lg-spring); }
.lg-dock .lg-icon:hover { transform: translateY(-8px) scale(1.18); }
.lg-dock .lg-icon:hover + .lg-icon, .lg-dock .lg-icon:has(+ .lg-icon:hover) { transform: translateY(-3px) scale(1.07); }
```

Markup examples (ARIA included):

```html
<article class="glass glass--liquid lg-card" data-liquid>…</article>

<button class="glass glass--liquid lg-btn lg-btn--primary lg-press" data-liquid>Get started</button>
<button class="glass lg-btn lg-btn--secondary lg-press">Learn more</button>

<div class="glass glass--liquid lg-tabs" role="tablist" aria-label="View" data-liquid>
  <span class="lg-tab-pill" aria-hidden="true"></span>
  <button role="tab" aria-selected="true">Day</button><button role="tab" aria-selected="false">Week</button>
  <button role="tab" aria-selected="false">Month</button>
</div>
<!-- JS: tabs.addEventListener('click', e => { const t = e.target.closest('[role=tab]'); if (t) flowTo(tabs, t); });
     plus Left/Right arrow-key handling for the tablist; call flowTo once on load to size the pill. -->

<label class="glass lg-field"><span aria-hidden="true">⌕</span><input type="search" placeholder="Search" aria-label="Search"></label>
<input class="lg-toggle" type="checkbox" role="switch" aria-label="Wi-Fi" checked>
<input class="lg-range" type="range" min="0" max="100" value="50" aria-label="Brightness">
<div class="glass glass--liquid lg-toast" role="status" aria-live="polite">Saved</div>
```

---

## 8. Layout patterns

**macOS glass dashboard** (inspired by the Figma kit register)
```html
<div class="lg-bg" aria-hidden="true">…orbs…</div>
<div class="glass lg-window">
  <header class="lg-window__chrome"><div class="lg-traffic" aria-hidden="true"><span></span><span></span><span></span></div>
    <div class="glass lg-tabs" role="tablist">…</div><label class="glass lg-field" style="margin-left:auto">…</label></header>
  <nav class="glass glass--light lg-sidebar" aria-label="Sections">…</nav>
  <main class="lg-dash">
    <section class="lg-kpis">  <!-- 4× .glass.glass--light.lg-kpi --> </section>
    <section class="lg-charts"><!-- 2× .glass.glass--light.lg-chart --> </section>
    <section class="glass glass--light lg-card lg-activity"><!-- ul activity list --></section>
  </main>
</div>
```
```css
.lg-dash { display: grid; gap: var(--sp-4); padding: var(--sp-4); align-content: start; }
.lg-kpis { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: var(--sp-4); }
.lg-charts { display: grid; grid-template-columns: 2fr 1fr; gap: var(--sp-4); }
@media (max-width: 900px) { .lg-window { grid-template-columns: 1fr; } .lg-sidebar { display: none; } .lg-charts { grid-template-columns: 1fr; } }
```
Dense → **Glassmorphism tier** for tiles; Liquid only on the window chrome, tab control and primary button.

**iOS home with glass widgets** — photo/gradient wallpaper; 4-column icon grid (`.lg-icon`), 2×2
and 4×2 widgets (`.glass.glass--liquid`, radius 28–32px), bottom `.lg-dock`, floating search pill.

**Smart-home control grid** — room tabs (segmented control) on top; grid of square tiles
(`aspect-ratio:1`) each with icon, name and state; tapping toggles a tinted "on" fill
(`--lg-tint: color-mix(in srgb, var(--accent) 35%, transparent)`); long-press opens a sheet from the
tile with a big brightness/temperature slider.

**AI SaaS / DeFi landing hero** — full-bleed animated orbs; floating `.lg-nav` pill; huge headline
(`--lg-fs-hero`) on the background (not on glass); 1–3 tilted glass panels as "product
glimpses" (chat window, token swap card, stats) with refraction + chroma on the front one; glass
pill CTA pair; below: glass feature cards in a bento grid, logo strip, pricing cards.

**Fitness / nutrition tracker dashboard** — dark mode default, vivid accents (lime, coral,
cyan); top row of ring/progress KPI tiles (macros, steps, heart rate); weekly bar chart in a
`.lg-chart`; meal/workout timeline list; floating glass tab bar at the bottom on mobile.

---

## 9. AI style prompt template (for matching hero imagery)

Mirrors liquidglassdesign.com's "style prompt per example" approach. Fill the brackets and hand it
to an image generator (e.g. the image-operations skill) for backgrounds, hero objects or icons:

```
[Subject: e.g. a set of floating UI cards / a glass app icon of a leaf / a pill-shaped button],
rendered as Apple-style liquid glass: thick, transparent, softly frosted glass with real
refraction that bends the background at the rounded edges, bright specular highlights along the
top-left rim, subtle chromatic dispersion at the edges, soft inner glow, gentle drop shadow.
Background: [vivid mesh gradient of #HEX, #HEX, #HEX / blurred aurora orbs / soft-focus photo of …].
Mood: [calm & premium / playful & bright / dark & futuristic]. Lighting: soft studio light from the
top-left. Composition: [centered, generous negative space, 16:9 / 1:1]. Style: minimal,
high-end product render, 3D, ultra-clean, 8k, no text, no logos.
```
Never ask for real brand logos or Apple's own icons/wallpapers in the prompt.

---

## 10. Accessibility & quality guardrails

- **Contrast ≥ 4.5:1** for body text (3:1 for large text/UI glyphs) against the *worst-case*
  backdrop that can pass beneath — check the lightest and darkest spots. If it fails, raise the
  fill (`--lg-fill-strong`), add a tint, or put a local scrim behind the text
  (`background: rgba(255,255,255,.45)` / `rgba(0,0,0,.35)`); keep headlines over the background,
  not on thin glass, when the backdrop is busy.
- **`prefers-reduced-transparency`** → solid surfaces, no refraction:
  ```css
  @media (prefers-reduced-transparency: reduce) {
    .glass { background: var(--lg-fill-solid) !important; -webkit-backdrop-filter: none !important; backdrop-filter: none !important; }
    .glass--liquid::after { display: none; }
  }
  @media (prefers-contrast: more) { .glass { background: var(--lg-fill-solid); border-color: currentColor; } }
  @media (forced-colors: active) { .glass { background: Canvas; border: 1px solid CanvasText; } }
  ```
  Support for the transparency query is still uneven across browsers, so also offer an in-page
  "Reduce transparency" toggle that sets the same class.
- **`prefers-reduced-motion`** → no morph, wobble, tilt, parallax, orb drift or jelly; keep instant
  state changes and short fades (the snippets above check `RM`).
- **Light/dark** via `prefers-color-scheme` tokens (section 2) — test both.
- **≤ 3 stacked blurred layers visible at once** (e.g. background → window → popover). Each
  backdrop blur is a GPU pass; more both costs frames and muddies legibility.
- **Visible keyboard focus** on every glass control (dark core + light halo ring, section 7),
  44×44px minimum hit areas, real `<button>`/`role` semantics, focus returned to the trigger when
  a sheet closes.
- **Performance:** refraction filters are regenerated only on resize (ResizeObserver), not on
  scroll; limit `data-liquid` to ~6–10 elements per view; use the glassmorphism tier for lists,
  tables and dense grids; pause off-screen animation; test on a mid-range phone.
- **Never glass on flat white**, and never put long-form reading text on low-opacity glass.

---

## 11. Pre-delivery checklist (Liquid output)

- [ ] Tier stated (Liquid / Glassmorphism / mixed) and justified by density & performance.
- [ ] Vivid background present behind every glass surface (gradient, orbs or photo) — no flat fills.
- [ ] Tokens defined once in `:root` with a dark-mode override; components use only tokens.
- [ ] Refraction works in Chromium and falls back cleanly to blur in Safari and Firefox (checked in at least one non-Chromium engine or by disabling the JS gate).
- [ ] Specular highlight follows the pointer; tilt only after an explicit user opt-in on mobile.
- [ ] Adaptive tint flips text/tint correctly as the glass passes over light and dark regions.
- [ ] Motion uses the spring/jelly easing; press squish, tab-indicator flow and sheet-from-trigger behave; gooey filter never touches text or a glass parent.
- [ ] Text contrast ≥ 4.5:1 over the worst-case backdrop; scrims added where needed.
- [ ] `prefers-reduced-transparency`, `prefers-reduced-motion`, `prefers-contrast`, `forced-colors` and both colour schemes all verified.
- [ ] ≤ 3 blurred layers stacked anywhere; ≤ ~10 refracting elements per view; scrolling holds 60fps.
- [ ] Visible focus rings on every glass control; keyboard-operable tabs, toggles, sliders, sheets.
- [ ] Hover / focus / active / loading / empty / error / success states designed.
- [ ] No Apple icons, SF Symbols, wallpapers or embedded SF Pro font; any Figma-kit asset credited (CC BY 4.0).
- [ ] Vision QA run (render, breakpoints, states) and the file confirmed in `output/` before delivery.
