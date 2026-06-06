# FOR LIGHT — Website AI Build Prompt
Feed this entire document to your AI coding tool (Cursor, Claude, ChatGPT, etc.) to generate the landing page.

---

## PROMPT START

Build a complete, single-file interactive landing page (`index.html`) for a luxury lighting design company called **FOR LIGHT**. The page must be entirely self-contained — all CSS and JavaScript embedded within the HTML file, no external files except CDN links. Use **vanilla HTML5, CSS3, and JavaScript (ES6+)**. Load **GSAP 3** via CDN for animations. No frameworks (no React, Vue, etc.).

The page has **four sequential acts** (the cinematic intro experience) followed by the **main homepage content**. The design language is precisely specified below — follow it exactly.

---

## DESIGN SYSTEM

This design system governs every pixel of the page. Do not deviate from it.

### Color Tokens

Define all colors as CSS custom properties on `:root`. Never use hardcoded hex values in component styles — always reference the tokens.

```css
:root {
  --bg:       #f1e9d8;  /* Primary page background — warm parchment */
  --bgDeep:   #e6dcc4;  /* Hover states, secondary backgrounds */
  --cream:    #faf4e6;  /* Near-white: text on dark, nav dropdowns */
  --ink:      #1c2118;  /* Primary text — deep forest-tinted near-black */
  --inkMute:  rgba(28,33,24,0.74); /* Secondary text */
  --inkSoft:  rgba(28,33,24,0.92); /* Body copy */
  --rule:     rgba(28,33,24,0.13); /* Hairline dividers */
  --moss:     #3a5239;  /* Deep forest green — utility bar background */
  --mossDeep: #243325;  /* Darker forest — footer background */
  --terra:    #b8654a;  /* Terracotta — primary accent / CTA color */
  --terraDeep:#8a4733;  /* Deeper terracotta — hover state for CTAs */
  --gold:     #a98448;  /* Warm antique gold — mega menus, highlights */
}
```

**Palette logic:**
- Page backgrounds: `--bg` (parchment). Never white, never cool gray, never black for normal content.
- Primary text: `--ink` on light backgrounds. `--cream` on dark (moss/mossDeep) backgrounds.
- Structure: `--moss` for the utility bar. `--mossDeep` for the footer.
- Calls to action: `--terra` fill, `--cream` text. Hover: `--terraDeep`.
- Decorative highlights: `--gold` (sparingly — mega menus, pull quotes).
- No blues. No cool grays. This is a warm-earthen brand.

---

### Typography

Load from Google Fonts:
```html
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@300;400&family=Jost:wght@200;300;400;500;600&display=swap" rel="stylesheet">
```

**Cormorant Garamond** (weight 300–400): All display headlines, hero text, the wordmark "FOR LIGHT", the footer logo, pull quotes. Emotional, editorial, architectural. Wide letter-spacing.

**Jost** (weight 200–600): Navigation, body copy, eyebrows, buttons, captions, form labels, utility text. Geometric, controlled.

**Scale:**
```css
/* Sizes use clamp() for fluid scaling */
.title-xl  { font-family: 'Cormorant Garamond', serif; font-size: clamp(56px, 8vw, 108px); font-weight: 300; letter-spacing: 0.04em; }
.title-lg  { font-family: 'Cormorant Garamond', serif; font-size: clamp(44px, 5.6vw, 78px);  font-weight: 300; letter-spacing: 0.04em; }
.title-md  { font-family: 'Cormorant Garamond', serif; font-size: clamp(32px, 3.5vw, 52px);  font-weight: 300; letter-spacing: 0.03em; }
.eyebrow   { font-family: 'Jost', sans-serif; font-size: 12px; font-weight: 600; letter-spacing: 0.32em; text-transform: uppercase; color: var(--inkMute); }
.body-copy { font-family: 'Jost', sans-serif; font-size: 17px; font-weight: 300; line-height: 1.78; color: var(--inkSoft); }
.btn-label { font-family: 'Jost', sans-serif; font-size: 11px; font-weight: 500; letter-spacing: 0.28em; text-transform: uppercase; }
```

---

### Layout

```css
.container { max-width: 1280px; margin: 0 auto; padding: 0 6vw; }
section    { padding: 100px 0; }
```

**No border-radius anywhere on this site.** Every element — buttons, inputs, cards, image containers, dropdowns — is sharp-cornered. `border-radius: 0` is a design signature. Enforce it globally:
```css
*, *::before, *::after { border-radius: 0 !important; }
```

Hairline rules: `border: none; border-top: 1px solid var(--rule);`

---

### Motion System

**Standard easing:** `cubic-bezier(0.22, 1, 0.36, 1)` — fast start, springy deceleration. Use for all transitions and GSAP tweens.

**GSAP defaults:**
```javascript
gsap.defaults({ ease: 'power3.out', duration: 0.7 });
// For spring feel: use CustomEase or cubic-bezier(0.22,1,0.36,1)
```

**Scroll entrance (Rise pattern):** Elements that enter the viewport animate from:
```css
opacity: 0; transform: translateY(26px);
```
to `opacity: 1; transform: translateY(0)` using `IntersectionObserver`. Apply this to: section headlines, body paragraphs, image blocks, stat numbers.

**Film grain overlay:** Apply a subtle noise texture site-wide using an SVG filter:
```html
<svg style="position:fixed;top:0;left:0;width:100%;height:100%;pointer-events:none;z-index:9999;opacity:0.025;mix-blend-mode:multiply;">
  <filter id="grain"><feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/><feColorMatrix type="saturate" values="0"/></filter>
  <rect width="100%" height="100%" filter="url(#grain)"/>
</svg>
```

**Custom cursor:** On desktop, hide the default cursor (`cursor: none`). Draw a small 6px terracotta circle that follows the mouse with a subtle lag (GSAP quickTo, duration 0.15s). During Acts 0–2 the cursor glow replaces this.

---

### Navigation

The navigation appears after the cinematic intro (Acts 0–3) is complete.

**Utility bar** (32px tall, fixed at top, `z-index: 100`):
```css
background: var(--mossDeep); color: var(--cream);
font-family: 'Jost'; font-size: 11px; font-weight: 400; letter-spacing: 0.2em;
display: flex; align-items: center; justify-content: space-between; padding: 0 6vw;
```
Content: left = "New York City" | center = "FOR LIGHT" | right = "Launching 2026 · Contact"

**Main nav** (68px tall, below utility bar, sticky):
- Initial state (over hero): `background: transparent`, with a dark gradient behind it for readability.
- Scrolled state (after 80px scroll): `background: rgba(241,233,216,0.92); backdrop-filter: blur(14px)`.
- Logo: "FOR LIGHT" in Cormorant Garamond, 22px, weight 300, letter-spacing 0.22em, color `--ink`.
- Nav links: Jost, 11px, weight 500, letter-spacing 0.22em, uppercase, color `--ink`. Active link has an underline that scales in from the left via `transform: scaleX()` on a `::after` pseudo-element.
- CTA button: "Book a Consultation" — `--terra` background over hero, transitions to `--ink` background on scroll.

**Mega menu dropdowns** (Design Services + Fixtures only):
- Full-width panel, `background: var(--cream)`, 1px solid `--rule` border at top.
- Grid layout: `display: grid; grid-template-columns: repeat(4, 1fr);`
- Section headers in `--gold`, body links in `--ink`, `font-size: 13px`.

Nav items: `Home · Design Services · Fixtures · Custom Manufacturing · About · Trade · Contact`

---

### Buttons

Four variants. All `border-radius: 0`, `cursor: pointer`, `transition: all 0.3s cubic-bezier(0.22,1,0.36,1)`.

```css
/* Solid (default) */
.btn-solid { background: var(--ink); color: var(--cream); padding: 14px 32px; }
.btn-solid:hover { background: var(--inkMute); }

/* Terra (primary CTA) */
.btn-terra { background: var(--terra); color: var(--cream); padding: 14px 32px; }
.btn-terra:hover { background: var(--terraDeep); }

/* Ghost */
.btn-ghost { background: transparent; color: var(--ink); border: 1px solid var(--ink); padding: 13px 31px; }
.btn-ghost:hover { background: var(--ink); color: var(--cream); }

/* Moss */
.btn-moss { background: var(--moss); color: var(--cream); padding: 14px 32px; }
.btn-moss:hover { background: var(--mossDeep); }
```

All button labels use `.btn-label` class (11px, Jost, 500 weight, 0.28em letter-spacing, uppercase).

---

### Footer

```css
footer { background: var(--mossDeep); color: var(--cream); padding: 80px 6vw 48px; }
```

4-column grid layout:
- Col 1: Wordmark "FOR LIGHT" in Cormorant Garamond, 36px, + tagline "Serving Light" in Jost 12px.
- Col 2: Design Services links
- Col 3: Products links (Fixtures, Custom Manufacturing, Trade)
- Col 4: Company links (About, Contact) + "Book a Consultation" terra button

Divider above footer bottom: 1px `rgba(250,244,230,0.15)` rule.
Copyright line: Jost 11px, `rgba(250,244,230,0.5)`.

---

## ACT 0 — CURSOR AS LIGHT SOURCE (Entry State)

**What the user sees:**
- On load: full viewport is completely dark — a near-black overlay (`rgba(28,33,24,1)` — the `--ink` color, not pure black) covers everything.
- The user's cursor emits a soft circular pool of warm antique gold light (~180px radius) wherever it moves. The darkness peels back at the cursor, revealing parts of a high-resolution curtain photograph (`assets/curtain.jpg`) beneath.
- Centered on screen, in Cormorant Garamond, weight 300: **"FOR LIGHT"** — in `--cream` color, font-size `clamp(64px, 9vw, 120px)`, letter-spacing 0.14em. Revealed only when the cursor passes over it.
- Below the wordmark: **"Move to explore. Click to enter."** — Jost, 11px, weight 400, letter-spacing 0.28em, uppercase, `rgba(250,244,230,0.55)`.
- When cursor is within 80px of center: a hairline ring (`1px solid rgba(250,244,230,0.3)`) pulses outward from the wordmark at 2-second intervals.

**Technical implementation:**
- Canvas element covers full viewport: `position: fixed; inset: 0; z-index: 20; pointer-events: none`.
- Enable `pointer-events: all` only for the click trigger on the underlying page layer.
- On `mousemove`: use `requestAnimationFrame` — never draw on raw mousemove events.
- Each frame: fill canvas with `rgba(28,33,24,1)`, then draw a radial gradient at cursor position:
  - Inner: `rgba(169,132,72,0.38)` (--gold at 38% opacity)
  - Mid: `rgba(184,101,74,0.12)` (--terra trace)
  - Outer: `rgba(0,0,0,0)` — transparent
  - Radius: 180px
- `ctx.globalCompositeOperation = 'destination-out'` to cut the circle out of the dark overlay, revealing the image beneath.
- Curtain image: `position: fixed; inset: 0; object-fit: cover; z-index: 10;`
- Wordmark + instruction text: `position: fixed; z-index: 25` (above canvas) — always visible but appear dim against the darkness.
- On `click` anywhere: trigger Act 1.
- Mobile / touch: skip Act 0. Show curtain image immediately with a centered "Enter" button (terra, Jost uppercase).

---

## ACT 1 — CURTAIN REVEAL + CINEMATIC VIDEO

**Trigger:** Click during Act 0.

**What the user sees:**
- The dark canvas fades out over 1.4 seconds (GSAP, `cubic-bezier(0.22,1,0.36,1)`) — the curtain image becomes fully visible.
- After 0.3s pause, the HTML5 `<video>` crossfades in and begins playing (muted, no controls, `playsinline`). The video shows: a curtain slowly drawing closed, camera zooms out smoothly to reveal a full living room with floor-to-ceiling windows opening to a balcony. Room is dimly lit — only warm ambient candle-level light.
- Video plays once. Ends → Act 2.
- "Skip" text link appears at bottom-right after 2 seconds: Jost 10px uppercase, `rgba(250,244,230,0.5)`, appears only after 2s delay. Clicking calls `startAct2()`.

**Technical implementation:**
- `<video id="cinematic" src="assets/intro-video.mp4" muted playsinline preload="auto" style="position:fixed;inset:0;width:100%;height:100%;object-fit:cover;z-index:15;opacity:0">`.
- On Act 0 click: `gsap.to(canvas, { opacity: 0, duration: 1.4, ease: 'power2.inOut', onComplete: () => { video.style.opacity = 1; video.play(); } })`.
- `video.addEventListener('ended', startAct2)`.
- Skip: `setTimeout(() => skipBtn.style.opacity = '1', 2000)`.

**Assets required:**
- `assets/curtain.jpg` — linen or silk curtain, half-open, warm amber interior light beyond. Style: cinematic, warm, parchment tones.
- `assets/intro-video.mp4` — 8–12 sec. Curtain slowly closes → camera pulls back → full living room revealed in warm dim light.

---

## ACT 2 — INTERACTIVE ROOM: HOVER TO ILLUMINATE

**Trigger:** Video ends or Skip clicked.

**What the user sees:**
- Crossfade from video to a still photograph (`assets/room-dim.jpg`): modern luxury NYC apartment living room, floor-to-ceiling windows, balcony visible, minimal warm ambient light only. Style consistent with `--bg` palette — cream walls, dark wood floors, linen furniture.
- Invisible fixture hotspots are positioned at the location of each visible light fixture in the photo.
- On hover over a hotspot: a warm gold light bloom (`--gold` palette) expands from the fixture position using canvas + `mix-blend-mode: screen`. The photograph beneath visibly brightens at that point — as if the fixture switched on.
- Multiple fixtures illuminate simultaneously.
- Hover-off: bloom fades out over 0.6s.
- When the first fixture lights up, text fades in at bottom-center: `"This is what designed light feels like."` — Cormorant Garamond, 26px, weight 300, letter-spacing 0.08em, `--cream`.
- When all fixtures are lit (or 6s after first is lit): a button fades in: `"Enter the Site"` — `btn-terra` style. Clicking scrolls to Act 3.

**Technical implementation:**
- Room image: `position: fixed; inset: 0; object-fit: cover; z-index: 5; opacity: 0` — GSAP fade in from 0 when Act 2 starts.
- Canvas overlay: `position: fixed; inset: 0; z-index: 6; mix-blend-mode: screen; pointer-events: none`.
- Fixture hotspots (array of `{ id, x, y, radius, intensity }` — coordinates as 0–1 fractions of viewport):
```javascript
const fixtures = [
  { id: 'floor-lamp',  x: 0.15, y: 0.65, radius: 220, intensity: 0 },
  { id: 'pendant',     x: 0.50, y: 0.35, radius: 180, intensity: 0 },
  { id: 'recessed-1', x: 0.30, y: 0.12, radius: 150, intensity: 0 },
  { id: 'recessed-2', x: 0.70, y: 0.12, radius: 150, intensity: 0 },
  { id: 'wall-sconce', x: 0.85, y: 0.50, radius: 160, intensity: 0 },
];
```
- Invisible `<div>` hotspot triggers: `position: fixed`, sized ~80px × 80px at each fixture coordinate. `mouseover` → GSAP tween `fixture.intensity` to 1 (0.4s). `mouseout` → tween to 0 (0.6s).
- Canvas render loop (rAF): for each fixture, if `intensity > 0`, draw radial gradient:
  - Center: `rgba(169,132,72, intensity * 0.55)` (--gold)
  - Mid: `rgba(184,101,74, intensity * 0.2)` (--terra trace)
  - Edge: transparent
- On mobile: convert hotspots to tap-to-toggle.

**Asset required:**
- `assets/room-dim.jpg` — same room as video final frame, dim warm ambient light, 3840×2160

---

## ACT 3 — TIME OF DAY AMBIENT SCROLL

**Trigger:** "Enter the Site" button, or scroll past the Act 2 viewport.

**What the user sees:**
- The page becomes scrollable. The room photograph fills the viewport as a sticky background while the user scrolls through a tall scroll section (height: 500vh).
- As the user scrolls, the room transitions through five time-of-day states. The room image's CSS filter is interpolated in real time:

| Scroll % | State | `brightness` | `sepia` | Overlay color |
|---|---|---|---|---|
| 0% | **Night** | 0.04 | 0 | `rgba(28,33,24,0.92)` — near ink |
| 20% | **Pre-dawn** | 0.18 | 0.3 | `rgba(36,51,37,0.7)` — deep moss tint |
| 45% | **Sunrise** | 0.55 | 0.65 | `rgba(184,101,74,0.45)` — terra glow |
| 70% | **Golden hour** | 0.85 | 0.55 | `rgba(169,132,72,0.35)` — gold flood |
| 100% | **Full daylight** | 1.0 | 0 | `rgba(241,233,216,0.0)` — clear |

- A separate `<div>` positioned over the window area of the photo intensifies in brightness as scroll progresses (simulating sunlight entering through glass).
- Light fixtures turn on one by one as scroll progresses — same gold bloom canvas technique from Act 2:
```javascript
const scrollFixtures = [
  { id: 'lamp-1',   triggerAt: 0.12, radius: 180 },
  { id: 'pendant',  triggerAt: 0.28, radius: 200 },
  { id: 'sconce',   triggerAt: 0.45, radius: 160 },
  { id: 'recessed', triggerAt: 0.60, radius: 140 },
];
```
- Text overlays (Cormorant Garamond, 34px, weight 300, `--cream`, centered, `position: sticky`) appear and fade at these scroll positions:
  - 0%: *"Without designed light, this is what remains."*
  - 28%: *"Light defines time. Time defines how a space feels."*
  - 58%: *"Great lighting design isn't about the fixture. It's about the moment it creates."*
  - 88%: *"This is FOR LIGHT."*
- At 100% scroll: the utility bar and main navigation slide in from the top. The main homepage content fades in below.

**Technical implementation:**
- Scroll progress: `const p = scrollY / (document.body.scrollHeight - window.innerHeight)`.
- Throttle with `requestAnimationFrame` flag.
- `brightness` / `sepia` interpolated using a lerp function between the table values above.
- Overlay `<div>`: `position: sticky; top: 0; mix-blend-mode: multiply` — background color interpolated from the table.
- Text overlays: `position: sticky; top: 45vh` — opacity goes from 0 → 1 → 0 as scroll passes each threshold (±8% window).

---

## HOMEPAGE CONTENT (after scroll experience)

After Act 3 the page transitions to the standard homepage. Navigation (utility bar + main nav) becomes visible. Content below uses the full design system.

### Intro Hero Section
Background: `--bg`. Full viewport height. Centered.

```
EYEBROW:   NEW YORK CITY · EST. 2026
HEADLINE:  FOR LIGHT
           (Cormorant Garamond, title-xl, weight 300)
SUBHEAD:   One contract. From first design to final commission.
           (Jost, 20px, weight 300, --inkMute)
BODY:      New York City's only firm that designs, specifies, procures, installs,
           and manufactures light — residential, commercial, and exterior —
           under one contract, with one point of accountability.
           (Jost, 17px, weight 300, --inkSoft, max-width 640px)
CTAs:      [Explore Design Services]  (btn-terra)
           [Browse Fixtures]          (btn-ghost)
```

Horizontal hairline rule below the hero: `1px solid var(--rule)`.

---

### Three Service Sections (stacked, alternating image left / text right)

Each section: `padding: 100px 6vw`. Image `object-fit: cover`, aspect ratio 4:3. Text side: eyebrow + title-lg headline + body-copy paragraph + button. Rise entrance animation on both image and text block.

**Section 1 — Design Services** (image right, text left)
```
EYEBROW:   DESIGN SERVICES
HEADLINE:  Lighting Design for
           NYC Interiors and Exteriors
BODY:      We design lighting environments for residential apartments,
           commercial spaces, and exteriors — then stay with you through
           installation. One team handles design, specification, procurement,
           and contractor coordination. When the lights come on, they look
           the way we both said they would.
CTA:       [Explore Design Services]   (btn-ghost)
```

**Section 2 — Fixtures** (image left, text right, background: `--bgDeep`)
```
EYEBROW:   CURATED FIXTURES
HEADLINE:  Fixtures Worth Specifying
BODY:      Every fixture in our collection carries full IES and photometric
           documentation, verified color temperature, and a 5-year warranty.
           Each fixture has its own dedicated page — no hunting through
           pages of uncurated catalog.
CTA:       [Browse Fixtures]   (btn-ghost)
```

**Section 3 — Custom Manufacturing** (image right, text left)
```
EYEBROW:   CUSTOM MANUFACTURING
HEADLINE:  Your Design.
           Our Manufacturing.
BODY:      Have a fixture concept that doesn't exist in any catalog? We
           manufacture it — from prototype to production run, UL/ETL-compliant,
           shipped to your project site. Designers, hospitality developers,
           and homeowners with a vision all start here.
CTA:       [Learn About Custom Manufacturing]   (btn-ghost)
```

---

### Brand Statement Section
Background: `--mossDeep`. Full width. `padding: 120px 6vw`. Centered.

```
PULL QUOTE:  "Every lighting brand positions light as a means to serve people.
              We serve light itself — which is how we make the spaces it
              touches exceptional."

SOURCE:      — FOR LIGHT
             (Jost, 12px, --gold, letter-spacing 0.3em, uppercase)
```

Quote: Cormorant Garamond, `clamp(28px, 3vw, 44px)`, weight 300, `--cream`, max-width 820px, centered, italic.

---

### Stats Row
Background: `--bg`. `padding: 80px 6vw`. 4-column grid. Animated CountUp on scroll entrance.

| Stat | Label |
|---|---|
| 9 | Pain points solved by one contract |
| 4 | Phases from design to sign-off |
| 5yr | Fixture warranty |
| 1 | Point of accountability |

Stat numbers: Cormorant Garamond, 72px, weight 300, `--terra`.
Labels: Jost, 13px, weight 400, `--inkMute`, max-width 160px.
Vertical hairline rules between columns: `1px solid var(--rule)`.

---

### CTA Strip
Background: `--terra`. Full width. `padding: 80px 6vw`. Centered.

```
HEADLINE:  Ready to talk about your space?
           (Cormorant Garamond, 52px, weight 300, --cream)
BODY:      Whether you're planning a renovation, specifying a hospitality project,
           or looking for a fixture that doesn't exist yet — we respond within
           1 business day.
           (Jost, 16px, weight 300, rgba(250,244,230,0.8))
CTA:       [Book a Consultation]   (btn-solid, --cream bg, --terra text)
```

---

### Footer
```css
footer { background: var(--mossDeep); color: var(--cream); padding: 80px 6vw 48px; }
```

4-column grid. Column gap: `6vw`.

- **Col 1**: Wordmark "FOR LIGHT" (Cormorant Garamond, 36px, weight 300, `--cream`). Tagline "Serving Light" (Jost 12px, `--gold`, letter-spacing 0.28em, uppercase). Statement: "New York City · Launching 2026" (Jost 12px, `rgba(250,244,230,0.5)`).
- **Col 2 — Design Services**: Eyebrow label + links: Residential, Commercial, Exterior, Our Process.
- **Col 3 — Products**: Eyebrow label + links: Fixtures, Custom Manufacturing, Trade Program.
- **Col 4 — Company**: Eyebrow label + links: About, Contact. Below: "Book a Consultation" (`btn-terra`, full width).

Horizontal rule: `1px solid rgba(250,244,230,0.12)` above copyright row.
Copyright: "© 2026 FOR LIGHT. All rights reserved." — Jost, 11px, `rgba(250,244,230,0.4)`.

Footer link style: Jost 14px, weight 300, `rgba(250,244,230,0.7)`. Hover: `--cream`. No underlines.
Footer eyebrow style: Jost 11px, weight 600, letter-spacing 0.32em, uppercase, `--gold`. Margin-bottom 20px.

---

## RESPONSIVE BEHAVIOR

**Desktop (>1024px):** Full experience — Acts 0–3 + full homepage content. Custom cursor active.

**Tablet (768–1024px):**
- Act 0: canvas cursor-light works if touch is not the primary input; otherwise skip to Act 1.
- Act 2: tap hotspots instead of hover. Double-tap to enter site.
- Nav: collapse to hamburger at 1024px. Mega menus become full-screen panels.

**Mobile (<768px):**
- Acts 0–1: skip cursor experience. Show curtain image with centered "FOR LIGHT" wordmark and a `btn-terra` "Enter" button.
- Act 2: tap to illuminate fixtures one by one.
- Act 3: scroll experience works; text overlays are shorter (Cormorant Garamond 22px).
- Homepage: all sections stack full-width. Stats row becomes 2-column. Footer becomes single column.

---

## PERFORMANCE REQUIREMENTS

- `assets/curtain.jpg` is NOT lazy-loaded (needed on first frame).
- All other images: `loading="lazy"`.
- Video: `preload="metadata"` initially. Switch to `preload="auto"` only after Act 0 canvas interaction begins (signal user intent).
- Canvas rAF: one global animation loop using a `dirty` flag — only redraw if intensity values changed.
- Scroll handler: one rAF-throttled listener for all scroll effects.
- Page weight target: under 4MB excluding video asset. Images: WebP format, maximum 600KB each.
- All `gsap.to` calls use `overwrite: 'auto'` to prevent conflicting tweens.

---

## ASSET SPECS (for AI image generation)

Generate all assets consistent with the brand's warm-earthen aesthetic.

| Asset | File | Dimensions | Generation prompt |
|---|---|---|---|
| Curtain still | `assets/curtain.jpg` | 3840×2160 | Heavy linen curtain, half-open, warm parchment and gold tones. Soft amber interior light glowing from behind. Cinematic composition, deep focus. No people. Architectural photography style. |
| Intro video | `assets/intro-video.mp4` | 1920×1080 | 8–12 sec cinemagraph: linen curtain slowly draws closed, camera pulls back to reveal a modern luxury NYC apartment living room, cream walls, dark oak floors, floor-to-ceiling windows, dim candlelight warmth. |
| Room dim | `assets/room-dim.jpg` | 3840×2160 | Modern luxury NYC apartment living room. Cream walls, dark oak floors, linen sofa, brass side table. Floor-to-ceiling windows, balcony beyond. Room lit only by faint warm ambient sources — no overhead lights. Pre-dawn feel. Architectural Digest quality. |

**Style consistency note for all assets:** Palette should match the brand — warm parchment, aged brass, deep forest. No blues, no cool grays, no stark white. Film-like grain is acceptable. Images should feel like they were taken by a luxury architectural photographer on medium format film.

---

## PROMPT END
