# FOR LIGHT — Website AI Build Prompt
Feed this entire document to your AI coding tool (Cursor, Claude, ChatGPT, etc.) to generate the landing page.

---

## PROMPT START

Build a complete, single-file interactive landing page (`index.html`) for a luxury lighting design company called **FOR LIGHT**. The page must be entirely self-contained — all CSS and JavaScript embedded within the HTML file, no external files except CDN links. Use **vanilla HTML5, CSS3, and JavaScript (ES6+)**. Load **GSAP 3** via CDN for animations. No frameworks (no React, Vue, etc.).

The page has **four sequential acts** that flow into each other. Each act is a full-screen experience.

---

## ACT 0 — CURSOR AS LIGHT SOURCE (Entry State)

**What the user sees:**
- The screen is completely black on load.
- The user's mouse cursor emits a soft, warm circular pool of light (~180px radius) wherever it moves — like holding a candle in a dark room.
- Beneath the black overlay is a high-resolution image of a half-open curtain (`assets/curtain.jpg`). The cursor's light circle reveals parts of this image as the user moves around.
- In the very center of the screen, in elegant serif type, appears the text: **"FOR LIGHT"** — barely visible at first, revealed when the cursor passes over it.
- Below the title, in smaller type: **"Move to explore. Click to enter."**
- When the cursor is near the center CTA area, a subtle animated pulse rings outward from the text.

**Technical implementation:**
- Use an HTML5 `<canvas>` element covering the full viewport, positioned above the curtain image with `position: fixed; z-index: 10`.
- On every `mousemove` event, clear the canvas and draw a radial gradient centered on the cursor: inner color `rgba(255, 200, 100, 0.35)`, outer color `rgba(0, 0, 0, 0)`, radius 180px. The canvas background is `rgba(0, 0, 0, 1)` — solid black everywhere except the gradient.
- Use `canvas.globalCompositeOperation = 'source-over'` — paint the warm circle, then fill the rest with black.
- The curtain image sits in a `<div>` behind the canvas with `z-index: 1`.
- On `click` anywhere, trigger Act 1.
- On touch devices (no cursor): skip Act 0, go directly to Act 1 with a tap-to-enter button.

---

## ACT 1 — CURTAIN REVEAL + CINEMATIC VIDEO

**Trigger:** User clicks anywhere during Act 0.

**What the user sees:**
- The canvas black overlay fades out over 1.2 seconds — the half-open curtain image is now fully visible.
- Simultaneously, an HTML5 `<video>` begins playing (muted, no controls). The video shows: a curtain slowly closing, then the camera zooms out to reveal a full living room with large floor-to-ceiling windows opening to a balcony. The room is dimly lit — warm, low ambient light only.
- The video plays once. When it ends, Act 2 begins automatically.
- A small "Skip" button in the lower-right corner lets users bypass the video.

**Technical implementation:**
- Video element: `<video id="cinematic" src="assets/intro-video.mp4" muted playsinline preload="auto">`. Hidden initially.
- On Act 0 click: GSAP `fadeOut` canvas + curtain image (1.2s), then crossfade in the video element.
- `video.addEventListener('ended', startAct2)`.
- Skip button: `position: fixed; bottom: 32px; right: 32px;` — clicking it calls `startAct2()` directly.
- Video must be `object-fit: cover` to fill the full viewport without letterboxing.

**Asset required:**
- `assets/curtain.jpg` — high-res still of a half-open curtain (warm interior light behind it)
- `assets/intro-video.mp4` — 8–12 second cinematic clip: curtain closes, zoom out to living room

---

## ACT 2 — INTERACTIVE ROOM: HOVER TO ILLUMINATE

**Trigger:** Video ends (or Skip is clicked).

**What the user sees:**
- The room is shown as a still photograph (`assets/room-dim.jpg`): a living room, floor-to-ceiling windows, balcony visible beyond, warm minimal ambient light.
- Scattered around the room are invisible hotspots positioned at the exact locations of visible light fixtures in the photograph (e.g., a floor lamp at left, a pendant over a table, recessed lights in the ceiling, a wall sconce near the balcony door).
- When the user hovers over a fixture hotspot, a warm amber light bloom expands outward from that point — as if the fixture has just turned on. The bloom uses `mix-blend-mode: screen` on a canvas overlay so it naturally lights up the photograph beneath.
- Multiple fixtures can be lit simultaneously.
- Hovering off a fixture slowly dims the bloom (0.6s fade out).
- When the first fixture is lit, elegant copy appears at bottom-center: **"This is what designed light feels like."**
- After all fixtures are lit (or after 6 seconds), a CTA button fades in: **"Enter the Site"** — clicking it scrolls smoothly to Act 3.

**Technical implementation:**
- Room image: full-screen `<img>` with `object-fit: cover`.
- Canvas overlay: same dimensions as viewport, `position: absolute; top: 0; left: 0; mix-blend-mode: screen; pointer-events: none`.
- Fixture hotspots: an array of objects, each with `{ x: %, y: %, radius: 200, color: 'rgba(255,180,60,0.5)' }`. Coordinates defined as percentages of viewport width/height so they scale correctly.
- On hover, animate each hotspot's `opacity` from 0 to 1 using GSAP (duration 0.4s, ease: 'power2.out'). On mouse-out, animate back to 0 (0.6s).
- Redraw the canvas on each animation frame: clear, then for each active hotspot draw a radial gradient from the hotspot center.

**Placeholder fixture hotspot coordinates (adjust to match your actual room photo):**
```javascript
const fixtures = [
  { id: 'floor-lamp',    x: 0.15, y: 0.65, radius: 220, intensity: 0 },
  { id: 'pendant',       x: 0.50, y: 0.35, radius: 180, intensity: 0 },
  { id: 'recessed-1',   x: 0.30, y: 0.12, radius: 150, intensity: 0 },
  { id: 'recessed-2',   x: 0.70, y: 0.12, radius: 150, intensity: 0 },
  { id: 'wall-sconce',  x: 0.85, y: 0.50, radius: 160, intensity: 0 },
];
```

**Asset required:**
- `assets/room-dim.jpg` — same room as video final frame, high-res still, dim warm ambient light

---

## ACT 3 — TIME OF DAY AMBIENT SCROLL

**Trigger:** User clicks "Enter the Site" or scrolls past Act 2.

**What the user sees:**
- The page transitions into a scroll-driven experience. The same room is displayed, but now the environment changes as the user scrolls downward.
- At the **top** of the scroll: the room is completely dark. Night. The windows show a dark sky. All artificial lights are off.
- As the user scrolls down, the scene transitions through:
  1. **Pre-dawn** (0–20% scroll): very faint blue-gray light at the horizon through the window
  2. **Sunrise** (20–50% scroll): warm amber and gold spill through the window, long shadows
  3. **Golden hour** (50–80% scroll): the room is flooded in warm golden light, every surface glowing
  4. **Full daylight** (80–100% scroll): bright, balanced natural light fills the room
- As the user scrolls, light fixtures inside the room **turn on one by one** — because as the natural light shifts, the designed lighting adjusts. Each fixture turns on at a specific scroll position with a warm bloom effect.
- Fixed text overlays appear and fade at specific scroll positions:
  - At 0%: *"Without designed light, this is what remains."*
  - At 30%: *"Light defines time. Time defines how a space feels."*
  - At 60%: *"Great lighting design isn't about the fixture. It's about the moment it creates."*
  - At 90%: *"This is FOR LIGHT."* — followed by the main site navigation appearing

**Technical implementation:**
- Use a `scroll` event listener on `window`. Calculate scroll progress as `scrollY / (document.body.scrollHeight - window.innerHeight)` → a value from 0 to 1.
- The room image `<img>` has a CSS filter applied in JavaScript: interpolate between states using scroll progress:
  - `brightness`: 0.05 at 0% → 0.4 at 30% → 0.9 at 70% → 1.0 at 100%
  - `sepia`: 0.8 at 30–60% (golden warmth) → 0 at 100%
  - `hue-rotate`: slight warm shift (+10deg) at golden hour → 0 at daylight
- Overlay a full-screen `<div>` with a gradient color that changes with scroll: deep navy at 0%, warm amber at 50%, sky blue-white at 100%. Use `mix-blend-mode: multiply` so it tints the photo beneath.
- Window glow effect: a separate `<div>` positioned over the window area of the photo, its background gradient (glow color + intensity) interpolated from scroll progress.
- Text overlays: `position: fixed`, opacity controlled by `IntersectionObserver` or scroll thresholds.
- Fixture blooms: same canvas technique as Act 2, each fixture has a `triggerScrollPosition` (0.0–1.0). When scroll passes that threshold, animate the bloom in.

**Fixture turn-on scroll triggers:**
```javascript
const scrollFixtures = [
  { id: 'lamp-1',   triggerAt: 0.15, radius: 180 },
  { id: 'pendant',  triggerAt: 0.30, radius: 200 },
  { id: 'sconce',   triggerAt: 0.50, radius: 160 },
  { id: 'recessed', triggerAt: 0.65, radius: 140 },
];
```

**At scroll = 1.0 (bottom):**
- The FOR LIGHT main navigation bar fades in: `Home | Design Services | Fixtures | Custom Manufacturing | About | Trade | Contact`
- Below it, the standard homepage hero copy (see below) is visible.

---

## HOMEPAGE CONTENT (below the scroll experience)

After Act 3, the page continues with standard homepage sections. These stack below the scroll experience:

### Hero Section
```
Headline: FOR LIGHT
Subhead: New York City's only firm that designs, specifies, procures, installs,
         and manufactures light — under one contract, with one point of accountability.
CTAs: [Explore Design Services]  [Browse Fixtures]  [Custom Manufacturing]
```

### Three Service Sections (stacked, full-width, alternating image/text)

**Section 1 — Design Services**
```
Headline: Lighting Design for NYC Interiors and Exteriors
Body: We design lighting environments for residential apartments, commercial spaces,
      and exteriors — then stay with you through installation. One team handles design,
      specification, procurement, and contractor coordination. When the lights come on,
      they look the way we both said they would.
CTA: [Explore Design Services →]
```

**Section 2 — Fixtures**
```
Headline: Fixtures Worth Specifying
Body: Every fixture in our collection carries full IES and photometric documentation,
      verified color temperature, and a 5-year warranty. Available for immediate purchase,
      with each fixture on its own dedicated page — no hunting through pages of uncurated catalog.
CTA: [Browse Fixtures →]
```

**Section 3 — Custom Manufacturing**
```
Headline: Your Design. Our Manufacturing.
Body: Have a fixture concept that doesn't exist in any catalog? We manufacture it —
      from prototype to production run, UL/ETL-compliant, shipped to your project site.
      Designers, hospitality developers, and homeowners with a vision all start here.
CTA: [Learn About Custom Manufacturing →]
```

### Brand Statement
```
"Every lighting brand positions light as a means to serve people.
 We serve light itself — which is how we make the spaces it touches exceptional."
                                                          — FOR LIGHT
```

### Footer
```
FOR LIGHT — New York City — Launching 2026
Design Services | Fixtures | Custom Manufacturing | About | Trade Program | Contact
© 2026 FOR LIGHT. All rights reserved.
```

---

## TYPOGRAPHY & VISUAL STYLE

- **Font stack**: `'Playfair Display', Georgia, serif` for headlines; `'Inter', system-ui, sans-serif` for body. Load both from Google Fonts.
- **Color palette**:
  - Background: `#0a0a0a` (near black)
  - Primary text: `#f5f0e8` (warm white)
  - Accent: `#c8a96e` (warm amber/gold — the color of light)
  - Secondary text: `#8a8070` (warm gray)
  - CTA buttons: `#c8a96e` background, `#0a0a0a` text
- **Spacing**: generous whitespace, large type. Minimum 18px body text.
- **Transitions**: all transitions use `ease-out` or GSAP `power2.out`. Nothing snaps — everything breathes.
- **Cursor**: on desktop, replace the default cursor with a small warm dot (4px circle, amber color) to reinforce the light theme throughout.

---

## RESPONSIVE BEHAVIOR

- **Desktop (>1024px)**: Full experience as described.
- **Tablet (768–1024px)**: Acts 0–1 play. Act 2 uses tap-to-illuminate instead of hover. Act 3 scroll experience works as described.
- **Mobile (<768px)**: Skip Act 0 (no cursor). Show curtain still image with a "Tap to Enter" button → fade to room. Tap fixtures to illuminate. Scroll experience works as described.

---

## PERFORMANCE REQUIREMENTS

- All images lazy-loaded except `assets/curtain.jpg` (needed immediately).
- Video preloaded but not autoplayed until Act 0 is complete.
- Canvas redraws throttled to `requestAnimationFrame` — never on raw `mousemove` directly.
- Scroll handler throttled using `requestAnimationFrame`.
- Total page weight target: under 4MB (before video asset).

---

## ASSET PLACEHOLDER SPECS (for AI-generated or stock assets)

Generate or source these assets to match the style described:

| Asset | File | Dimensions | Description |
|---|---|---|---|
| Curtain still | `assets/curtain.jpg` | 3840×2160 | Half-open velvet or linen curtain, warm interior light behind it, dramatic |
| Intro video | `assets/intro-video.mp4` | 1920×1080 | 8–12 sec: curtain closes slowly, camera pulls back revealing full living room |
| Room dim | `assets/room-dim.jpg` | 3840×2160 | Modern living room, large windows to balcony, minimal warm ambient light only |

**Style reference for all assets:**
Modern luxury NYC apartment. Warm neutral palette — cream walls, dark wood floors, linen furniture. Floor-to-ceiling windows with a balcony view. Warm amber light sources. Cinematic, not editorial. Shot or rendered as if by a luxury architecture photographer (think Architectural Digest, not IKEA).

---

## PROMPT END
