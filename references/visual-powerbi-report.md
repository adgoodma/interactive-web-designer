# Visual PowerBi Report — premium, single-file interactive report

**Trigger:** "Visual PowerBi Report" (any case), "Power BI style report", "Power BI style
dashboard", "make it look like a Power BI report". Output: ONE self-contained `.html` page that
looks and behaves like a premium modern Power BI report — 16:9 canvas feel, deep-colour nav rail,
slicers, KPI cards and a library of hand-built SVG visuals with cross-filtering. **No external
dependencies** (no chart libraries, fonts or CDNs) so the file opens anywhere.

> This is a *designed HTML report in the Power BI visual language* — not a .pbix and not a live
> connection. If the user wants numbers from their real Power BI model, fetch them first with the
> **powerbi** skill, then pour them into the `DATA` object here.

## 1. Page anatomy

```
┌──────────┬──────────────────────────────────────────────────────────────────┐
│ [Brand]  │ Conversion & velocity  [Sample data]     Region▾ Segment▾ Month▾  │
│          │ one-line muted subtitle                  [MTD|QTD|YTD] [Reset]    │
│ SALES    ├─────────────┬─────────────┬─────────────┬────────────────────────┤
│ ▸ Conv.  │ HERO KPI    │ KPI         │ KPI         │ KPI                    │  row 1: 4 × s3
│ PEOPLE   ├─────────────┴─────────────┴───────┬─────┴────────────────────────┤
│ ▸ Workf. │ primary visual (≈60–65%)          │ side visual / brief         │  row 2: s8 + s4
│ OPS      ├───────────────────────┬───────────┴──────────────────────────────┤
│ ▸ Supply │ smaller visual        │ smaller visual / table                    │  row 3: s6+s6 / s5+s3+s4
│ ───────  │                       │                                           │
│ Definit. │                       │                                           │
└──────────┴───────────────────────┴───────────────────────────────────────────┘
```

- **Nav rail (~200px)** in the deep theme colour: brand mark top-left (`[Brand]` placeholder
  unless the user supplies one), grouped sections with 9.5px uppercase headings, icon + label
  items, active item = lighter fill + white inset bar, **"Definitions & data" pinned at bottom**
  (opens a dialog with metric definitions + data note). Each nav item is a *report page* with its
  own layout. The glass sidebar ([glass-navigation.md](glass-navigation.md)) can replace it on request.
- **Header:** 30px/800 editorial title with a punchy phrase ("Conversion & velocity", "People,
  with perspective.", "Supply, in motion.") + one-line muted subtitle; amber **Sample data**
  chip while sample data is in use.
- **Slicer bar (top-right):** dropdown slicers that open as **checkbox lists** (Region, Segment),
  a single-select **Reporting month** (e.g. "Aug 2026"), a **MTD / QTD / YTD** segmented toggle,
  and a primary **Reset filters** button.
- **Row 1 — 4 KPI cards:** label · big number (32.3%, 1,284, $25.29m, 59 days) · green/red delta
  with context ("vs prior month"; cost-type metrics invert the colour) · small icon top-right ·
  micro-visual (sparkline, area sparkline, progress bar, stacked bar, dot strip, mini columns).
  The first may be a **hero** card filled with the dark theme colour, white text.
- **Row 2:** wide primary visual + side visual or narrative brief card.
- **Row 3:** 2–3 smaller visuals / table.

## 2. Visual style tokens

| Token | Value |
|---|---|
| Canvas | `#F4F6FA` |
| Card | white, radius 12–16px (14 default), 1px hairline `#E6E9F0`, soft shadow `0 4px 14px -4px rgba(16,24,40,.08)`, 14–16px padding |
| Titles | 13–14px bold card title + 11px muted subtitle; tiny **pill tabs** top-right ("Chart \| Table", "Headcount \| FTE") + focus-mode icon |
| Type | `"Segoe UI Variable","Segoe UI",Inter,system-ui`; **tabular numerals** for all figures |
| Palette | single hue per report: `--c1…--c5` (dark → pale) + grey; **green / amber / red only for status** (`#1E9E5A` / `#E59A1A` / `#D64545`) |

### Theme presets (match the three reference pages)

| Preset | `--nav` (rail / hero) | `--c1` accent | Page register |
|---|---|---|---|
| **Sales Blue** | `#0B2A5B` | `#1F5FD6` | "Conversion & velocity" — funnel, loss donut, dumbbell, lollipop |
| **People Purple** | `#3B1E6E` | `#6B3FB8` | "People, with perspective." — workforce line, org donut, people brief, hiring chevrons, movement, drivers |
| **Supply Navy** | `#0A2240` (hero `#0E2F5C`) | `#1660C7` | "Supply, in motion." — network flow, dark operating brief, promise line, inventory donut, exceptions table |

Switch with `<body data-theme="sales|people|supply">` (the template sets it per page).
For a user brand, override `--nav` and `--c1…--c5` with a 5-step ramp of one hue.

## 3. Visual library checklist

Every visual is a pure function in `V` returning SVG/HTML; all elements carry `data-cat`
(cross-filter key) + `data-tip` (tooltip) and animate on entry.

- [ ] **KPI card variants** — standard, hero (dark fill), with sparkline / area sparkline / progress / stacked bar / dot strip / mini columns; inverted-delta option
- [ ] **Funnel / conversion journey** — tall bars per stage, translucent tapering area between stages, stage labels, **conversion % chips**
- [ ] **Donut** — centre total + caption, legend with values (legend items are cross-filter buttons)
- [ ] **Line / area over time** — two series, gridlines, point tooltips, **legend toggles** (≥1 series always visible), metric switch tabs
- [ ] **Sankey / network flow** — node boxes per column, curved cubic connectors with width ∝ volume
- [ ] **Dumbbell / range bars** — actual vs target markers, over-target rows tinted amber, "Nd" labels
- [ ] **Lollipop bars** — win rate by segment with % labels
- [ ] **Chevron / step process** — "From hello to hired" with step conversion %
- [ ] **Diverging columns** — positive (theme) / negative (red) movement around a zero line
- [ ] **Bullet bars** — score bar + benchmark tick, coloured by above/below
- [ ] **Table with status pills** — On track / At risk / Late
- [ ] **Narrative brief card** — numbered, status-coloured items with owner lines; light or dark (`dark:true`)

## 4. Interactivity contract (all required)

| Feature | How the template does it |
|---|---|
| Slicers filter all visuals | Region / Segment / Month / Period update `S`; `ctx()` derives a volume factor + deterministic jitter; every page re-renders from `DATA` |
| Cross-filtering | Click/Enter on any `[data-cat]` (donut slice, bar, legend item, node, row) sets `S.hl`; page gets `.has-hl` and every non-matching element dims to 22%. A highlighted **segment or region also re-computes the KPIs**. Click again / Esc / empty card space clears |
| Rich tooltips | `data-tip="Title\nline\nline"` on marks; shown on hover **and keyboard focus** |
| Animated entry | `grow-y`, `grow-x`, `draw` (stroke-dash), `spin-in`, staggered `animation-delay`; cards rise in |
| Card toggles | `tabs:['Chart','Table']` or metric switches (`Headcount \| FTE`) stored in `S.tog[id]` |
| KPI count-up | `[data-count]` eased 0 → value in 900ms (skipped under reduced motion) |
| Page navigation | Rail buttons swap `S.page` → new title, theme and layout |
| Focus mode | Expand icon → card goes fixed full-screen over a scrim; Esc / scrim restores and returns focus |
| Reset | Primary "Reset filters" restores every slicer, highlight and hidden series |
| Responsive | 12-col grid → 2 columns ≤1200px → 1 column ≤760px (rail becomes a horizontal strip) |
| Accessible | Landmarks, `aria-current` page, `aria-pressed` toggles, `aria-expanded` dropdowns, real checkbox/radio inputs, `role="img"` + labels on SVGs, marks focusable (`tabindex=0`, `role="button"`), visible focus, reduced-motion fallback |

## 5. Data — one swappable object

All numbers live in `const DATA = {…}` at the top of the script, flagged `sample: true`. The
"Sample data" chip and the Definitions dialog announce it. To use real data:

1. Keep the shape (`months`, `dims`, `periodMult`, and one block per page) and replace values;
   set `sample:false` — the chip disappears.
2. For row-level data, replace `ctx()` with a real aggregation (filter fact rows by `S.region`,
   `S.segment`, `S.month`, `S.period`, then sum) — the visual functions don't change.
3. **Never invent business figures and present them as real.** Sample data stays labelled.

## 6. Build workflow

1. Phase 0 of the main skill still runs: confirm pages, theme preset (or brand colours) and data
   source (sample vs user data / Power BI via the powerbi skill).
2. Pick each page's **title phrase + subtitle**, its 4 KPIs and 3–6 visuals from the library.
3. Start from the template below; add/remove pages in `PAGES` + rail buttons; set `THEME`.
4. Deliver via the html skill into `output/`; run Vision QA at 1440, 1100 and 390px widths.

## 7. Template — self-contained HTML/CSS/JS (3 pages, 3 themes, no dependencies)

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Visual PowerBi Report — sample</title>
<style>
/* ================= 1. THEME TOKENS (3 presets) ================= */
:root{
  --canvas:#F4F6FA; --card:#fff; --line:#E6E9F0; --ink:#0F1B2D; --ink-2:#5B6577; --ink-3:#8A93A3;
  --good:#1E9E5A; --warn:#E59A1A; --bad:#D64545;
  --radius:14px; --shadow:0 1px 2px rgba(16,24,40,.04),0 4px 14px -4px rgba(16,24,40,.08);
  --font:"Segoe UI Variable","Segoe UI",Inter,system-ui,sans-serif;
  --ease:cubic-bezier(.22,1,.36,1);
}
/* Sales Blue */
[data-theme="sales"]{--nav:#0B2A5B;--nav-2:#14386F;--c1:#1F5FD6;--c2:#4C86EA;--c3:#8DB3F4;--c4:#C9DBFA;--c5:#E9F0FD;--hero:#0B2A5B}
/* People Purple */
[data-theme="people"]{--nav:#3B1E6E;--nav-2:#4B2A85;--c1:#6B3FB8;--c2:#8B63CF;--c3:#B39AE2;--c4:#D9CCF2;--c5:#F1ECFA;--hero:#3B1E6E}
/* Supply Navy */
[data-theme="supply"]{--nav:#0A2240;--nav-2:#123158;--c1:#1660C7;--c2:#3F82DD;--c3:#86B2EC;--c4:#C6DBF6;--c5:#EAF2FC;--hero:#0E2F5C}

*{box-sizing:border-box}
html,body{margin:0;height:100%}
body{font-family:var(--font);background:var(--canvas);color:var(--ink);font-size:13px;-webkit-font-smoothing:antialiased}
.num,td.n{font-variant-numeric:tabular-nums}
button{font:inherit;color:inherit}
:focus-visible{outline:2px solid var(--c1);outline-offset:2px}

/* ================= 2. LAYOUT ================= */
.app{display:grid;grid-template-columns:200px 1fr;min-height:100vh}
.rail{background:var(--nav);color:#fff;display:flex;flex-direction:column;padding:18px 12px;position:sticky;top:0;height:100vh;transition:background .4s}
.brand{display:flex;align-items:center;gap:8px;font-weight:800;font-size:13px;line-height:1.05;padding:0 8px 18px;letter-spacing:-.01em}
.brand i{width:26px;height:26px;border-radius:7px;background:linear-gradient(135deg,var(--c2),#fff3);display:block}
.rail h6{margin:14px 8px 4px;font-size:9.5px;letter-spacing:.14em;text-transform:uppercase;color:#ffffff8c;font-weight:700}
.rail button{all:unset;display:flex;align-items:center;gap:10px;padding:8px 10px;border-radius:8px;color:#ffffffcc;cursor:pointer;font-size:12.5px}
.rail button:hover{background:#ffffff14;color:#fff}
.rail button[aria-current="page"]{background:#ffffff24;color:#fff;font-weight:600;box-shadow:inset 3px 0 0 #fff}
.rail button:focus-visible{outline:2px solid #fff}
.rail .spacer{flex:1}
.rail svg{width:16px;height:16px;flex:0 0 16px}

main{padding:22px 26px 30px;min-width:0}
.top{display:flex;gap:16px;align-items:flex-start;justify-content:space-between;flex-wrap:wrap;margin-bottom:16px}
.ttl h1{margin:0;font-size:30px;font-weight:800;letter-spacing:-.02em}
.ttl p{margin:4px 0 0;color:var(--ink-2)}
.sample{display:inline-block;margin-left:8px;font-size:10px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;background:#FFF4D6;color:#8A5A00;border-radius:999px;padding:2px 8px;vertical-align:middle}
.slicers{display:flex;gap:8px;align-items:flex-end;flex-wrap:wrap}
.slicer{position:relative;display:flex;flex-direction:column;gap:3px}
.slicer>label,.slicer>span{font-size:10.5px;color:var(--ink-3)}
.dd-btn{min-width:130px;display:flex;justify-content:space-between;align-items:center;gap:8px;background:#fff;border:1px solid var(--line);border-radius:8px;padding:6px 10px;cursor:pointer}
.dd-btn::after{content:"";width:6px;height:6px;border-right:1.5px solid var(--ink-2);border-bottom:1.5px solid var(--ink-2);transform:rotate(45deg) translateY(-2px)}
.dd{position:absolute;top:100%;left:0;z-index:30;margin-top:4px;min-width:170px;background:#fff;border:1px solid var(--line);border-radius:10px;box-shadow:0 12px 30px -8px rgba(16,24,40,.25);padding:6px;display:none}
.dd.open{display:block;animation:pop .18s var(--ease)}
.dd label{display:flex;gap:8px;align-items:center;padding:6px 8px;border-radius:6px;cursor:pointer}
.dd label:hover{background:var(--c5)}
.dd input{accent-color:var(--c1)}
.seg{display:inline-flex;background:#fff;border:1px solid var(--line);border-radius:8px;padding:2px}
.seg button{border:0;background:none;padding:5px 10px;border-radius:6px;cursor:pointer;font-size:12px;color:var(--ink-2)}
.seg button[aria-pressed="true"]{background:var(--c1);color:#fff}
.btn{border:0;border-radius:8px;padding:7px 14px;font-weight:600;cursor:pointer}
.btn.primary{background:var(--c1);color:#fff}.btn.ghost{background:#fff;border:1px solid var(--line)}

/* 12-col grid */
.grid{display:grid;grid-template-columns:repeat(12,1fr);gap:14px}
.s3{grid-column:span 3}.s4{grid-column:span 4}.s5{grid-column:span 5}.s6{grid-column:span 6}.s7{grid-column:span 7}.s8{grid-column:span 8}.s12{grid-column:span 12}
@media (max-width:1200px){.s3,.s4,.s5{grid-column:span 6}.s6,.s7,.s8{grid-column:span 12}}
@media (max-width:760px){
  .app{grid-template-columns:1fr}.rail{position:static;height:auto;flex-direction:row;flex-wrap:nowrap;overflow-x:auto;padding:10px;gap:4px}
  .rail h6,.rail .spacer{display:none}.brand{padding:0 8px 0 0}.rail button{white-space:nowrap}
  .grid>*{grid-column:span 12!important} main{padding:16px}.ttl h1{font-size:24px}
}

/* ================= 3. CARDS ================= */
.card{background:var(--card);border:1px solid var(--line);border-radius:var(--radius);box-shadow:var(--shadow);padding:14px 16px;min-width:0;position:relative;animation:rise .5s var(--ease) both}
.card h3{margin:0;font-size:13.5px;font-weight:700}
.card .sub{color:var(--ink-3);font-size:11px;margin-top:2px}
.chead{display:flex;align-items:flex-start;gap:8px;margin-bottom:8px}
.chead .grow{flex:1;min-width:0}
.tabs{display:inline-flex;background:var(--c5);border-radius:999px;padding:2px}
.tabs button{border:0;background:none;font-size:10.5px;padding:3px 9px;border-radius:999px;cursor:pointer;color:var(--ink-2)}
.tabs button[aria-pressed="true"]{background:var(--c1);color:#fff}
.icon-btn{border:0;background:none;width:24px;height:24px;border-radius:6px;cursor:pointer;color:var(--ink-3);display:grid;place-items:center}
.icon-btn:hover{background:var(--c5);color:var(--ink)}
.kpi .lbl{font-size:12px;font-weight:600;color:var(--ink-2)}
.kpi .big{font-size:30px;font-weight:800;letter-spacing:-.02em;margin:4px 0 2px}
.kpi .delta{font-size:11px;font-weight:600}
.kpi .delta.up{color:var(--good)}.kpi .delta.down{color:var(--bad)}
.kpi .delta span{color:var(--ink-3);font-weight:400}
.kpi .ico{position:absolute;top:14px;right:14px;color:var(--ink-3)}
.kpi.hero{background:var(--hero);color:#fff;border-color:transparent}
.kpi.hero .lbl,.kpi.hero .delta span{color:#ffffffb3}.kpi.hero .ico{color:#ffffffa0}.kpi.hero .delta.up{color:#7EE2A8}.kpi.hero .delta.down{color:#FF9C9C}
.micro{margin-top:8px;height:38px}
.micro svg{width:100%;height:100%;display:block;overflow:visible}
svg text{font-family:var(--font);fill:var(--ink-2);font-size:10px}
.viz svg{width:100%;height:auto;display:block;overflow:visible}

/* status pills */
.pill{display:inline-block;padding:2px 8px;border-radius:999px;font-size:10.5px;font-weight:600;white-space:nowrap}
.pill.good{background:#E3F5EB;color:#167443}.pill.warn{background:#FDF1DC;color:#9A5F00}.pill.bad{background:#FBE4E4;color:#A92D2D}.pill.info{background:var(--c5);color:var(--c1)}
table{width:100%;border-collapse:collapse;font-size:12px}
th{text-align:left;font-weight:600;color:var(--ink-3);font-size:10.5px;text-transform:uppercase;letter-spacing:.06em;padding:6px 8px;border-bottom:1px solid var(--line)}
td{padding:7px 8px;border-bottom:1px solid var(--line)}td.n{text-align:right}
.legend{display:flex;flex-wrap:wrap;gap:6px 12px;font-size:11px;color:var(--ink-2);margin-top:6px}
.legend button{all:unset;display:flex;align-items:center;gap:6px;cursor:pointer;padding:2px 4px;border-radius:4px}
.legend button[aria-pressed="false"]{opacity:.4}
.legend i{width:9px;height:9px;border-radius:3px;display:block}
.legend b{color:var(--ink);font-variant-numeric:tabular-nums}
.brief li{display:flex;gap:10px;padding:9px 0;border-bottom:1px solid var(--line);align-items:flex-start}
.brief ol{list-style:none;margin:0;padding:0}
.brief .no{flex:0 0 26px;height:26px;border-radius:50%;display:grid;place-items:center;font-weight:700;color:#fff;font-size:12px}
.brief .t{font-weight:600}.brief .m{color:var(--ink-3);font-size:11px}
.card.dark{background:var(--hero);color:#fff;border-color:transparent}.card.dark .sub,.card.dark .m{color:#ffffffa6}.card.dark li{border-color:#ffffff1f}
.chev{display:flex;gap:4px}
.chev div{flex:1;min-width:0;padding:10px 8px 10px 22px;color:#fff;clip-path:polygon(0 0,calc(100% - 14px) 0,100% 50%,calc(100% - 14px) 100%,0 100%,14px 50%);cursor:pointer;transition:opacity .2s}
.chev div:first-child{clip-path:polygon(0 0,calc(100% - 14px) 0,100% 50%,calc(100% - 14px) 100%,0 100%);padding-left:12px;border-radius:8px 0 0 8px}
.chev b{display:block;font-size:17px}.chev small{font-size:10.5px;opacity:.85}

/* ================= 4. CROSS-FILTER + MOTION ================= */
[data-cat]{cursor:pointer;transition:opacity .25s}
.report.has-hl [data-cat]:not(.hl){opacity:.22}
.grow-y{transform-box:fill-box;transform-origin:bottom;animation:growY .8s var(--ease) both}
.grow-x{transform-box:fill-box;transform-origin:left;animation:growX .8s var(--ease) both}
.draw{stroke-dasharray:var(--len,2000);stroke-dashoffset:var(--len,2000);animation:draw 1.1s var(--ease) forwards}
.fade{animation:fade .8s var(--ease) both}
.spin-in{transform-box:view-box;transform-origin:center;animation:spin .9s var(--ease) both}
@keyframes growY{from{transform:scaleY(0)}}@keyframes growX{from{transform:scaleX(0)}}
@keyframes draw{to{stroke-dashoffset:0}}@keyframes fade{from{opacity:0}}
@keyframes spin{from{transform:rotate(-90deg) scale(.6);opacity:0}}
@keyframes rise{from{opacity:0;transform:translateY(8px)}}@keyframes pop{from{opacity:0;transform:translateY(-4px)}}
@media (prefers-reduced-motion:reduce){*,*::before,*::after{animation:none!important;transition:none!important}.draw{stroke-dashoffset:0}}

/* tooltip, focus mode, dialog */
.tt{position:fixed;z-index:100;pointer-events:none;background:#0F1B2D;color:#fff;border-radius:8px;padding:8px 10px;font-size:11.5px;line-height:1.45;box-shadow:0 10px 24px rgba(0,0,0,.25);opacity:0;transition:opacity .12s;max-width:240px}
.tt.show{opacity:1}.tt b{display:block;font-size:12px;margin-bottom:2px}
.scrim{position:fixed;inset:0;background:rgba(15,27,45,.45);z-index:60;display:none}
.scrim.show{display:block}
.card.focus{position:fixed;inset:5vh 6vw;z-index:70;overflow:auto;animation:pop .25s var(--ease)}
dialog{border:0;border-radius:14px;padding:22px;max-width:560px;box-shadow:0 30px 60px rgba(0,0,0,.3)}
dialog::backdrop{background:rgba(15,27,45,.45)}
.sr{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0)}
</style>
</head>
<body data-theme="sales">
<div class="app">
  <nav class="rail" aria-label="Report pages">
    <div class="brand"><i aria-hidden="true"></i><span>[Brand]<br>Insights</span></div>
    <h6>Sales</h6>
    <button data-page="sales"><svg viewBox="0 0 24 24"><path d="M3 4h18l-7 8v6l-4 2v-8z" fill="none" stroke="currentColor" stroke-width="2"/></svg>Conversion</button>
    <h6>People</h6>
    <button data-page="people"><svg viewBox="0 0 24 24"><circle cx="9" cy="8" r="3.5" fill="none" stroke="currentColor" stroke-width="2"/><path d="M2 20c0-4 3-6 7-6s7 2 7 6M16 4.5a3.5 3.5 0 0 1 0 7M18 14c2.5.6 4 2.6 4 6" fill="none" stroke="currentColor" stroke-width="2"/></svg>Workforce</button>
    <h6>Operations</h6>
    <button data-page="supply"><svg viewBox="0 0 24 24"><path d="M3 7h11v9H3zM14 10h4l3 3v3h-7" fill="none" stroke="currentColor" stroke-width="2"/><circle cx="7" cy="18" r="2" fill="currentColor"/><circle cx="17" cy="18" r="2" fill="currentColor"/></svg>Supply</button>
    <div class="spacer"></div>
    <button id="defsBtn"><svg viewBox="0 0 24 24"><path d="M5 4h11l3 3v13H5z" fill="none" stroke="currentColor" stroke-width="2"/><path d="M8 10h8M8 14h8" stroke="currentColor" stroke-width="2"/></svg>Definitions &amp; data</button>
  </nav>

  <main class="report" id="report">
    <header class="top">
      <div class="ttl"><h1 id="h1"></h1><p id="subtitle"></p></div>
      <div class="slicers" id="slicers"></div>
    </header>
    <div class="grid" id="grid" aria-live="polite"></div>
  </main>
</div>
<div class="tt" id="tt" role="tooltip"></div>
<div class="scrim" id="scrim"></div>
<dialog id="defs" aria-labelledby="defsTitle"><h2 id="defsTitle" style="margin-top:0">Definitions &amp; data</h2><div id="defsBody"></div><form method="dialog"><button class="btn primary">Close</button></form></dialog>

<script>
/* =====================================================================
   5. DATA — ONE swappable object. Everything below is SAMPLE data.
   Replace with the user's data (same shape) and set sample:false.
   ===================================================================== */
const DATA = {
  sample: true,
  months: ['Sep 2025','Oct 2025','Nov 2025','Dec 2025','Jan 2026','Feb 2026','Mar 2026','Apr 2026','May 2026','Jun 2026','Jul 2026','Aug 2026'],
  defaultMonth: 'Aug 2026',
  dims: {
    region:  { 'North America':.42, 'EMEA':.31, 'APAC':.19, 'LATAM':.08 },   // share of volume
    segment: { 'Enterprise':.38, 'Mid-market':.40, 'SMB':.22 }
  },
  periodMult: { MTD:1, QTD:2.9, YTD:7.6 },
  definitions: [
    ['Win rate','Won deals ÷ (won + lost) closed in period.'],
    ['Median won cycle','Median days from opportunity creation to close-won.'],
    ['Voluntary turnover','Voluntary exits ÷ average headcount, trailing 12 months.'],
    ['OTIF','Orders delivered on time and in full ÷ all orders shipped.']
  ],
  sales: {
    title:'Conversion & velocity', subtitle:'Where conversion is won, where we lose, and where time goes.',
    kpi:{ winRate:32.3, won:10, lost:21, cycle:59, winTrend:[28,29,31,30,33,31,32,34,31,33,32,32.3], cycleTrend:[66,64,65,62,63,61,60,62,59,60,58,59] },
    funnel:[['Qualified leads',114],['Opportunities',80],['Proposals',65],['Negotiation',32],['Won',10]],
    lossReasons:[['Price',8],['Competition',6],['No decision',4],['Timing',3]],
    stageTime:[['Discovery',21,14],['Qualified',33,24],['Proposal',27,20],['Negotiation',18,15]],   // [stage, actual, target]
    segWin:[['Enterprise',36.1],['Mid-market',30.4],['SMB',28.2]]
  },
  people: {
    title:'People, with perspective.', subtitle:'A clear view of our people, their experience and what needs attention.',
    kpi:{ headcount:1284, turnover:5.1, engagement:78.1, openRoles:42, hcTrend:[1190,1198,1205,1214,1222,1231,1240,1249,1258,1266,1276,1284] },
    workforce:{ Headcount:[1190,1198,1205,1214,1222,1231,1240,1249,1258,1266,1276,1284], FTE:[1121,1127,1133,1140,1148,1155,1163,1171,1179,1186,1195,1203], Hires:[22,18,15,20,24,19,21,23,22,18,25,20] },
    org:[['Engineering',412],['Sales',268],['Operations',231],['Customer success',187],['Corporate',186]],
    hiring:[['Applied',1563],['Screened',681],['Interviewed',214],['Offered',58],['Hired',42]],
    movement:[['Hires',61],['Promotions',24],['Internal moves',18],['Voluntary exits',-31],['Involuntary exits',-9]],
    drivers:[['Manager support',82,75],['Growth & learning',71,75],['Recognition',68,72],['Workload',63,70],['Belonging',79,75]], // [driver, score, benchmark]
    brief:[['bad','Unblock critical hiring','12 roles open > 60 days in Engineering','Owner: Talent lead'],['warn','Keep workloads sustainable','Workload score 63, below 70 benchmark','Owner: HR business partners'],['good','Act on rising attention','Engagement up 1.4 pts vs prior month','Owner: People analytics']]
  },
  supply: {
    title:'Supply, in motion.', subtitle:'Live supply performance, risks and the customer promise.',
    kpi:{ otif:92.6, inventory:25.29, atRisk:2, cost:2.91, otifTrend:[90.1,90.8,91.5,90.9,91.8,92.4,91.9,92.8,92.1,93.0,92.2,92.6], invMix:[['Healthy',.68],['Slow-moving',.22],['Excess',.10]] },
    network:{ nodes:[['Asia suppliers',0],['EU suppliers',0],['Domestic suppliers',0],['Plant A',1],['Plant B',1],['Central DC',1],['Customers',2]],
              links:[['Asia suppliers','Plant A',38],['Asia suppliers','Central DC',14],['EU suppliers','Plant B',27],['EU suppliers','Central DC',9],['Domestic suppliers','Plant A',12],['Domestic suppliers','Central DC',20],['Plant A','Customers',50],['Plant B','Customers',27],['Central DC','Customers',43]] },
    promise:{ Promised:[95,95,95,95,95,95,95,95,95,95,95,95], Delivered:[90.1,90.8,91.5,90.9,91.8,92.4,91.9,92.8,92.1,93.0,92.2,92.6] },
    exposure:[['Healthy',17.2],['Slow-moving',5.6],['Excess',2.5]],
    brief:[['bad','Expedite late inbound','PO 4471 from Asia suppliers is 6 days late','Owner: Supply planning'],['warn','Rebalance stock','Central DC overstocked on 3 SKUs','Owner: Inventory lead'],['info','Recover delayed deliveries','2 customer orders at risk this week','Owner: Logistics']],
    exceptions:[['Order 8812','Retail','Plant A','Late','bad'],['Order 8790','Wholesale','Central DC','At risk','warn'],['PO 4471','—','Asia suppliers','Late','bad'],['Order 8755','Retail','Plant B','On track','good']]
  }
};

/* ================= 6. STATE ================= */
const S = { page:'sales', month:DATA.defaultMonth, period:'MTD',
  region:Object.keys(DATA.dims.region), segment:Object.keys(DATA.dims.segment),
  hl:null, tog:{}, hidden:{} };
const $ = s => document.querySelector(s);
const esc = s => String(s).replace(/[&<>"]/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;'}[c]));
const tip = (t, ...l) => `data-tip="${esc([t,...l].join('\n'))}"`;
const cat = c => `data-cat="${esc(c)}" tabindex="0" role="button" aria-label="${esc(c)}" ${S.hl===c?'class="hl"':''}`;
const fmt = { int:v=>Math.round(v).toLocaleString('en-US'), pct:v=>v.toFixed(1)+'%', m:v=>'$'+v.toFixed(2)+'m', d:v=>Math.round(v)+' days' };

/* Filter context: volume factor from slicers, deterministic jitter for rates. */
function ctx(){
  const share = (dim, sel) => sel.reduce((a,k)=>a+DATA.dims[dim][k],0);
  let seg = S.segment, reg = S.region;
  if (S.hl && DATA.dims.segment[S.hl]!==undefined) seg = [S.hl];      // cross-filter KPIs by highlighted segment
  if (S.hl && DATA.dims.region[S.hl]!==undefined)  reg = [S.hl];
  const mi = DATA.months.indexOf(S.month);
  const f = share('region',reg) * share('segment',seg) * DATA.periodMult[S.period] * (0.9 + 0.1*(mi+1)/12);
  const key = reg.join()+seg.join()+S.month+S.period;
  const j = salt => { let h=2166136261; for (const ch of key+salt) h=Math.imul(h^ch.charCodeAt(0),16777619); return ((h>>>0)%2000)/1000-1; };
  return { f, s:share('region',reg)*share('segment',seg), mi, j, n:v=>Math.max(0,Math.round(v*f)), r:(v,spread,s)=>v + j(s)*spread };
}

/* ================= 7. VISUAL LIBRARY (hand-built SVG) ================= */
const PAL = ['var(--c1)','var(--c2)','var(--c3)','var(--c4)','#9AA4B5'];
const V = {
  spark(vals, {area=false, color='var(--c1)', w=200, h=38}={}){
    const mn=Math.min(...vals), mx=Math.max(...vals), x=i=>i*(w/(vals.length-1)), y=v=>h-4-(v-mn)/((mx-mn)||1)*(h-8);
    const d=vals.map((v,i)=>(i?'L':'M')+x(i).toFixed(1)+','+y(v).toFixed(1)).join('');
    return `<svg viewBox="0 0 ${w} ${h}" preserveAspectRatio="none" aria-hidden="true">${area?`<path class="fade" d="${d}L${w},${h}L0,${h}Z" fill="${color}" opacity=".25"/>`:''}<path class="draw" pathLength="2000" d="${d}" fill="none" stroke="${color}" stroke-width="2" vector-effect="non-scaling-stroke"/></svg>`;
  },
  dots(n, filled, color='var(--c1)'){ let s=''; for(let i=0;i<n;i++) s+=`<circle class="fade" style="animation-delay:${i*30}ms" cx="${6+i*14}" cy="10" r="5" fill="${i<filled?color:'#ffffff40'}"/>`; return `<svg viewBox="0 0 ${n*14} 20" aria-hidden="true">${s}</svg>`; },
  progress(p, color='var(--c1)'){ return `<svg viewBox="0 0 200 20" preserveAspectRatio="none" aria-hidden="true"><rect y="6" width="200" height="8" rx="4" fill="var(--c5)"/><rect class="grow-x" y="6" width="${Math.min(200,p*2)}" height="8" rx="4" fill="${color}"/></svg>`; },
  stack(parts){ let x=0; const cs=['var(--good)','var(--warn)','var(--bad)']; return `<svg viewBox="0 0 200 20" preserveAspectRatio="none" aria-hidden="true">${parts.map(([l,p],i)=>{const r=`<rect class="grow-x" x="${x}" y="5" width="${p*200}" height="10" fill="${cs[i]}" ${tip(l,(p*100).toFixed(0)+'%')}/>`; x+=p*200; return r;}).join('')}</svg>`; },
  minicols(vals, color='var(--c2)'){ const mx=Math.max(...vals); return `<svg viewBox="0 0 ${vals.length*16} 38" preserveAspectRatio="none" aria-hidden="true">${vals.map((v,i)=>`<rect class="grow-y" style="animation-delay:${i*40}ms" x="${i*16+3}" y="${38-v/mx*36}" width="10" height="${v/mx*36}" rx="2" fill="${i===vals.length-1?'var(--c1)':color}"/>`).join('')}</svg>`; },

  kpi(o){ // {label, value, type, delta, ctx, icon, micro, hero}
    const up=o.delta>=0, good = o.invert ? !up : up;
    return `<div class="card kpi ${o.hero?'hero':''} s3" role="group" aria-label="${esc(o.label)}">
      <div class="lbl">${o.label}</div><div class="ico" aria-hidden="true">${o.icon||'◔'}</div>
      <div class="big num" data-count="${o.value}" data-fmt="${o.type}">${fmt[o.type](o.value)}</div>
      <div class="delta ${good?'up':'down'}">${up?'▲':'▼'} ${Math.abs(o.delta).toFixed(1)}${o.type==='pct'?' pts':'%'} <span>${o.ctx||'vs prior month'}</span></div>
      <div class="micro">${o.micro||''}</div></div>`;
  },

  funnel(stages){
    const W=640,H=260,b=60,gap=(W-40)/stages.length,mx=stages[0][1];
    let bars='',area='',chips='';
    const pts=stages.map(([l,v],i)=>({x:20+i*gap+gap/2,top:H-30-(v/mx)*(H-70),v,l}));
    area=`<path class="fade" d="M${pts.map(p=>`${p.x-b/2},${p.top} ${p.x+b/2},${p.top}`).join(' L')} L${pts.at(-1).x+b/2},${H-30} L${pts[0].x-b/2},${H-30}Z" fill="var(--c3)" opacity=".28"/>`;
    pts.forEach((p,i)=>{
      bars+=`<g ${cat(p.l)}><rect class="grow-y" style="animation-delay:${i*90}ms" x="${p.x-b/2}" y="${p.top}" width="${b}" height="${H-30-p.top}" rx="4" fill="${PAL[Math.min(i,3)]}" ${tip(p.l,fmt.int(p.v)+' records',i?`${(p.v/pts[i-1].v*100).toFixed(1)}% from previous stage`:'Top of funnel')}/>
        <text x="${p.x}" y="${p.top-8}" text-anchor="middle" style="font-weight:800;font-size:15px;fill:var(--ink)">${fmt.int(p.v)}</text>
        <text x="${p.x}" y="${H-12}" text-anchor="middle">${esc(p.l)}</text></g>`;
      if(i) chips+=`<g class="fade"><rect x="${(p.x+pts[i-1].x)/2-24}" y="${H-100}" width="48" height="20" rx="10" fill="#fff" stroke="var(--line)"/><text x="${(p.x+pts[i-1].x)/2}" y="${H-86}" text-anchor="middle" style="font-weight:700;fill:var(--c1)">${(p.v/pts[i-1].v*100).toFixed(1)}%</text></g>`;
    });
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H}" role="img" aria-label="Conversion funnel">${area}${bars}${chips}</svg></div>`;
  },

  donut(items, {centre='', unit=''}={}){
    const tot=items.reduce((a,[,v])=>a+v,0), R=70,r=46,cx=90,cy=90; let a0=-Math.PI/2, arcs='';
    items.forEach(([l,v],i)=>{ const a1=a0+v/tot*Math.PI*2, L=a1-a0>Math.PI?1:0, p=(a,rr)=>`${cx+rr*Math.cos(a)},${cy+rr*Math.sin(a)}`;
      arcs+=`<path ${cat(l)} d="M${p(a0,R)} A${R},${R} 0 ${L} 1 ${p(a1,R)} L${p(a1,r)} A${r},${r} 0 ${L} 0 ${p(a0,r)}Z" fill="${PAL[i%5]}" stroke="#fff" stroke-width="2" ${tip(l,fmt.int(v)+unit,(v/tot*100).toFixed(1)+'% of total')}/>`; a0=a1; });
    return `<div class="viz" style="display:flex;gap:14px;align-items:center;flex-wrap:wrap"><svg viewBox="0 0 180 180" style="max-width:170px" role="img" aria-label="Donut chart"><g class="spin-in">${arcs}</g>
      <text x="90" y="88" text-anchor="middle" style="font-size:26px;font-weight:800;fill:var(--ink)">${fmt.int(tot)}</text><text x="90" y="106" text-anchor="middle">${centre}</text></svg>
      <div class="legend" style="flex-direction:column;flex:1;min-width:120px">${items.map(([l,v],i)=>`<button ${cat(l)} aria-pressed="${S.hl===l}" style="justify-content:space-between;width:100%"><span style="display:flex;gap:6px;align-items:center"><i style="background:${PAL[i%5]}"></i>${esc(l)}</span><b>${fmt.int(v)}</b></button>`).join('')}</div></div>`;
  },

  line(series, months, {id, fmtv=fmt.int, area=true}={}){
    const W=640,H=230,P={l:40,r:10,t:10,b:24}, keys=Object.keys(series).filter(k=>!S.hidden[id+k]);
    const all=keys.flatMap(k=>series[k]); const mn=Math.min(...all)*.98, mx=Math.max(...all)*1.01;
    const x=i=>P.l+i*(W-P.l-P.r)/(months.length-1), y=v=>P.t+(1-(v-mn)/((mx-mn)||1))*(H-P.t-P.b);
    let g=''; for(let t=0;t<=4;t++){const v=mn+(mx-mn)*t/4; g+=`<line x1="${P.l}" x2="${W-P.r}" y1="${y(v)}" y2="${y(v)}" stroke="var(--line)"/><text x="${P.l-6}" y="${y(v)+3}" text-anchor="end">${fmtv(v)}</text>`;}
    months.forEach((m,i)=>{ if(i%2===0||i===months.length-1) g+=`<text x="${x(i)}" y="${H-6}" text-anchor="middle">${m.slice(0,3)}</text>`; });
    const paths=keys.map((k,si)=>{const c=PAL[Object.keys(series).indexOf(k)]; const d=series[k].map((v,i)=>(i?'L':'M')+x(i)+','+y(v)).join('');
      return `${area&&si===0?`<path class="fade" d="${d}L${x(months.length-1)},${H-P.b}L${P.l},${H-P.b}Z" fill="${c}" opacity=".12"/>`:''}<path class="draw" pathLength="2000" d="${d}" fill="none" stroke="${c}" stroke-width="2.5"/>
      ${series[k].map((v,i)=>`<circle cx="${x(i)}" cy="${y(v)}" r="${i===months.length-1?4:2.5}" fill="#fff" stroke="${c}" stroke-width="2" ${tip(k+' · '+months[i],fmtv(v))}/>`).join('')}`;}).join('');
    const leg=Object.keys(series).map((k,i)=>`<button data-legend="${id}|${esc(k)}" aria-pressed="${!S.hidden[id+k]}"><i style="background:${PAL[i]}"></i>${esc(k)}</button>`).join('');
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H}" role="img" aria-label="Line chart over time">${g}${paths}</svg></div><div class="legend">${leg}</div>`;
  },

  sankey(net, f){
    const W=640,H=250, colX=[20,270,520], nw=110, by=c=>net.nodes.filter(n=>n[1]===c);
    const val=n=>Math.max(net.links.filter(l=>l[0]===n).reduce((a,l)=>a+l[2],0), net.links.filter(l=>l[1]===n).reduce((a,l)=>a+l[2],0));
    const pos={}; [0,1,2].forEach(c=>{ const ns=by(c), tot=ns.reduce((a,n)=>a+val(n[0]),0), sc=(H-20-(ns.length-1)*16)/tot; let y=10;
      ns.forEach(([n])=>{ const h=Math.max(28,val(n)*sc); pos[n]={x:colX[c],y,h,o:0,i:0,sc}; y+=h+16; }); });
    const links=net.links.map(([a,b,v])=>{ const A=pos[a],B=pos[b], w=Math.max(2,v*A.sc*.9), y0=A.y+A.o+w/2, y1=B.y+B.i+w/2; A.o+=w; B.i+=w;
      const x0=A.x+nw, x1=B.x, mx=(x0+x1)/2;
      return `<path ${cat(a)} class="fade" d="M${x0},${y0} C${mx},${y0} ${mx},${y1} ${x1},${y1}" fill="none" stroke="var(--c3)" stroke-opacity=".55" stroke-width="${w}" ${tip(a+' → '+b, fmt.int(v*f*10)+' units')}/>`; }).join('');
    const nodes=Object.entries(pos).map(([n,p])=>`<g ${cat(n)}><rect x="${p.x}" y="${p.y}" width="${nw}" height="${p.h}" rx="8" fill="#fff" stroke="var(--c2)" ${tip(n, fmt.int(val(n)*f*10)+' units')}/><text x="${p.x+10}" y="${p.y+p.h/2+4}" style="fill:var(--ink);font-weight:600">${esc(n)}</text></g>`).join('');
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H}" role="img" aria-label="Network flow">${links}${nodes}</svg></div>`;
  },

  dumbbell(rows){ // [label, actual, target]
    const W=560,rh=34,H=rows.length*rh+26,P=110,mx=Math.max(...rows.flatMap(r=>[r[1],r[2]]))*1.15, x=v=>P+v/mx*(W-P-50);
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H}" role="img" aria-label="Actual versus target days">${rows.map(([l,a,t],i)=>{const y=14+i*rh, bad=a>t;
      return `<g ${cat(l)}><rect x="0" y="${y-12}" width="${W}" height="${rh-6}" rx="6" fill="${bad?'#FDF5E6':'transparent'}"/><text x="8" y="${y+4}" style="fill:var(--ink)">${esc(l)}</text>
      <line class="grow-x" x1="${x(Math.min(a,t))}" x2="${x(Math.max(a,t))}" y1="${y}" y2="${y}" stroke="${bad?'var(--warn)':'var(--good)'}" stroke-width="3"/>
      <circle cx="${x(t)}" cy="${y}" r="5" fill="#fff" stroke="var(--ink-3)" stroke-width="2" ${tip(l+' target',t+' days')}/><circle cx="${x(a)}" cy="${y}" r="6" fill="var(--c1)" ${tip(l+' actual',Math.round(a)+' days',bad?`${Math.round(a-t)} days over target`:'Within target')}/>
      <text x="${W-6}" y="${y+4}" text-anchor="end" style="font-weight:700;fill:${bad?'var(--bad)':'var(--good)'}">${Math.round(a)}d</text></g>`;}).join('')}
      <text x="${P}" y="${H-2}">○ target  ● actual</text></svg></div>`;
  },

  lollipop(rows,{fmtv=fmt.pct,max=50}={}){
    const W=560,rh=36,H=rows.length*rh+6,P=100,x=v=>P+v/max*(W-P-60);
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H}" role="img" aria-label="Lollipop chart">${rows.map(([l,v],i)=>{const y=18+i*rh;
      return `<g ${cat(l)}><text x="0" y="${y+4}" style="fill:var(--ink)">${esc(l)}</text><line x1="${P}" x2="${W-60}" y1="${y}" y2="${y}" stroke="var(--c5)" stroke-width="6" stroke-linecap="round"/>
      <line class="grow-x" x1="${P}" x2="${x(v)}" y1="${y}" y2="${y}" stroke="var(--c2)" stroke-width="3"/><circle cx="${x(v)}" cy="${y}" r="7" fill="var(--c1)" ${tip(l,fmtv(v)+' win rate')}/>
      <text x="${W-4}" y="${y+4}" text-anchor="end" style="font-weight:700;fill:var(--ink)">${fmtv(v)}</text></g>`;}).join('')}</svg></div>`;
  },

  chevron(steps){
    return `<div class="chev" role="list">${steps.map(([l,v],i)=>`<div role="listitem" ${cat(l)} style="background:color-mix(in srgb,var(--c1) ${100-i*15}%,var(--c3))" ${tip(l,fmt.int(v),i?(v/steps[i-1][1]*100).toFixed(1)+'% conversion':'Start')}><b class="num">${fmt.int(v)}</b><small>${esc(l)}${i?' · '+(v/steps[i-1][1]*100).toFixed(0)+'%':''}</small></div>`).join('')}</div>`;
  },

  diverging(rows){
    const W=560,H=200,mx=Math.max(...rows.map(r=>Math.abs(r[1]))), z=H/2, bw=(W-20)/rows.length;
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H+20}" role="img" aria-label="Movement columns"><line x1="0" x2="${W}" y1="${z}" y2="${z}" stroke="var(--ink-3)"/>${rows.map(([l,v],i)=>{const h=Math.abs(v)/mx*(z-18), x0=10+i*bw+bw*.2;
      return `<g ${cat(l)}><rect class="${v>=0?'grow-y':'fade'}" x="${x0}" y="${v>=0?z-h:z}" width="${bw*.6}" height="${h}" rx="3" fill="${v>=0?'var(--c1)':'var(--bad)'}" opacity="${v>=0?1:.75}" ${tip(l,(v>0?'+':'')+fmt.int(v))}/>
      <text x="${x0+bw*.3}" y="${v>=0?z-h-5:z+h+12}" text-anchor="middle" style="font-weight:700;fill:var(--ink)">${v>0?'+':''}${fmt.int(v)}</text><text x="${x0+bw*.3}" y="${H+16}" text-anchor="middle">${esc(l)}</text></g>`;}).join('')}</svg></div>`;
  },

  bullets(rows){ // [label, score, benchmark]
    const W=560,rh=30,H=rows.length*rh,P=130,x=v=>P+v/100*(W-P-40);
    return `<div class="viz"><svg viewBox="0 0 ${W} ${H}" role="img" aria-label="Bullet bars">${rows.map(([l,v,b],i)=>{const y=6+i*rh;
      return `<g ${cat(l)}><text x="0" y="${y+12}" style="fill:var(--ink)">${esc(l)}</text><rect x="${P}" y="${y}" width="${W-P-40}" height="16" rx="3" fill="var(--c5)"/>
      <rect class="grow-x" x="${P}" y="${y+3}" width="${x(v)-P}" height="10" rx="2" fill="${v>=b?'var(--c1)':'var(--c3)'}" ${tip(l,'Score '+v.toFixed(0),'Benchmark '+b)}/><line x1="${x(b)}" x2="${x(b)}" y1="${y-2}" y2="${y+18}" stroke="var(--ink)" stroke-width="2"/>
      <text x="${W-4}" y="${y+12}" text-anchor="end" style="font-weight:700;fill:${v>=b?'var(--good)':'var(--warn)'}">${v.toFixed(0)}</text></g>`;}).join('')}</svg></div>`;
  },

  table(cols, rows){ return `<div style="overflow:auto"><table><thead><tr>${cols.map(c=>`<th>${esc(c)}</th>`).join('')}</tr></thead><tbody>${rows.map(r=>`<tr ${cat(r[0])}>${r.map(c=>typeof c==='number'?`<td class="n">${fmt.int(c)}</td>`:`<td>${c}</td>`).join('')}</tr>`).join('')}</tbody></table></div>`; },

  brief(items){ const col={bad:'var(--bad)',warn:'var(--warn)',good:'var(--good)',info:'var(--c2)'}, lab={bad:'Urgent',warn:'Watch',good:'On track',info:'Action'};
    return `<div class="brief"><ol>${items.map(([s,t,m,o],i)=>`<li ${cat(t)}><span class="no" style="background:${col[s]}">${i+1}</span><div style="flex:1"><div class="t">${esc(t)}</div><div class="m">${esc(m)}</div><div class="m">${esc(o)}</div></div><span class="pill ${s}">${lab[s]}</span></li>`).join('')}</ol></div>`; }
};

/* Card wrapper: title, subtitle, optional pill tabs (chart/table or metric switch), expand button. */
function card(id, span, title, sub, body, {tabs=null, dark=false}={}){
  const t = tabs ? `<div class="tabs" role="group" aria-label="${esc(title)} view">${tabs.map((x,i)=>`<button data-tog="${id}|${i}" aria-pressed="${(S.tog[id]||0)===i}">${x}</button>`).join('')}</div>` : '';
  return `<section class="card ${span} ${dark?'dark':''}" id="c-${id}" aria-labelledby="t-${id}"><div class="chead"><div class="grow"><h3 id="t-${id}">${title}</h3><div class="sub">${sub}</div></div>${t}
    <button class="icon-btn" data-expand="${id}" aria-label="Focus mode: ${esc(title)}" title="Focus mode"><svg width="14" height="14" viewBox="0 0 24 24"><path d="M4 9V4h5M20 9V4h-5M4 15v5h5M20 15v5h-5" fill="none" stroke="currentColor" stroke-width="2"/></svg></button></div>${body}</section>`;
}
const view = id => S.tog[id]||0;

/* ================= 8. PAGES (each its own layout) ================= */
const PAGES = {
  sales(c){ const d=DATA.sales, k=d.kpi, m=Math.max(2,c.mi+1);  // ≥2 points so lines can draw
    const win=c.r(k.winRate,2.5,'w'), won=c.n(k.won), lost=c.n(k.lost), cyc=c.r(k.cycle,6,'c');
    const funnel=d.funnel.map(([l,v])=>[l,Math.max(1,c.n(v))]);
    const loss=d.lossReasons.map(([l,v],i)=>[l,Math.max(1,c.n(v*(1+c.j('l'+i)*.3)))]);
    return [
      V.kpi({label:'Closed-deal win rate',value:win,type:'pct',delta:c.j('dw')*2.4,hero:true,icon:'⚑',micro:V.dots(14,Math.round(win/100*14),'#fff')}),
      V.kpi({label:'Won deals',value:Math.max(1,won),type:'int',delta:c.j('dwo')*12,icon:'✓',micro:V.progress(Math.min(100,won/Math.max(1,won+lost)*100))}),
      V.kpi({label:'Lost deals',value:lost,type:'int',delta:c.j('dl')*10,invert:true,icon:'✕',micro:V.minicols(k.winTrend.slice(0,m).map(v=>100-v))}),
      V.kpi({label:'Median won cycle',value:cyc,type:'d',delta:c.j('dc')*5,invert:true,icon:'◷',micro:V.spark(k.cycleTrend.slice(0,m))}),
      card('funnel','s8','Lead conversion journey',`Qualified leads → won · ${S.period} to ${S.month}`, view('funnel')?V.table(['Stage','Count'],funnel):V.funnel(funnel),{tabs:['Chart','Table']}),
      card('loss','s4','Why we lose','Lost deals by primary reason', view('loss')?V.table(['Reason','Deals'],loss):V.donut(loss,{centre:'lost deals'}),{tabs:['Chart','Table']}),
      card('time','s6','Where time accumulates','Days in stage — actual vs target', V.dumbbell(d.stageTime.map(([l,a,t],i)=>[l,c.r(a,4,'t'+i),t]))),
      card('seg','s6','Win rate by customer segment',`Closed deals · ${S.month}`, V.lollipop(d.segWin.filter(([s])=>S.segment.includes(s)).map(([l,v],i)=>[l,c.r(v,2,'s'+i)])))
    ];
  },
  people(c){ const d=DATA.people, k=d.kpi, m=Math.max(2,c.mi+1), scale=c.s;
    const months=DATA.months.slice(0,m), metric=view('wf')?'FTE':'Headcount';
    const wf={[metric]:d.workforce[metric].slice(0,m).map(v=>Math.round(v*scale)), Hires:d.workforce.Hires.slice(0,m).map(v=>Math.round(v*scale*10))};
    return [
      V.kpi({label:'Headcount',value:Math.round(k.hcTrend[c.mi]*scale),type:'int',delta:c.j('h')*1.5+0.8,hero:true,icon:'👥',micro:V.spark(k.hcTrend.slice(0,m),{area:true,color:'#fff'})}),
      V.kpi({label:'Voluntary turnover',value:c.r(k.turnover,.6,'t'),type:'pct',delta:c.j('dt')*.5,invert:true,icon:'↺',micro:V.minicols([5.8,5.6,5.5,5.4,5.3,5.1])}),
      V.kpi({label:'Engagement score',value:c.r(k.engagement,1.5,'e'),type:'pct',delta:1.4+c.j('de')*.3,icon:'♥',micro:V.progress(k.engagement)}),
      V.kpi({label:'Open roles',value:Math.max(1,Math.round(k.openRoles*scale)),type:'int',delta:c.j('do')*8,invert:true,icon:'◎',micro:V.dots(12,Math.round(12*scale))}),
      card('wf','s8','Our workforce, over time',`${metric} and monthly hires (×10 scale) · ${months[0]}–${S.month}`, V.line(wf,months,{id:'wf'}),{tabs:['Headcount','FTE']}),
      card('org','s4','One organization','Headcount by function', view('org')?V.table(['Function','People'],d.org.map(([l,v])=>[l,Math.round(v*scale)])):V.donut(d.org.map(([l,v])=>[l,Math.round(v*scale)]),{centre:'people'}),{tabs:['Chart','Table']}),
      card('hire','s8','From hello to hired',`Hiring funnel · ${S.period}`, V.chevron(d.hiring.map(([l,v])=>[l,Math.max(1,c.n(v/DATA.periodMult.YTD))]))),
      card('pbrief','s4','The people brief','What needs attention this month', V.brief(d.brief)),
      card('move','s6','Headcount movement',`${S.period} · ${S.month}`, V.diverging(d.movement.map(([l,v])=>[l,Math.round(c.n(Math.abs(v))*Math.sign(v)/DATA.periodMult.YTD*3)||Math.sign(v)]))),
      card('drv','s6','Experience drivers','Score vs benchmark (|)', V.bullets(d.drivers.map(([l,v,b],i)=>[l,Math.min(100,c.r(v,3,'d'+i)),b])))
    ];
  },
  supply(c){ const d=DATA.supply, k=d.kpi, m=Math.max(2,c.mi+1), scale=c.s;
    const months=DATA.months.slice(0,m);
    return [
      V.kpi({label:'Customer OTIF',value:c.r(k.otif,1.2,'o'),type:'pct',delta:.4+c.j('do')*.6,hero:true,icon:'◎',micro:V.spark(k.otifTrend.slice(0,m),{area:true,color:'#fff'})}),
      V.kpi({label:'Inventory value',value:k.inventory*scale,type:'m',delta:c.j('di')*3,invert:true,icon:'▣',micro:V.stack(k.invMix)}),
      V.kpi({label:'Orders at risk',value:Math.max(0,Math.round(k.atRisk*scale*2)),type:'int',delta:c.j('dr')*20,invert:true,icon:'⚠',micro:V.minicols([1,3,2,4,2,2])}),
      V.kpi({label:'Supply chain cost',value:k.cost*scale*DATA.periodMult[S.period],type:'m',delta:c.j('dc')*2,invert:true,icon:'$',micro:V.progress(72)}),
      card('net','s8','The network, connected','Suppliers → plants & DCs → customers', view('net')?V.table(['From','To','Units'],d.network.links.map(([a,b,v])=>[a,b,Math.round(v*c.f*10)])):V.sankey(d.network,c.f),{tabs:['Flow','Table']}),
      card('obrief','s4',"Today's operating brief",'Prioritised actions with owners', V.brief(d.brief),{dark:true}),
      card('prom','s5','Customer promise over time','OTIF delivered vs promised (%)', V.line({Delivered:d.promise.Delivered.slice(0,m),Promised:d.promise.Promised.slice(0,m)},months,{id:'prom',fmtv:v=>v.toFixed(0)+'%',area:true})),
      card('exp','s3','Inventory exposure','Value by health ($m)', V.donut(d.exposure.map(([l,v])=>[l,Math.round(v*scale*10)/10]),{centre:'$m'})),
      card('exc','s4','Priority exceptions','Open issues by status', V.table(['Item','Channel','Node','Status'],d.exceptions.map(([a,b,n,s,t])=>[a,b,n,`<span class="pill ${t}">${s}</span>`])))
    ];
  }
};

/* ================= 9. RENDER + INTERACTION ================= */
const THEME = {sales:'sales',people:'people',supply:'supply'};
function slicerHTML(){
  const multi=(dim,label)=>`<div class="slicer"><span id="l-${dim}">${label}</span><button class="dd-btn" data-dd="${dim}" aria-haspopup="true" aria-expanded="false" aria-labelledby="l-${dim}">${S[dim].length===Object.keys(DATA.dims[dim]).length?'All':S[dim].length===1?S[dim][0]:S[dim].length+' selected'}</button>
    <div class="dd" id="dd-${dim}" role="group" aria-label="${label}">${Object.keys(DATA.dims[dim]).map(o=>`<label><input type="checkbox" data-dim="${dim}" value="${esc(o)}" ${S[dim].includes(o)?'checked':''}>${esc(o)}</label>`).join('')}</div></div>`;
  return multi('region','Region')+multi('segment','Segment')+
    `<div class="slicer"><span id="l-month">Reporting month</span><button class="dd-btn" data-dd="month" aria-haspopup="true" aria-expanded="false" aria-labelledby="l-month">${S.month}</button>
    <div class="dd" id="dd-month" role="radiogroup" aria-label="Reporting month" style="max-height:260px;overflow:auto">${DATA.months.map(mo=>`<label><input type="radio" name="month" value="${mo}" ${mo===S.month?'checked':''}>${mo}</label>`).join('')}</div></div>
    <div class="slicer"><span>Period</span><div class="seg" role="group" aria-label="Period">${['MTD','QTD','YTD'].map(p=>`<button data-period="${p}" aria-pressed="${S.period===p}">${p}</button>`).join('')}</div></div>
    <button class="btn primary" id="reset">Reset filters</button>`;
}
function render(){
  const P=DATA[S.page]; document.body.dataset.theme=THEME[S.page];
  $('#h1').innerHTML=esc(P.title)+(DATA.sample?'<span class="sample" title="Illustrative sample data">Sample data</span>':'');
  $('#subtitle').textContent=P.subtitle;
  $('#slicers').innerHTML=slicerHTML();
  $('#grid').innerHTML=PAGES[S.page](ctx()).join('');
  document.querySelectorAll('.rail [data-page]').forEach(b=>b.dataset.page===S.page?b.setAttribute('aria-current','page'):b.removeAttribute('aria-current'));
  $('#report').classList.toggle('has-hl',!!S.hl);
  countUp();
}
function countUp(){
  if (matchMedia('(prefers-reduced-motion:reduce)').matches) return;
  document.querySelectorAll('[data-count]').forEach(el=>{ const to=+el.dataset.count, f=fmt[el.dataset.fmt], t0=performance.now();
    const step=t=>{ const p=Math.min(1,(t-t0)/900), e=1-Math.pow(1-p,3); el.textContent=f(to*e); if(p<1) requestAnimationFrame(step); }; requestAnimationFrame(step); });
}
function closeDDs(){ document.querySelectorAll('.dd.open').forEach(d=>{d.classList.remove('open'); const b=document.querySelector(`[data-dd="${d.id.slice(3)}"]`); b&&b.setAttribute('aria-expanded','false');}); }
let focused=null;
function exitFocus(){ if(!focused) return; focused.classList.remove('focus'); $('#scrim').classList.remove('show'); const b=focused.querySelector('[data-expand]'); focused=null; b&&b.focus(); }

document.addEventListener('click', e=>{
  const t=e.target;
  const pg=t.closest('[data-page]'); if(pg){ S.page=pg.dataset.page; S.hl=null; exitFocus(); render(); return; }
  const dd=t.closest('[data-dd]'); if(dd){ const box=$('#dd-'+dd.dataset.dd), open=!box.classList.contains('open'); closeDDs(); if(open){box.classList.add('open'); dd.setAttribute('aria-expanded','true'); box.querySelector('input').focus();} return; }
  if(t.closest('.dd')) return;
  closeDDs();
  const per=t.closest('[data-period]'); if(per){ S.period=per.dataset.period; render(); return; }
  if(t.closest('#reset')){ Object.assign(S,{month:DATA.defaultMonth,period:'MTD',region:Object.keys(DATA.dims.region),segment:Object.keys(DATA.dims.segment),hl:null,hidden:{}}); render(); return; }
  const tg=t.closest('[data-tog]'); if(tg){ const [id,i]=tg.dataset.tog.split('|'); S.tog[id]=+i; render(); return; }
  const lg=t.closest('[data-legend]'); if(lg){ const [id,k]=lg.dataset.legend.split('|'); if(!S.hidden[id+k] && lg.parentElement.querySelectorAll('[aria-pressed="true"]').length===1) return; /* keep >=1 series visible */ S.hidden[id+k]=!S.hidden[id+k]; render(); return; }
  const ex=t.closest('[data-expand]'); if(ex){ const c=$('#c-'+ex.dataset.expand); if(focused===c){exitFocus();return;} exitFocus(); focused=c; c.classList.add('focus'); $('#scrim').classList.add('show'); ex.focus(); return; }
  if(t.closest('#scrim')){ exitFocus(); return; }
  if(t.closest('#defsBtn')){ $('#defsBody').innerHTML=`<p>${DATA.sample?'<b>All figures are illustrative sample data.</b> ':''}Swap the <code>DATA</code> object to use real figures.</p><table>${DATA.definitions.map(([a,b])=>`<tr><td><b>${a}</b></td><td>${b}</td></tr>`).join('')}</table>`; $('#defs').showModal(); return; }
  const cf=t.closest('[data-cat]'); if(cf){ S.hl = S.hl===cf.dataset.cat ? null : cf.dataset.cat; render(); return; }
  if(t.closest('.grid') && !t.closest('button') && S.hl){ S.hl=null; render(); }   // click empty card space clears cross-filter
});
document.addEventListener('change', e=>{
  const t=e.target;
  if(t.dataset.dim){ const dim=t.dataset.dim, sel=[...document.querySelectorAll(`input[data-dim="${dim}"]:checked`)].map(x=>x.value); if(!sel.length){t.checked=true;return;} S[dim]=sel; S.hl=null; const keep=dim; render(); $('#dd-'+keep).classList.add('open'); document.querySelector(`[data-dd="${keep}"]`).setAttribute('aria-expanded','true'); }
  if(t.name==='month'){ S.month=t.value; render(); }
});
document.addEventListener('keydown', e=>{
  if(e.key==='Escape'){ closeDDs(); exitFocus(); if(S.hl){S.hl=null; render();} }
  if((e.key==='Enter'||e.key===' ') && e.target.matches('[data-cat]')){ e.preventDefault(); e.target.click(); }
});
/* Tooltips (mouse + keyboard focus) */
const tt=$('#tt');
function showTT(el,x,y){ const [h,...l]=el.dataset.tip.split('\n'); tt.innerHTML=`<b>${esc(h)}</b>${l.map(esc).join('<br>')}`; tt.classList.add('show');
  const r=tt.getBoundingClientRect(); tt.style.left=Math.min(innerWidth-r.width-8,x+14)+'px'; tt.style.top=Math.max(8,y-r.height-10)+'px'; }
document.addEventListener('mousemove', e=>{ const el=e.target.closest('[data-tip]'); el?showTT(el,e.clientX,e.clientY):tt.classList.remove('show'); });
document.addEventListener('focusin', e=>{ const el=e.target.closest('[data-cat]'); const tipEl=el&&(el.matches('[data-tip]')?el:el.querySelector('[data-tip]')); if(tipEl){const r=el.getBoundingClientRect(); showTT(tipEl,r.left+r.width/2,r.top);} else tt.classList.remove('show'); });

render();
</script>
</body>
</html>
```

## 8. Pre-delivery checklist

- [ ] Title phrase + subtitle per page; "Sample data" chip present unless real data supplied
- [ ] Every slicer changes every visual on the page; Reset restores defaults
- [ ] Cross-filter highlight/dim works from donut, bars, legend, nodes and table rows; Esc clears
- [ ] Tooltips on hover and keyboard focus; KPI count-up; entry animations stop under reduced motion
- [ ] Chart ⇄ Table and metric toggles work; focus mode opens/closes with focus return
- [ ] Reflows to 2 and 1 columns without overflow; no external requests (single file in `output/`)
- [ ] Status colours used only for status; one theme hue otherwise; tabular numerals
