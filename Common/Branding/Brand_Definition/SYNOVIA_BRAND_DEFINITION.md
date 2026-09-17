# Synovia — Brand Definition (consolidated)

**The single source of truth for the look of every Synovia web surface, report
and deck.** Everything needed to build an on-brand screen is in this one file.

Consolidated from, and superseding for reference purposes:

| Source file | What it contributed |
|---|---|
| `Common/assets/UI_Style_Specification.md` | **Flow 3** spec — the theme the PTI portal actually runs on |
| `Common/Branding/BRAND_GUIDE.md` | **Fusion** (Bootstrap) theme + named components |
| `Common/Branding/fusion_design_tokens.json` | Fusion machine-readable tokens |
| `Common/Branding/fusion_theme.css` | Fusion theme implementation |
| `Common/assets/BRAND.md` | Logo/palette asset notes |
| `LiveWeb/app/static/css/flow.css`, `flow3.css` | Flow 3 implementations |
| `Common/Branding/fusion_branding.py` | Excel/deck palette helpers |

The source files remain in place — this document is the definition; the CSS and
Python files remain the *implementations*. Where they disagree, **this file
records which one wins** (§7).

---

## 0. Which system applies where — READ THIS FIRST

There are **two complete, deliberately incompatible design systems** in the
platform. The `flow.css` header is explicit: *"Pages using this sheet do NOT
load Bootstrap / fusion_theme.css — the spec says do not mix the two systems."*

| System | Stylesheet | Base template | Templates | Used by |
|---|---|---|---|---|
| **Flow 3** (current) | `flow.css` — framework-free | `base_flow.html` | **66** | **all 9 `pti_*` pages + `pod_queue`**, and nearly every other screen |
| **Fusion** (Bootstrap) | `fusion_theme.css` | `base.html` | 7 | `home`, `launchpad`, the five `woodies_*` pages |
| Flow 3 component layer | `flow3.css` — `f3-*` namespaced | (overlay) | — | `home`, `launchpad` — Flow 3 look layered *over* Bootstrap |

> **For PTI work, use Flow 3 (§1).** Every `pti_*.html` and `pod_queue.html`
> extends `base_flow.html`. The Fusion theme (§2) is the older Bootstrap
> system, still live on the Woodies and landing pages.

Both systems share the **Montserrat** typeface and a navy/aqua identity, so they
read as one family — but their tokens, class names and component recipes are
different and must not be mixed on one page.

---

# PART 1 — FLOW 3 (the current system · use this for PTI)

Framework-free: vanilla HTML + CSS + JS. No Bootstrap, no icon font, no build
step. Montserrat is `@import`ed by the stylesheet.

## 1.1 Design tokens

```css
:root{
  /* brand darks */
  --abyss:#07152B; --navy:#0F2A4A; --navy2:#143458;
  /* brand accents */
  --flow:#13DAC6; --flow-deep:#0AB6A6;      /* flow-aqua — primary accent */
  --fusion:#FF7A45; --fusion2:#FF9A6B;      /* fusion coral — "powered by" / submit */
  --sky:#3BA0FF;
  /* surfaces */
  --bg:#EEF3FA; --surface:#FFFFFF; --surface2:#F7FAFF;
  /* ink */
  --ink:#132133; --muted:#5E7085; --hairline:#DCE6F2;
  /* status */
  --good:#17B26A; --warn:#F79009; --bad:#F04438; --info:#7A5AF8;
  /* shape */
  --radius:16px; --radius-sm:11px; --pill:999px;
  --shadow:0 1px 2px rgba(15,42,74,.06), 0 8px 24px rgba(15,42,74,.06);
  --shadow-lg:0 18px 50px rgba(7,21,43,.16);
  --font:'Montserrat','Segoe UI',system-ui,-apple-system,sans-serif;
}
```

### Colour usage

| Token | Hex | Use |
|---|---|---|
| `--abyss` / `--navy` / `--navy2` | `#07152B` / `#0F2A4A` / `#143458` | splash gradient, nav bar, sheet headers, dark chips |
| `--flow` / `--flow-deep` | `#13DAC6` / `#0AB6A6` | **primary accent** — active nav, primary buttons, links, done states |
| `--fusion` / `--fusion2` | `#FF7A45` / `#FF9A6B` | "Powered by Fusion", the Submit action, avatar |
| `--sky` | `#3BA0FF` | secondary/info chips, charts |
| `--bg` / `--surface` / `--surface2` | `#EEF3FA` / `#FFFFFF` / `#F7FAFF` | app bg / cards / subtle fills & hovers |
| `--ink` / `--muted` / `--hairline` | `#132133` / `#5E7085` / `#DCE6F2` | body text / secondary text / borders |
| `--good` / `--warn` / `--bad` / `--info` | `#17B26A` / `#F79009` / `#F04438` / `#7A5AF8` | status |

## 1.2 Typography

- **Family:** Montserrat everywhere. Headings `font-weight:800; letter-spacing:-.01em; text-wrap:balance`.
- **Body:** 13–13.5px, `line-height:1.45`, `-webkit-font-smoothing:antialiased`.
- **Numbers:** `.tnum` / `.mono` use `font-variant-numeric:tabular-nums`.

| Role | Size / weight |
|---|---|
| Splash H1 | `clamp(34px,5vw,58px)` / 800 |
| Page title (`h2`) | 25px / 800 |
| KPI value | 34px / 800, `letter-spacing:-.02em` |
| Panel title (`h3`) | 15px / 800 |
| Body | 13–13.5px / 400–600 |
| Eyebrow / label | 11px / 700, `letter-spacing:.14em`, uppercase, `--muted` |
| Small / job hint | 10–10.5px |

```css
.eyebrow{font-size:11px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;color:var(--muted)}
```

## 1.3 Layout

- **Page width:** `main{max-width:1220px;margin:0 auto;padding:26px 30px 70px}`
- **Top bar:** sticky, translucent, blurred — `background:rgba(238,243,250,.82);backdrop-filter:blur(12px);border-bottom:1px solid var(--hairline)`
- **Floating pill nav:** centred, sticky under the top bar, navy gradient capsule; active item = flow gradient
- **Grids:** KPI `repeat(auto-fit,minmax(190px,1fr))`; two-column content `1.6fr 1fr`, collapsing to one column ≤900px
- **View transition:** `@keyframes fade` (opacity + 8px rise, .35s)

```css
.nav{display:flex;gap:4px;padding:6px;border-radius:var(--pill);
  background:linear-gradient(135deg,var(--navy),var(--navy2));box-shadow:var(--shadow-lg);
  border:1px solid rgba(255,255,255,.06)}
.nav button{border:none;background:transparent;color:#AFC3E0;font-weight:600;font-size:13px;
  padding:9px 17px;border-radius:var(--pill);transition:color .15s,background .15s}
.nav button:hover{color:#fff}
.nav button.active{background:linear-gradient(135deg,var(--flow),var(--flow-deep));
  color:#04241f;box-shadow:0 6px 16px rgba(19,218,198,.35)}
```

## 1.4 Components

**Panel / tile / KPI**
```css
.panel{background:var(--surface);border:1px solid var(--hairline);border-radius:var(--radius);
  box-shadow:var(--shadow);padding:20px}
.tile{background:var(--surface);border:1px solid var(--hairline);border-radius:var(--radius);
  padding:18px;box-shadow:var(--shadow);position:relative;overflow:hidden;
  transition:transform .16s ease,box-shadow .16s ease,border-color .16s ease}
.tile:hover{transform:translateY(-3px);box-shadow:var(--shadow-lg);border-color:rgba(19,218,198,.4)}
.tile .edge{position:absolute;left:0;top:0;bottom:0;width:4px;background:var(--flow)} /* accent rail */
.kpi .label{font-size:11.5px;font-weight:600;letter-spacing:.04em;text-transform:uppercase;color:var(--muted)}
.kpi .val{font-size:34px;font-weight:800;letter-spacing:-.02em;margin-top:6px}
```

**Buttons** — primary is a flow gradient with dark-teal ink.
```css
.act{border:none;border-radius:11px;padding:12px 18px;font-weight:700;font-size:13px;color:#04241f;
  background:linear-gradient(135deg,var(--flow),var(--flow-deep));
  display:flex;flex-direction:column;align-items:flex-start;gap:1px;transition:transform .12s,box-shadow .2s}
.act:hover{transform:translateY(-1px);box-shadow:0 8px 20px rgba(19,218,198,.32)}
.act.submit{background:linear-gradient(135deg,var(--fusion),var(--fusion2));color:#3a1400}
.act.reproc{background:linear-gradient(135deg,#F79009,#FBB040);color:#3a2400}
.act.secondary{background:var(--surface2);color:var(--ink);border:1px solid var(--hairline)}
.act.danger{background:linear-gradient(135deg,var(--bad),#F97066);color:#fff}
.act:disabled{opacity:.45;cursor:not-allowed}
.btn{width:100%;padding:13px;border:none;border-radius:11px;font-weight:700;font-size:14px;
  background:linear-gradient(135deg,var(--flow),var(--flow-deep));color:#04241f;transition:transform .12s,box-shadow .2s}
.btn:hover{transform:translateY(-1px);box-shadow:0 10px 26px rgba(19,218,198,.35)}
```

**Status pills & chips** — tinted background, strong text of the same hue, leading dot.
```css
.pill{display:inline-flex;align-items:center;gap:6px;font-size:11px;font-weight:700;padding:3px 10px;border-radius:var(--pill)}
.pill::before{content:"";width:6px;height:6px;border-radius:50%;background:currentColor}
.p-good{background:rgba(23,178,106,.12);color:var(--good)}
.p-sky{background:rgba(59,160,255,.13);color:#1E7FD6}
.p-bad{background:rgba(240,68,56,.12);color:var(--bad)}
.p-flow{background:rgba(19,218,198,.14);color:var(--flow-deep)}
.p-muted{background:rgba(94,112,133,.13);color:var(--muted)}
.p-fusion{background:rgba(255,122,69,.14);color:#D9531F}
.chip{font-size:11px;font-weight:700;color:var(--muted);background:var(--surface2);
  border:1px solid var(--hairline);padding:3px 9px;border-radius:var(--pill)}
```

**Tables** — note: unlike Fusion, the Flow 3 header row is a *light* band with muted uppercase text, not solid navy.
```css
table{width:100%;border-collapse:collapse;font-size:13px}
th{text-align:left;font-size:11px;font-weight:700;letter-spacing:.06em;text-transform:uppercase;
  color:var(--muted);padding:11px 12px;border-bottom:1px solid var(--hairline)}
td{padding:12px;border-bottom:1px solid var(--hairline)}
tbody tr:hover{background:var(--surface2)}
.tablewrap{overflow-x:auto}
```

**Modal / sheet**
```css
.modal{position:fixed;inset:0;z-index:90;display:none;align-items:flex-start;justify-content:center;
  padding:38px 16px;overflow:auto;background:rgba(7,21,43,.55);backdrop-filter:blur(5px)}
.modal.open{display:flex}
.sheet{width:min(1060px,96vw);background:var(--surface);border-radius:20px;box-shadow:var(--shadow-lg);overflow:hidden}
.sheet-head{padding:22px 24px;background:linear-gradient(135deg,var(--navy),var(--navy2));color:#fff}
.sheet-body{padding:24px}
```

**Toast**
```css
.toast{position:fixed;bottom:26px;left:50%;transform:translateX(-50%) translateY(20px);z-index:120;
  background:var(--navy);color:#fff;padding:12px 20px;border-radius:12px;box-shadow:var(--shadow-lg);
  font-weight:600;font-size:13px;opacity:0;transition:.28s}
.toast.on{opacity:1;transform:translateX(-50%) translateY(0)}
```

**Other signature elements:** step/stage rail (numbered circular nodes joined by
a bar — done = flow, current = navy with flow ring, reject = red); dropdown menu
(rounded, `--shadow-lg`, `pop` keyframe); splash screen (layered radial
gradients over navy, faint route-background image, login card with
`backdrop-filter:blur(14px)`).

## 1.5 Motion

- Standard transition: `transform .12–.16s ease, box-shadow .2s`
- Keyframes: `fade` (view enter), `pop` (menus), `pulse` (stage node)
- **Always** wrap: `@media(prefers-reduced-motion:reduce){*{animation:none!important;transition:none!important}}`

## 1.6 Building a new Flow 3 screen

1. Add the Montserrat `@import` and the `:root` block (§1.1).
2. `body{font-family:var(--font);background:var(--bg);color:var(--ink);line-height:1.45}`
3. Compose from `.panel` / `.tile` / `.act` / `.pill` / `.chip` / `table` plus the sticky top bar and floating `.nav`.
4. `--flow` for primary/interactive, `--fusion` for the single hero/CTA accent, navy for structure, status tokens for state.

Keep it framework-free and token-driven and any new screen reads as the same product.

---

# PART 2 — FUSION (Bootstrap theme · legacy surfaces)

Bootstrap 5.3.3 themed via `--bs-*` overrides, Bootstrap Icons 1.11.3, Montserrat.
Used by `base.html`: `home`, `launchpad`, `woodies_*`.

## 2.1 Palette

| Token | Hex | Use |
|---|---|---|
| `navy` | `#0B1D3A` | primary dark — sidebar, table header band, headings |
| `teal` | `#00C4B4` | primary accent — active nav, primary buttons, links |
| `teal_deep` | `#00A99B` | link default / hover on teal |
| `accent` / `amber` | `#F97316` | warning / attention |
| `sky` | `#0EA5E9` | secondary accent / charts |
| `green` | `#198754` | success / healthy |
| `rose` | `#DC3545` | failure / error |
| `violet` | `#7C3AED` | info / secondary category |
| `bg` | `#F0F4FF` | app background |
| `surface` | `#FFFFFF` | cards / panels |
| `text` | `#1E2A3A` | body text |
| `muted` | `#6B7A8D` | secondary text / labels |
| `border` | `#D8E0EE` | borders / dividers |

## 2.2 Layout tokens

- Radii: card **10px**, tile **12px**, pill **12px**, button 6px, small 4px
- Shadows: card `0 1px 3px rgba(11,29,58,.06)`, hover `0 6px 18px rgba(11,29,58,.08)`
- Motion: `transform 0.14s ease, box-shadow 0.14s ease, border-color 0.14s ease`
- Sidebar **220px**; topbar 56px; page padding 28px; content max-width 1400px
- Type scale (px): KPI value 28 · page title 24 · card title 15 · body 13 · label 11 · small 10

## 2.3 Named components

Specify screens **by these names** ("use a *Tile* grid", "statuses as *Status Pills*").

- **Splash Screen** *(do not restyle)* — full-viewport radial navy gradient `#14305c` → `navy` at 70%; centred `FusionLogo.jpg` (~180px) on a white rounded card (12px radius, 14×18px padding) with teal glow `0 8px 30px rgba(0,196,180,.25)`; app name white 38px/700; tagline teal uppercase 13px, 3px letter-spacing; three pulsing teal dots (8px, staggered 0.15s); fade-in 0.7s; version bottom-centre at 35% white; auto-redirect after **1.6s**. Classes `.splash-body .splash .splash-logo .splash-title .splash-tagline .splash-loader .splash-version`.
- **Portal Shell** — fixed navy sidebar (220px) with logo brand block, vertical nav (teal active, 3px left bar), "Powered by Synovia" footer; white sticky topbar (56px); content on `bg`. Classes `.sidebar .sidebar-brand .nav-item.active .sidebar-footer .main .topbar .page-content`.
- **Card** — white panel, 1px border, 10px radius, soft shadow, 20×24px padding; `.card-title` small uppercase muted.
- **KPI Card** — 4px coloured accent strip down the left edge (gradient), 44px tinted icon chip, 28px/700 value, 11px uppercase muted label. Variants `c-sky c-green c-amber c-rose c-violet`; `.kpi-grid` auto-fill min 220px.
- **Tile** — clickable launcher, 12px radius, 44px teal icon chip, navy 15px/700 title, muted description, teal uppercase CTA; hover lifts 2px. `.tile-grid` auto-fill min 260px.
- **Status Pill** — 12px pill, 11px/600 uppercase, tinted background with strong same-hue text (never solid blocks in tables). `.badge.badge-green|amber|red|teal|violet|grey`.
- **Pipeline** — horizontal chain of stage chips with muted arrows; states done (green tint), active (teal tint), error (rose tint), pending (plain).
- **Data Table + Header Band** — the signature: **solid navy `#0B1D3A` header row, white 10px/700 uppercase** — the same band painted into Excel exports so portal and workbook read as one product. `.table-wrap` for scroll, `.mono` for identifiers.
- **Toolbar / Buttons / Pager / Alert / Empty state** — `.toolbar` (`.row-count` right), `.btn` (white, 6px, teal on hover), `.btn-primary` (solid teal, navy text), `.pager`, `.alert-error` / `.alert-info`, `.empty-state`.

---

# PART 3 — SHARED

## 3.1 Status → colour map

Applies identically to portal pills, Excel cells and deck shapes.

| Status values | Fusion | Flow 3 |
|---|---|---|
| SUCCESS · OK · CONFIRMED · SENT · PROCESSED · LIVE | green `#198754` | `--good` `#17B26A` |
| PARTIAL · WARN · PENDING · IN PROGRESS | amber `#F97316` | `--warn` `#F79009` |
| FAILED · ERROR · TIMEOUT | rose `#DC3545` | `--bad` `#F04438` |
| INFO · ARCHIVED-secondary | violet `#7C3AED` | `--info` `#7A5AF8` |
| neutral / LEGACY / unknown | muted `#6B7A8D` | `--muted` `#5E7085` |

## 3.2 The brand outside the web (Excel, decks)

`Common/Branding/fusion_branding.py` carries the identical palette and status
map for openpyxl and python-pptx, so script output matches the portal. Montserrat
falls back to **Segoe UI** where it is not installed.

```python
from fusion_branding import COLORS, STATUS_COLORS, style_header_row, xl_status_font
style_header_row(ws)                # navy Header Band + freeze + autofilter
xl_status_font(cell, "FAILED")      # rose bold text, mirroring the Status Pill
```

API: `COLORS`, `STATUS_COLORS`, `FONT_NAME`/`FONT_FALLBACK`, `hex_to_rgb()`,
`rgb()`, `xl_header()`, `xl_status_font()`, `style_header_row()`.

Scripts reach it by adding `Common\Branding` to `sys.path` — the pattern used by
`PTI_Error_Report.py`, `PTI_Outbound_API_Report.py`, the WOD/WPR report utilities
and the analysis workbooks in `V3\Analyse\`.

## 3.3 Logos and assets

Canonical home: `Common/Branding/`.

| Asset | Use |
|---|---|
| `FusionLogo.jpg` | Fusion product mark — splash, sidebar brand, "Powered by Fusion" |
| `SynoviaLogoHor.jpg` | Synovia company mark — "Powered by" footer |
| `SynoviaFlowLogo.png` / `.svg` | Synovia Flow wordmark — Flow 3 splash / top bar |
| `SynoviaFlowJustLogo.png` | mark only — favicon and compact placements |
| `synovia_logo_white.png` | white wordmark for dark backgrounds |
| `Montserrat (2).zip` | web brand font |
| `CodecPro-Regular (1).ttf` | secondary display face |
| `synovia-route-background.png` | faint splash backdrop (`opacity:.13`, `object-fit:cover`) |

**Rules:** logos placed over navy always sit on a **white rounded panel**.
Product tagline: **"Next-Generation Integration"**. The "Powered by Fusion" pill
uses the coral tokens (`rgba(255,122,69,.14)` background, `--fusion2` text,
glowing coral dot).

Deployed copies: `LiveWeb/app/static/img/`, `LiveWeb_Woodies/app/static/brand/`.

---

## 4. File map

| Path | Role |
|---|---|
| `Common/Branding/Brand_Definition/SYNOVIA_BRAND_DEFINITION.md` | **this file** — the definition |
| `Common/Branding/fusion_theme.css` | Fusion theme (canonical) |
| `Common/Branding/fusion_design_tokens.json` | Fusion tokens (machine-readable) |
| `Common/Branding/fusion_branding.py` | Excel/deck palette helpers |
| `Common/Branding/BRAND_GUIDE.md` | Fusion component guide (source) |
| `Common/assets/UI_Style_Specification.md` | Flow 3 spec (source) |
| `LiveWeb/app/static/css/flow.css` | **Flow 3 theme — what PTI renders with** |
| `LiveWeb/app/static/css/flow3.css` | Flow 3 `f3-*` layer over Bootstrap |
| `LiveWeb/app/static/css/fusion_theme.css` | deployed Fusion theme (in sync with canonical) |
| `LiveWeb/app/templates/base_flow.html` | Flow 3 shell — 66 templates incl. all PTI |
| `LiveWeb/app/templates/base.html` | Fusion shell — 7 templates |

**Sync rule:** `Common/Branding/fusion_theme.css` is the source of truth for the
Fusion theme; the LiveWeb copy is a deployment. They are currently identical —
keep them so.

---

## 5. Known conflicts and open decisions

1. **Two systems, one platform.** Flow 3 is on 66 templates (all of PTI), Fusion
   on 7. The Flow 3 spec calls Fusion *"the previous app … do not mix them"*, yet
   Fusion is still live on the landing and Woodies pages. **No decision is
   recorded anywhere on whether Fusion is being retired.** Until one is, treat
   Flow 3 as the default for new work and leave the Fusion pages alone.

2. **Duplicate brand packs.** `Common/assets/` holds a second copy of
   `BRAND_GUIDE.md`, `fusion_theme.css`, `fusion_design_tokens.json` and
   `fusion_branding.py`. Its tokens **differ** from `Common/Branding/` — it
   carries an `abyss` `#07152B` token and a different `bg`, i.e. it is part-way
   migrated toward Flow 3. **No Python code reads `Common/assets`** — every
   script points at `Common/Branding`. Treat `Common/Branding` as canonical for
   the Fusion pack, and `Common/assets/UI_Style_Specification.md` as canonical
   for Flow 3 (it is the only copy of that spec).

3. **Version drift.** `BRAND_GUIDE.md` declares v1.1.0 while
   `fusion_design_tokens.json` still says `"version": "1.0.0"`, and the JSON's
   description claims it mirrors `style.css` whereas `fusion_theme.css` says the
   tokens are the source. Cosmetic, but it makes "which is authoritative"
   ambiguous. The tokens file is the intended source.

4. **Two "fusion" meanings.** In the Fusion theme, *Fusion* is the product and
   teal `#00C4B4` is its accent. In Flow 3, `--fusion` is a **coral** `#FF7A45`
   used only for the "Powered by Fusion" pill and the single hero CTA, while the
   primary accent is `--flow` aqua `#13DAC6`. Do not carry teal into Flow 3
   screens expecting it to read as "brand".
