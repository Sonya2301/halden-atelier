# Halden Atelier

A single-page editorial site for a fictional interior architecture studio based in Bratislava. Built as a vanilla HTML/CSS/JS demo to test premium design execution — masked video hero, kinetic typography marquee, scroll-scrubbed frame-by-frame exploded house view, and editorial chapter reveals.

**Live:** https://sonya2301.github.io/halden-atelier/

---

## Running it locally

No build step. Open `index.html` in any modern browser:

```bash
open index.html          # macOS — opens in default browser
```

Or via a local server (recommended if you want to test the OG image preview):

```bash
python3 -m http.server 8080
# then visit http://localhost:8080
```

> The Claude Code preview sandbox cannot serve local `.mp4` files. Always test in real Chrome / Safari / Firefox.

---

## File map

```
halden-atelier/
├── index.html              ← the entire site (single file, no framework)
├── interior_design.mp4     ← hero background video (looping, ~2.3MB)
├── exploding_view.mp4      ← source video for the scroll-scrubbed frame sequence (4K)
├── frames/                 ← 145 JPEGs extracted from exploding_view.mp4
├── og-image.jpg            ← 1200×630 share image (frame 1, cropped)
├── 404.html                ← custom branded error page
├── robots.txt
├── sitemap.xml
└── README.md
```

---

## How the scroll-scrubbed exploded view works (the centrepiece)

This is the technique Apple uses on their product pages — render a frame-by-frame animation tied directly to scroll position, never the codec.

### 1. Extract the source video to JPEGs

`exploding_view.mp4` is 4K (3840×2160), 24fps, 6 seconds, 145 frames.

```bash
ffmpeg -i exploding_view.mp4 \
  -vf "fps=24" \
  -q:v 3 \
  frames/f_%03d.jpg
```

Output: `frames/f_001.jpg` through `frames/f_145.jpg` (~31MB total at native resolution).

If you re-record the source video at a different frame rate, length, or count, update `FRAME_COUNT` in the script and the `frameTotal` display label.

### 2. Preload and rasterise

Each frame is loaded via plain `<img>` (because `fetch()` is blocked on `file://` in Chrome), then promoted to `ImageBitmap` in the background for fast GPU blits. The first frame's `onload` triggers a `.ready` class that fades the canvas in.

### 3. Scroll → frame mapping

The scroll-scrubbed section is `500vh` tall with a sticky `100dvh` inner stage. A scroll listener computes the section's progress `[0, 1]` from its bounding rect, multiplies by `FRAME_COUNT - 1`, and stores it as `targetFrame`. A separate `requestAnimationFrame` loop eases `currentFrame` toward `targetFrame` (lerp factor `0.10`), then `drawImage()`s the closest frame onto a canvas. The lerp is what makes the motion feel smooth instead of step-y on quick scrolls.

### Why not just scrub the mp4 directly?

Tried. Even with all-keyframe re-encoding, browsers stutter when seeking video. The image-sequence approach has no codec decode on draw — every blit is GPU-direct. Trade-off: bigger initial download (~31MB vs 5MB mp4), but smooth at 60fps.

---

## Design system

Pulled from Leon Lehnert's [taste-skill](https://github.com/Leonxlnx/taste-skill) — specifically `redesign-skill`, `soft-skill`, and `gpt-tasteskill`. The aesthetic target is "Editorial Luxury" (warm cream, deep espresso ink, desaturated terracotta accent, Fraunces serif + Outfit sans, custom cubic-bezier easings, no Inter, no AI-purple, no 3-card rows).

### Tokens

| CSS var | Value | Use |
|---|---|---|
| `--bg` | `#efece5` | Warm cream page background |
| `--ink` | `#1a1815` | Deep espresso primary text |
| `--ink-2` | `#5d5a52` | Secondary text |
| `--ink-3` | `#908c83` | Tertiary text / labels |
| `--accent` | `#8a3a26` | Desaturated terracotta (single accent) |
| `--line` | `rgba(26,24,21,0.10)` | Hairline borders |
| `--font-display` | `"Fraunces", serif` | Headlines + italic accents |
| `--font-body` | `"Outfit", system-ui, sans-serif` | UI + body text |
| `--font-mono` | `"JetBrains Mono", monospace` | Frame chip + small labels |
| `--r-pill` | `999px` | Buttons, marquee dots, nav island |
| `--r-card` | `22px` | Default container radius |
| `--r-lg` | `28px` | Larger container radius |
| `--shadow-soft` | `0 30px 60px -28px rgba(26,24,21,0.18)` | Diffusion shadow on elevated elements |
| `--motion` | `cubic-bezier(0.32, 0.72, 0, 1)` | Default ease |
| `--motion-slow` | `cubic-bezier(0.16, 1, 0.3, 1)` | Slower, more cinematic ease |

---

## Deploying

Hosted on GitHub Pages, served from `main` branch root.

```bash
git add .
git commit -m "your message"
git push
# Pages rebuilds in 30–60 seconds
```

Hard-reload Safari with **Option+Cmd+R** after pushing — it caches aggressively.

---

## Quality baseline

- Open Graph + Twitter cards with a real 1200×630 OG image
- JSON-LD `ProfessionalService` schema
- Canonical URL, `theme-color`, `color-scheme`
- Skip-to-content link, ARIA labels, visible focus rings
- One `<h1>`, semantic `<main>` / `<section>` / `<nav>` / `<article>`
- `prefers-reduced-motion` honoured
- `transform` / `opacity` animations only — no `width`/`height`/`top`/`left`
- Mobile single-column collapse below 720px
- Custom branded 404 page
- `robots.txt` + `sitemap.xml`

### Known gaps (intentional)

- No full HTTP-header CSP — GitHub Pages doesn't allow custom headers; a meta-tag CSP was rejected as too fragile given Google Fonts + inline styles + canvas drawing.
- No WebP for the frame sequence — JPEG q3 is already small and the `<img>` → `ImageBitmap` pipeline is tuned for JPEG decode timing. Worth revisiting if load times become an issue.
- No build step, no TypeScript, no tests — appropriate for a single-file static demo. Would earn their weight if this grew into a multi-page studio site.

---

## Credits

Designed and built with [Claude Code](https://claude.com/claude-code). Aesthetic system from Leon Lehnert's [taste-skill](https://github.com/Leonxlnx/taste-skill).
