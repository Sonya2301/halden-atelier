# Halden Atelier

A single-page editorial site for a fictional interior architecture studio.

## What it does

- Masked, scroll-parallaxed video as the hero background
- Floating glass nav island with a button-in-button CTA
- Kinetic typography marquee of materials
- **Scroll-scrubbed exploded house view**, rendered frame-by-frame from a 145-image sequence on a canvas (the Apple product-page technique)
- Three editorial chapters with word-by-word opacity scrub-reveals as you scroll past

## Run it

Open `index.html` directly in Chrome (or any modern browser). All assets are local — no build step, no dependencies.

> Local `file://` works fine. The Claude Code preview sandbox blocks local `.mp4` playback, so use a real browser.

## How the scroll-explode works

`exploding_view.mp4` is pre-extracted into 145 JPEG frames in `frames/`. On load each frame is decoded once and promoted to an `ImageBitmap`. As you scroll through the pinned section, a `targetFrame` is computed from scroll progress, and a `requestAnimationFrame` loop eases `currentFrame` toward it and blits the closest frame onto a canvas. No video codec decoding on scroll — that's what makes it smooth.

## Stack

Vanilla HTML, CSS, and JS. Fraunces + Outfit + JetBrains Mono from Google Fonts. No framework.

## Credits

Built with Claude Code using Leon Lehnert's [taste-skill](https://github.com/Leonxlnx/taste-skill) (specifically `redesign-skill`, `soft-skill`, and `gpt-tasteskill`).
