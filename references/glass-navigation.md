# Glass Navigation System — floating frosted-glass sidebar

A production-ready, single-file **glass sidebar navigation**: a floating vertical panel of dark
frosted glass over a dark, wavy monochrome backdrop, with a vivid full-bleed active bar, count
badges, a profile card, search, grouped sections and an animated collapse to an icon rail.
Triggers: "glass sidebar", "glass navigation", "glass nav", "frosted sidebar", "SideBar UI",
"collapsible sidebar like macOS". It is the default left nav for any **Liquid** app/dashboard and
can replace the solid rail of a **Visual PowerBi Report** on request (see §7).

> Reference register: a dark streaming-app sidebar mock-up (frosted panel, red active bar). Take
> the *register* — never copy a brand's logo, name or icons. Icons in the template are original
> inline SVG.

## 1. Anatomy (top → bottom)

| Zone | Spec |
|---|---|
| Panel | ~300px wide, `border-radius: 24px`, floats with 24px gutter, height ≤ 760px |
| Window dots | macOS traffic lights — 11px circles `#FF5F57` / `#FEBC2E` / `#28C840`, 7px gap, top-left |
| Header | Bold 20px title ("SideBar UI") · bell icon with red count badge · circular white chevron collapse button straddling the panel edge, with a **white pill label "Collapse"** (red text) revealed on hover/focus |
| Profile card | Glass card (hairline border, 16px radius): 44px circular avatar · bold name · muted 11px "⇄ Switch account" · right chevron |
| Search | Small magnifier icon + pill glass input, placeholder `SEARCH...` uppercase, letter-spacing .14em |
| Section labels | `MENU`, `DISCOVERY`, `GENERAL` — 9.5px uppercase, letter-spacing .16em, muted grey; groups separated by 1px hairline dividers |
| Nav items | 42px rows: 18px icon + 14px label, light grey text. Hover = faint glass highlight. **Active** = solid accent bar (`#E50914`) that **bleeds ~8px past both panel edges**, white text + icon, soft coloured glow. Count badge = 20px accent circle ("7") right-aligned |

## 2. Material tokens

```
--glass-fill: rgba(40,40,44,.55)               /* dark frosted fill */
backdrop-filter: blur(20–30px) saturate(140%)  /* 24px default */
border: 1px solid rgba(255,255,255,.12)        /* light inner edge */
background-image: linear-gradient(180deg, rgba(255,255,255,.10), transparent 22%)  /* top highlight */
box-shadow: 0 30px 60px -12px rgba(0,0,0,.65), 0 12px 24px -8px rgba(0,0,0,.45),
            inset 0 1px 0 rgba(255,255,255,.14)  /* deep drop + inner top light */
text: #ECECEE / #A9A9B0 / #7C7C84  (primary / item / label)
```
**Backdrop:** glass needs texture behind it — the template builds a dark, wavy monochrome
field from layered `repeating-radial-gradient`s (no image asset). Swap in a photo / 3D render if
the user has one.

## 3. Accent presets & light-glass variant

| Preset | `--accent` | Use |
|---|---|---|
| Red (default) | `#E50914` | Media / entertainment register |
| Microsoft blue | `#0F6CBD` | Productivity, Microsoft-flavoured UIs, Power BI reports |
| Purple | `#7B3FE4` | People / HR, creative tools |

Set via `<body data-accent="red|blue|purple">`. **Light glass:** `data-theme="light"` switches
to `rgba(255,255,255,.55)` fill, dark text, white edge and a pale backdrop.

## 4. Behaviour

- **Collapse → rail:** width 300 → 80px with `cubic-bezier(.22,1,.36,1)` .42s; labels fade,
  chevron rotates 180°, pill text flips to "Expand", section labels become `•••`, badges dock to
  the icon corner. State persists in `localStorage`; **Ctrl/⌘ + B** toggles.
- **Rail tooltips:** in rail mode hovering *or focusing* an item shows a white tooltip to the right.
- **Search** filters nav items live.
- **Mobile (≤760px):** becomes an off-canvas drawer opened by a hamburger, with scrim; Esc / scrim
  click closes and returns focus to the trigger.

## 5. Accessibility checklist

- `<aside aria-label="Primary">`, groups `role="group"` + `aria-labelledby` their section label.
- Active link carries `aria-current="page"`; collapse button carries `aria-expanded` + `aria-controls`
  and a changing `aria-label`.
- Arrow ↑/↓, Home/End move focus between items; visible white focus ring + accent halo.
- Badge counts have text equivalents (`aria-label="7 new"`, bell "6 unread").
- `prefers-reduced-motion` → transitions ≈ 0; `prefers-reduced-transparency` and no
  `backdrop-filter` support → solid `#26262a` panel. Text contrast on glass ≥ 4.5:1.

## 6. Template — self-contained HTML/CSS/JS (no external dependencies)

Copy, then adapt the title, items, icons and accent. Deliver via the html skill into `output/`.

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Glass Navigation — SideBar UI</title>
<style>
/* ===== 1. Tokens ===== */
:root{
  --accent:#E50914;            /* red default — swap via [data-accent] */
  --accent-ink:#fff;
  --glass-fill:rgba(40,40,44,.55);
  --glass-fill-2:rgba(255,255,255,.06);  /* cards / inputs on glass */
  --glass-hover:rgba(255,255,255,.08);
  --glass-border:rgba(255,255,255,.12);
  --glass-hair:rgba(255,255,255,.08);
  --glass-blur:24px;
  --text:#ECECEE; --text-2:#A9A9B0; --text-3:#7C7C84;
  --shadow:0 30px 60px -12px rgba(0,0,0,.65),0 12px 24px -8px rgba(0,0,0,.45);
  --w-open:300px; --w-rail:80px; --radius:24px;
  --ease:cubic-bezier(.22,1,.36,1); --dur:.42s;
  --font:"Segoe UI Variable","Segoe UI",Inter,-apple-system,system-ui,sans-serif;
}
[data-accent="blue"]  {--accent:#0F6CBD}
[data-accent="purple"]{--accent:#7B3FE4}
[data-theme="light"]{
  --glass-fill:rgba(255,255,255,.55); --glass-fill-2:rgba(255,255,255,.55);
  --glass-hover:rgba(0,0,0,.05); --glass-border:rgba(255,255,255,.7); --glass-hair:rgba(0,0,0,.08);
  --text:#1B1B1F; --text-2:#55555C; --text-3:#85858C;
  --shadow:0 30px 60px -16px rgba(30,30,60,.35),0 8px 18px -8px rgba(30,30,60,.2);
}
*{box-sizing:border-box}
html,body{height:100%;margin:0}
body{font-family:var(--font);color:var(--text);background:#0c0c0e;overflow:hidden}

/* ===== 2. Dark wavy monochrome background (pure CSS, no assets) ===== */
.bg{position:fixed;inset:0;z-index:0;
  background:
    repeating-radial-gradient(ellipse 120% 80% at 110% 30%,#2a2a2e 0 18px,#141416 30px 46px,#2a2a2e 60px),
    #111;
  filter:blur(1.5px) contrast(1.1)}
.bg::after{content:"";position:absolute;inset:0;
  background:radial-gradient(ellipse 70% 90% at 20% 60%,rgba(0,0,0,.0),rgba(0,0,0,.65)),
             repeating-radial-gradient(ellipse 90% 140% at -10% 110%,rgba(255,255,255,.06) 0 10px,transparent 22px 40px)}
[data-theme="light"] .bg{background:repeating-radial-gradient(ellipse 120% 80% at 110% 30%,#dfe3ea 0 18px,#c8ced9 30px 46px,#dfe3ea 60px);filter:blur(1.5px)}
[data-theme="light"] .bg::after{background:radial-gradient(circle at 20% 40%,rgba(160,180,255,.45),transparent 50%)}

/* ===== 3. Panel ===== */
.shell{position:relative;z-index:1;display:flex;height:100%;padding:24px;gap:24px}
.sidebar{position:relative;flex:0 0 var(--w-open);width:var(--w-open);height:calc(100vh - 48px);max-height:760px;
  display:flex;flex-direction:column;padding:18px 0 14px;border-radius:var(--radius);
  background:linear-gradient(180deg,rgba(255,255,255,.10),rgba(255,255,255,0) 22%),var(--glass-fill);
  -webkit-backdrop-filter:blur(var(--glass-blur)) saturate(140%);backdrop-filter:blur(var(--glass-blur)) saturate(140%);
  border:1px solid var(--glass-border);box-shadow:var(--shadow),inset 0 1px 0 rgba(255,255,255,.14);
  transition:width var(--dur) var(--ease),flex-basis var(--dur) var(--ease)}
@supports not ((backdrop-filter:blur(1px)) or (-webkit-backdrop-filter:blur(1px))){.sidebar{background:#2a2a2e}}
@media (prefers-reduced-transparency:reduce){.sidebar{backdrop-filter:none;-webkit-backdrop-filter:none;background:#26262a}}
.sidebar[data-collapsed="true"]{flex-basis:var(--w-rail);width:var(--w-rail)}

.dots{display:flex;gap:7px;padding:0 20px 14px}
.dots i{width:11px;height:11px;border-radius:50%;display:block}
.dots i:nth-child(1){background:#FF5F57}.dots i:nth-child(2){background:#FEBC2E}.dots i:nth-child(3){background:#28C840}

.head{display:flex;align-items:center;gap:10px;padding:0 20px 14px;position:relative}
.title{font-weight:700;font-size:20px;letter-spacing:-.01em;white-space:nowrap;margin-right:auto}
.icon-btn{position:relative;display:grid;place-items:center;width:30px;height:30px;border-radius:50%;border:0;background:transparent;color:var(--text);cursor:pointer}
.icon-btn:hover{background:var(--glass-hover)}
.badge{position:absolute;top:-2px;right:-2px;min-width:16px;height:16px;padding:0 4px;border-radius:8px;background:var(--accent);color:#fff;font:700 10px/16px var(--font);text-align:center}
.collapse-btn{position:absolute;right:-14px;top:-2px;display:flex;align-items:center;gap:8px;border:0;background:none;padding:0;cursor:pointer;z-index:3}
.collapse-btn .chev{width:28px;height:28px;border-radius:50%;display:grid;place-items:center;background:#fff;color:#111;box-shadow:0 4px 12px rgba(0,0,0,.4);transition:transform var(--dur) var(--ease)}
.collapse-btn .pill{position:absolute;left:36px;white-space:nowrap;background:#fff;color:var(--accent);font:700 12px var(--font);padding:4px 12px;border-radius:999px;box-shadow:0 4px 12px rgba(0,0,0,.35);opacity:0;transform:translateX(-6px);transition:.2s var(--ease);pointer-events:none}
.collapse-btn:hover .pill,.collapse-btn:focus-visible .pill{opacity:1;transform:none}
.sidebar[data-collapsed="true"] .collapse-btn .chev{transform:rotate(180deg)}

.profile{margin:0 14px 12px;display:flex;align-items:center;gap:12px;padding:10px;border-radius:16px;background:var(--glass-fill-2);border:1px solid var(--glass-hair);color:inherit;text-decoration:none}
.profile:hover{background:var(--glass-hover)}
.avatar{flex:0 0 44px;height:44px;border-radius:50%;background:radial-gradient(circle at 35% 35%,#9a9aa2,#3b3b40);display:grid;place-items:center;font-weight:700;border:2px solid rgba(255,255,255,.18)}
.who{min-width:0;margin-right:auto}.who b{display:block;font-size:15px}.who small{display:flex;gap:5px;align-items:center;color:var(--text-3);font-size:11px}

.search{display:flex;align-items:center;gap:10px;padding:0 20px 8px}
.search input{flex:1;min-width:0;height:32px;border-radius:999px;border:1px solid var(--glass-hair);background:var(--glass-fill-2);color:var(--text);padding:0 14px;font:600 11px var(--font);letter-spacing:.14em;text-transform:uppercase}
.search input::placeholder{color:var(--text-3)}

nav{flex:1;overflow-y:auto;scrollbar-width:none;margin:0 -8px;padding:0 8px} /* gutter lets the active bar bleed 8px past the panel edge */
.group{padding:4px 0 6px;border-top:1px solid var(--glass-hair)}
.group:first-child{border-top:0}
.label{font:700 9.5px var(--font);letter-spacing:.16em;text-transform:uppercase;color:var(--text-3);padding:10px 20px 4px;white-space:nowrap}
.item{position:relative;display:flex;align-items:center;gap:16px;height:42px;padding:0 24px;color:var(--text-2);text-decoration:none;font-size:14px;font-weight:500;white-space:nowrap;outline:none;transition:background .2s,color .2s}
.item svg{flex:0 0 18px}
.item:hover{background:var(--glass-hover);color:var(--text)}
.item[aria-current="page"]{background:var(--accent);color:var(--accent-ink);margin:0 -8px;padding:0 32px;box-shadow:0 8px 20px -6px color-mix(in srgb,var(--accent) 70%,transparent)}
.item .count{margin-left:auto;min-width:20px;height:20px;border-radius:10px;background:var(--accent);color:#fff;font:700 11px/20px var(--font);text-align:center}
.item:focus-visible,.icon-btn:focus-visible,.collapse-btn:focus-visible .chev,.profile:focus-visible,.search input:focus-visible{outline:2px solid #fff;outline-offset:2px;box-shadow:0 0 0 4px color-mix(in srgb,var(--accent) 60%,transparent)}

/* collapsed rail */
.sidebar .txt{transition:opacity .2s var(--ease)}
.sidebar[data-collapsed="true"] .txt{opacity:0;pointer-events:none;width:0;overflow:hidden}
.sidebar[data-collapsed="true"] .head{justify-content:center;padding:0 0 14px}
.sidebar[data-collapsed="true"] .dots{justify-content:center;padding:0 0 14px}
.sidebar[data-collapsed="true"] .profile{justify-content:center;margin:0 12px 12px;padding:6px}
.sidebar[data-collapsed="true"] .search{justify-content:center;padding:0 0 8px}
.sidebar[data-collapsed="true"] .item{justify-content:center;padding:0;gap:0}
.sidebar[data-collapsed="true"] .item[aria-current="page"]{margin:0 -6px;padding:0}
.sidebar[data-collapsed="true"] .label{text-align:center;padding:10px 0 4px;font-size:0}
.sidebar[data-collapsed="true"] .label::before{content:"•••";font-size:9px;letter-spacing:2px}
.sidebar[data-collapsed="true"] .item .count{position:absolute;top:6px;right:16px;min-width:16px;height:16px;font-size:9px;line-height:16px}
.sidebar[data-collapsed="true"] .collapse-btn{right:-14px}
/* tooltips in rail mode */
.tip{position:fixed;z-index:50;pointer-events:none;background:#fff;color:#111;font:600 12px var(--font);padding:6px 10px;border-radius:8px;box-shadow:0 8px 20px rgba(0,0,0,.35);opacity:0;transform:translateX(-4px);transition:opacity .15s,transform .15s}
.tip.show{opacity:1;transform:none}

/* demo content */
main{flex:1;display:grid;place-items:center;color:var(--text-2);font-size:14px}
.demo-ctrls{display:flex;gap:8px;flex-wrap:wrap;justify-content:center}
.demo-ctrls button{border:1px solid var(--glass-border);background:var(--glass-fill);backdrop-filter:blur(12px);color:var(--text);border-radius:999px;padding:6px 14px;cursor:pointer;font:600 12px var(--font)}
.menu-btn{display:none}

/* ===== 4. Mobile: off-canvas drawer ===== */
@media (max-width:760px){
  .shell{padding:12px}
  .sidebar{position:fixed;left:12px;top:12px;height:calc(100% - 24px);z-index:40;transform:translateX(calc(-100% - 24px));transition:transform var(--dur) var(--ease)}
  .sidebar.open{transform:none}
  .sidebar[data-collapsed="true"]{width:var(--w-open);flex-basis:var(--w-open)}
  .collapse-btn{display:none}
  .scrim{position:fixed;inset:0;background:rgba(0,0,0,.45);opacity:0;pointer-events:none;transition:opacity .3s;z-index:30}
  .scrim.show{opacity:1;pointer-events:auto}
  .menu-btn{display:inline-flex;position:fixed;top:16px;left:16px;z-index:20}
}
@media (prefers-reduced-motion:reduce){*,*::before,*::after{transition-duration:.01ms!important;animation-duration:.01ms!important}}
</style>
</head>
<body data-accent="red">
<div class="bg" aria-hidden="true"></div>
<!-- Inline SVG icon sprite (original, stroke icons — no icon font / CDN) -->
<svg width="0" height="0" style="position:absolute" aria-hidden="true">
  <symbol id="i-home" viewBox="0 0 24 24"><path d="M3 11 12 3l9 8v10h-6v-6H9v6H3z" fill="currentColor"/></symbol>
  <symbol id="i-play" viewBox="0 0 24 24"><circle cx="12" cy="12" r="9" fill="none" stroke="currentColor" stroke-width="2"/><path d="m10 8 6 4-6 4z" fill="currentColor"/></symbol>
  <symbol id="i-plus" viewBox="0 0 24 24"><circle cx="12" cy="12" r="9" fill="none" stroke="currentColor" stroke-width="2"/><path d="M12 8v8M8 12h8" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-tv" viewBox="0 0 24 24"><rect x="3" y="6" width="18" height="13" rx="2" fill="none" stroke="currentColor" stroke-width="2"/><path d="m8 2 4 4 4-4" fill="none" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-cal" viewBox="0 0 24 24"><rect x="3" y="5" width="18" height="16" rx="2" fill="none" stroke="currentColor" stroke-width="2"/><path d="M3 10h18M8 3v4M16 3v4" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-game" viewBox="0 0 24 24"><path d="M6 8h12a4 4 0 0 1 4 4v1a4 4 0 0 1-7 2.6L14 15h-4l-1 .6A4 4 0 0 1 2 13v-1a4 4 0 0 1 4-4z" fill="none" stroke="currentColor" stroke-width="2"/><path d="M7 11v3M5.5 12.5h3" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-copy" viewBox="0 0 24 24"><rect x="8" y="8" width="13" height="13" rx="2" fill="none" stroke="currentColor" stroke-width="2"/><path d="M16 8V5a2 2 0 0 0-2-2H5a2 2 0 0 0-2 2v9a2 2 0 0 0 2 2h3" fill="none" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-star" viewBox="0 0 24 24"><circle cx="12" cy="12" r="9" fill="none" stroke="currentColor" stroke-width="2"/><path d="m12 7 1.5 3.2 3.5.4-2.6 2.4.7 3.5L12 14.8 8.9 16.5l.7-3.5L7 10.6l3.5-.4z" fill="currentColor"/></symbol>
  <symbol id="i-gear" viewBox="0 0 24 24"><circle cx="12" cy="12" r="3" fill="none" stroke="currentColor" stroke-width="2"/><path d="M12 2v3M12 19v3M4.2 4.2l2.1 2.1M17.7 17.7l2.1 2.1M2 12h3M19 12h3M4.2 19.8l2.1-2.1M17.7 6.3l2.1-2.1" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-out" viewBox="0 0 24 24"><path d="M14 4h5a1 1 0 0 1 1 1v14a1 1 0 0 1-1 1h-5M3 12h11M10 8l4 4-4 4" fill="none" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-bell" viewBox="0 0 24 24"><path d="M6 16V11a6 6 0 0 1 12 0v5l2 2H4zM10 20a2 2 0 0 0 4 0" fill="currentColor"/></symbol>
  <symbol id="i-chev-l" viewBox="0 0 24 24"><path d="m15 6-6 6 6 6" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"/></symbol>
  <symbol id="i-chev-r" viewBox="0 0 24 24"><path d="m9 6 6 6-6 6" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></symbol>
  <symbol id="i-swap" viewBox="0 0 24 24"><path d="M4 8h14l-3-3M20 16H6l3 3" fill="none" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-search" viewBox="0 0 24 24"><circle cx="11" cy="11" r="6" fill="none" stroke="currentColor" stroke-width="2"/><path d="m20 20-4.5-4.5" stroke="currentColor" stroke-width="2"/></symbol>
  <symbol id="i-menu" viewBox="0 0 24 24"><path d="M4 7h16M4 12h16M4 17h16" stroke="currentColor" stroke-width="2"/></symbol>
</svg>

<div class="shell">
  <button class="icon-btn menu-btn" id="menuBtn" aria-controls="sidebar" aria-expanded="false" aria-label="Open navigation"><svg width="20" height="20"><use href="#i-menu"/></svg></button>
  <div class="scrim" id="scrim"></div>

  <aside class="sidebar" id="sidebar" data-collapsed="false" aria-label="Primary">
    <div class="dots" aria-hidden="true"><i></i><i></i><i></i></div>
    <div class="head">
      <span class="title txt">SideBar UI</span>
      <button class="icon-btn" aria-label="Notifications, 6 unread" data-tip="Notifications"><svg width="18" height="18"><use href="#i-bell"/></svg><span class="badge">6</span></button>
      <button class="collapse-btn" id="collapseBtn" aria-controls="sidebar" aria-expanded="true" aria-label="Collapse sidebar">
        <span class="chev"><svg width="14" height="14"><use href="#i-chev-l"/></svg></span><span class="pill" id="pillTxt">Collapse</span>
      </button>
    </div>

    <a class="profile" href="#account" data-tip="Dorian — switch account">
      <span class="avatar" aria-hidden="true">D</span>
      <span class="who txt"><b>Dorian</b><small><svg width="11" height="11"><use href="#i-swap"/></svg>Switch account</small></span>
      <svg class="txt" width="16" height="16" aria-hidden="true"><use href="#i-chev-r"/></svg>
    </a>

    <div class="search">
      <svg width="16" height="16" aria-hidden="true" style="color:var(--text-2)"><use href="#i-search"/></svg>
      <input class="txt" type="search" placeholder="Search..." aria-label="Search navigation">
    </div>

    <nav id="nav">
      <div class="group" role="group" aria-labelledby="g1"><div class="label" id="g1">Menu</div>
        <a class="item" href="#home" aria-current="page"><svg width="18" height="18"><use href="#i-home"/></svg><span class="txt">Home</span></a>
        <a class="item" href="#continue"><svg width="18" height="18"><use href="#i-play"/></svg><span class="txt">Continue watching</span></a>
        <a class="item" href="#list"><svg width="18" height="18"><use href="#i-plus"/></svg><span class="txt">My List</span><span class="count" aria-label="7 new">7</span></a>
        <a class="item" href="#series"><svg width="18" height="18"><use href="#i-tv"/></svg><span class="txt">Series</span></a>
        <a class="item" href="#movies"><svg width="18" height="18"><use href="#i-cal"/></svg><span class="txt">Movies</span></a>
        <a class="item" href="#games"><svg width="18" height="18"><use href="#i-game"/></svg><span class="txt">Games</span></a>
      </div>
      <div class="group" role="group" aria-labelledby="g2"><div class="label" id="g2">Discovery</div>
        <a class="item" href="#releases"><svg width="18" height="18"><use href="#i-copy"/></svg><span class="txt">Releases</span></a>
        <a class="item" href="#popular"><svg width="18" height="18"><use href="#i-star"/></svg><span class="txt">Popular</span></a>
      </div>
      <div class="group" role="group" aria-labelledby="g3"><div class="label" id="g3">General</div>
        <a class="item" href="#settings"><svg width="18" height="18"><use href="#i-gear"/></svg><span class="txt">Settings</span></a>
        <a class="item" href="#logout"><svg width="18" height="18"><use href="#i-out"/></svg><span class="txt">Log out</span></a>
      </div>
    </nav>
  </aside>

  <main>
    <div>
      <p id="pageTitle" style="font-size:22px;color:var(--text);font-weight:700;text-align:center">Home</p>
      <div class="demo-ctrls">
        <button data-accent-set="red">Red</button><button data-accent-set="blue">Microsoft blue</button><button data-accent-set="purple">Purple</button>
        <button id="themeBtn">Toggle light glass</button>
      </div>
    </div>
  </main>
</div>
<div class="tip" id="tip" role="tooltip"></div>

<script>
(() => {
  const sb = document.getElementById('sidebar'), btn = document.getElementById('collapseBtn'),
        pill = document.getElementById('pillTxt'), tip = document.getElementById('tip'),
        nav = document.getElementById('nav'), title = document.getElementById('pageTitle');
  const KEY = 'glassnav.collapsed';
  function setCollapsed(c){
    sb.dataset.collapsed = c; btn.setAttribute('aria-expanded', String(!c));
    btn.setAttribute('aria-label', c ? 'Expand sidebar' : 'Collapse sidebar');
    pill.textContent = c ? 'Expand' : 'Collapse';
    try{ localStorage.setItem(KEY, c ? '1' : '0'); }catch(e){}
  }
  try{ setCollapsed(localStorage.getItem(KEY) === '1'); }catch(e){ setCollapsed(false); }
  btn.addEventListener('click', () => setCollapsed(sb.dataset.collapsed !== 'true'));
  // Ctrl/Cmd + B toggles
  document.addEventListener('keydown', e => { if ((e.ctrlKey||e.metaKey) && e.key.toLowerCase()==='b'){ e.preventDefault(); btn.click(); } });

  // Active state (aria-current) + page title
  nav.addEventListener('click', e => {
    const a = e.target.closest('.item'); if (!a) return; e.preventDefault();
    nav.querySelectorAll('.item[aria-current]').forEach(x => x.removeAttribute('aria-current'));
    a.setAttribute('aria-current','page'); title.textContent = a.querySelector('.txt').textContent.trim();
    closeDrawer();
  });
  // Roving arrow-key navigation inside nav
  nav.addEventListener('keydown', e => {
    if (!['ArrowDown','ArrowUp','Home','End'].includes(e.key)) return;
    const items = [...nav.querySelectorAll('.item:not([hidden])')], i = items.indexOf(document.activeElement); if (i < 0) return;
    e.preventDefault();
    const n = e.key==='Home'?0:e.key==='End'?items.length-1:(i + (e.key==='ArrowDown'?1:-1) + items.length) % items.length;
    items[n].focus();
  });
  // Search filters items
  sb.querySelector('.search input').addEventListener('input', e => {
    const q = e.target.value.toLowerCase();
    nav.querySelectorAll('.item').forEach(a => a.hidden = !!q && !a.textContent.toLowerCase().includes(q));
  });
  // Rail tooltips (only when collapsed)
  function showTip(el){
    if (sb.dataset.collapsed !== 'true' || matchMedia('(max-width:760px)').matches) return;
    const label = el.dataset.tip || el.textContent.trim(); if (!label) return;
    const r = el.getBoundingClientRect(); tip.textContent = label;
    tip.style.left = (sb.getBoundingClientRect().right + 12) + 'px';
    tip.style.top = (r.top + r.height/2 - 14) + 'px'; tip.classList.add('show');
  }
  const hide = () => tip.classList.remove('show');
  sb.querySelectorAll('.item,.profile,[data-tip]').forEach(el => {
    el.addEventListener('mouseenter', () => showTip(el)); el.addEventListener('mouseleave', hide);
    el.addEventListener('focus', () => showTip(el)); el.addEventListener('blur', hide);
  });
  // Mobile drawer
  const menuBtn = document.getElementById('menuBtn'), scrim = document.getElementById('scrim');
  function openDrawer(){ sb.classList.add('open'); scrim.classList.add('show'); menuBtn.setAttribute('aria-expanded','true'); sb.querySelector('.item').focus(); }
  function closeDrawer(){ if(!sb.classList.contains('open')) return; sb.classList.remove('open'); scrim.classList.remove('show'); menuBtn.setAttribute('aria-expanded','false'); menuBtn.focus(); }
  menuBtn.addEventListener('click', openDrawer); scrim.addEventListener('click', closeDrawer);
  document.addEventListener('keydown', e => { if (e.key === 'Escape') closeDrawer(); });
  // Demo: accent + light/dark
  document.querySelectorAll('[data-accent-set]').forEach(b => b.onclick = () => document.body.dataset.accent = b.dataset.accentSet);
  document.getElementById('themeBtn').onclick = () => document.body.dataset.theme = document.body.dataset.theme === 'light' ? '' : 'light';
})();
</script>
</body>
</html>
```

## 7. Using it as the left nav of a Visual PowerBi Report

When the user asks for the glass nav inside a report (see
[visual-powerbi-report.md](visual-powerbi-report.md)):

1. Replace the report's `.rail` with this `<aside class="sidebar">` (keep the report's
   `data-page` buttons as the `.item`s, section labels = report groups, "Definitions & data" as the
   last item in a `GENERAL` group).
2. Give the report a backdrop the glass can blur: a deep theme-colour gradient strip
   (`linear-gradient(160deg,var(--nav),#000)`) behind the sidebar column only — keep the report
   canvas `#F4F6FA` so the visuals stay legible.
3. Use the **Microsoft blue** preset (or the report theme's `--c1`) as `--accent`; keep red only
   if the user asks.
4. Collapse to the 80px rail at ≤1280px to give visuals room; drawer at ≤760px.

## 8. Pre-delivery checklist

- [ ] Active bar visibly bleeds past the panel edge; badge, bell count and pill label render
- [ ] Collapse/expand animates smoothly, tooltips appear in rail mode (mouse + keyboard)
- [ ] Mobile drawer opens/closes; Esc works; focus returns
- [ ] Reduced motion / transparency fallbacks verified; contrast ≥ 4.5:1
- [ ] No external fonts, icon CDNs or brand assets — single self-contained file in `output/`
