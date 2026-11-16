# React Bits — Animated Component & Background Library

[React Bits](https://www.reactbits.dev) ([GitHub](https://github.com/DavidHDev/react-bits)) is an
open-source collection of **140+ high-quality, animated, interactive & fully customizable React
components** for building memorable interfaces. This reference catalogues every capability so the
skill can reach for a ready-made React Bits piece instead of building an effect from scratch.

## How it works

- **Four categories:** Text Animations, Animations, Components, Backgrounds.
- **Four variants per component:** `JS-CSS`, `JS-TW` (Tailwind), `TS-CSS`, `TS-TW` — pick to match
  the target project (JavaScript or TypeScript, plain CSS or Tailwind).
- **Minimal dependencies, tree-shakeable, copy-paste ready** — drops into any modern React project.
- **Fully customizable** — tweak everything via props or edit the source directly.
- **Install three ways:**
  - **CLI (shadcn):** `npx shadcn@latest add @react-bits/BlurText-TS-TW`
  - **CLI (jsrepo):** each component page shows a copy-ready jsrepo command.
  - **Manual:** select your tech (JS/TS, CSS/Tailwind) on the component page and copy the code.
- **AI-friendly:** designed to work with Cursor, GitHub Copilot, and v0 — describe what you need,
  drop it in, ship.
- **Official ports:** Vue -> [vue-bits.dev](https://vue-bits.dev); Svelte -> [sveltebits.xyz](https://sveltebits.xyz).
- **License:** MIT + Commons Clause — free for personal and commercial use.

> React Bits is React-first. This skill's default delivery is a single-file `.html` via the **html**
> skill — so when a build is vanilla HTML/CSS/JS, **port the React Bits effect** (recreate the
> animation/shader in vanilla JS or CSS from the component's technique) rather than importing JSX.
> When the target *is* a React/Vite app, hand the component list to **app-builder** and install via
> the shadcn/jsrepo CLI. Either way, React Bits is the reference vocabulary for the effect.

## Creative Tools (three, free)

| Tool | What it does |
|------|--------------|
| **Background Studio** | Explore animated backgrounds, customize the effect live, export as **video / image / code**. |
| **Shape Magic** | Create inner rounded corners between shapes; export as **SVG, React code, or clip-path code**. |
| **Texture Lab** | Apply **20+ effects** (noise, dithering, ASCII, ...) to images/videos and export in high quality. |

## Text Animations (23)

ASCIIText · BlurText · CircularText · CountUp · CurvedLoop · DecryptedText · FallingText ·
FuzzyText · GlitchText · GradientText · RotatingText · ScrambledText · ScrollFloat · ScrollReveal ·
ScrollVelocity · ShinyText · Shuffle · SplitText · TextCursor · TextPressure · TextType · TrueFocus ·
VariableProximity

*Use for:* animated headlines, kinetic hero type, scroll-linked text reveals, decrypt/scramble
effects, counters, curved/circular labels, variable-font proximity motion.

## Animations (31)

AnimatedContent · Antigravity · BlobCursor · ClickSpark · Crosshair · Cubes · CursorGrid ·
ElectricBorder · FadeContent · GhostCursor · GlareHover · GradualBlur · ImageTrail · LaserFlow ·
LogoLoop · MagicRings · Magnet · MagnetLines · MetaBalls · MetallicPaint · Noise · OrbitImages ·
PixelTrail · PixelTransition · Ribbons · ShapeBlur · SplashCursor · StarBorder · StickerPeel ·
Strands · TargetCursor

*Use for:* custom cursors & pointer effects (blob, ghost, crosshair, target, splash, trail),
magnetic UI, click sparks, hover glare, image trails, logo loops/marquees, metaballs, animated
borders, reveal-on-scroll wrappers.

## Components (37)

AnimatedList · BorderGlow · BounceCards · BubbleMenu · CardNav · CardSwap · Carousel · ChromaGrid ·
CircularGallery · Counter · DecayCard · Dock · DomeGallery · ElasticSlider · FlowingMenu ·
FluidGlass · FlyingPosters · Folder · GlassIcons · GlassSurface · GooeyNav · InfiniteMenu · Lanyard ·
LineSidebar · MagicBento · Masonry · ModelViewer · PillNav · PixelCard · ProfileCard ·
ReflectiveCard · ScrollStack · SpotlightCard · Stack · StaggeredMenu · Stepper · TiltedCard

*Use for:* navs & menus (dock, gooey, pill, bubble, card, staggered, flowing, line sidebar),
galleries & carousels (circular, dome, flying posters, rolling), cards (tilted, spotlight, chroma,
pixel, profile, reflective, decay, bounce, glass), 3D model viewer, bento grids, masonry, steppers,
sliders, counters, animated lists, physics lanyard.

## Backgrounds (45)

Aurora · Balatro · Ballpit · Beams · ColorBends · DarkVeil · Dither · DotField · DotGrid · EvilEye ·
FaultyTerminal · Ferrofluid · FloatingLines · Galaxy · GradientBlinds · Grainient · GridDistortion ·
GridMotion · GridScan · Hyperspeed · Iridescence · LetterGlitch · LightPillar · LightRays ·
Lightfall · Lightning · LineWaves · LiquidChrome · LiquidEther · Orb · Particles · PixelBlast ·
PixelSnow · Plasma · PlasmaWave · Prism · PrismaticBurst · Radar · RippleGrid · ShapeGrid ·
SideRays · Silk · SoftAurora · Threads · Waves

*Use for:* full-bleed animated hero/section backgrounds — WebGL/shader (aurora, silk, plasma,
iridescence, liquid chrome/ether, prismatic burst, dark veil, galaxy, hyperspeed), geometric (dot
grid, grid motion/distortion/scan, shape grid, ripple grid), particle/physics (ballpit, particles,
ferrofluid, pixel blast/snow), lines & rays (beams, threads, waves, light rays/pillar, side rays,
lightning), and retro/glitch (faulty terminal, letter glitch, dither, grainient).

## Choosing & using React Bits in a build

1. **Match effect -> component.** Name the effect the design needs (e.g. "animated aurora hero
   background", "split-text headline reveal", "magnetic dock nav") and pick the exact React Bits
   component from the lists above.
2. **Pick the variant** that fits the target project (`JS-CSS` / `JS-TW` / `TS-CSS` / `TS-TW`).
3. **React target ->** install via `npx shadcn@latest add @react-bits/<Name>-<Variant>` (or jsrepo)
   and customize props; hand the list to **app-builder** for multi-route apps.
   **Vanilla `.html` target ->** recreate the effect's technique in vanilla JS/CSS/WebGL (React Bits
   is the design reference), or use **Background Studio** to export a background as code/video.
4. **Keep it performant & inclusive** — lazy-load heavy WebGL backgrounds, cap them to one hero
   moment, and always ship a `prefers-reduced-motion` fallback (a static gradient / first frame).
5. **Attribution:** React Bits is MIT + Commons Clause — free for personal & commercial use; keep
   the license notice when you copy source verbatim.
