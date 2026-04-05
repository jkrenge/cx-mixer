# CX Mixer — Development Context

## What This Is

Single-page Astro static site for CX Mixer, a podcast and event series for e-commerce CX leaders. Deployed to GitHub Pages at `jkrenge.github.io/cx-mixer/`.

## Architecture

- **Single page:** Everything is in `src/pages/index.astro` — all sections, all styles, all scripts. No routing, no subpages.
- **Layout:** `src/layouts/Base.astro` handles header, footer, nav, grain overlay, scroll reveal observer, and mobile nav toggle.
- **Styles:** `src/styles/global.css` is the design system (tokens, fonts, buttons, badges, grids). Page-specific styles are scoped `<style>` blocks in `index.astro`.
- **No build tools beyond Astro:** No Tailwind, no PostCSS, no bundler config. Just Astro + CSS + vanilla JS.

## Key Conventions

- **Base URL:** Site uses `base: '/cx-mixer/'` in `astro.config.mjs`. All asset refs use `` `${import.meta.env.BASE_URL}filename` `` or `${base}filename` (in layout). Never hardcode `/cx-mixer/` — always use the variable.
- **Fonts:** Satoshi from Fontshare (display), Outfit + Fraunces from Google Fonts (body + serif emphasis). Loaded via CSS `@import`.
- **CSS variables:** All design tokens live in `:root` in `global.css`. Use `--sp-*` for spacing, `--r-*` for radius, `--fast/--med/--slow` for durations, `--ease/--ease-bounce` for timing functions.
- **Color palette:** Deep purple base (`--ink: #1A0A2E`), magenta primary accent (`--coral/--magenta: #E91E8C`), cyan secondary (`--teal/--cyan: #53C6D6`), neon green highlight (`--neon: #7FFF00`), purple mid-tone (`--electric: #8B31C7`).
- **Scroll reveals:** Elements with class `reveal` start invisible and animate in when they enter the viewport (IntersectionObserver in Base.astro). Add `reveal-delay-1` through `reveal-delay-5` for staggered timing.
- **Mobile scroll spy:** On screens <= 768px, a rAF scroll loop highlights the single card closest to viewport center with `.scroll-active` class. Targets: `.intro__card, .events__card, .yt-card, .quotes__card, .community__float`.

## Media Handling

- **Raw originals** go in `public/raw_photos/` and `public/raw_videos/` — both gitignored.
- **Compressed outputs** go in `public/photos/` (webp, 600px max) and `public/videos/` (mp4, h264, 720p).
- **Quote avatars** go in `public/quotes/` — 120px webp thumbnails of LinkedIn profile photos.
- Use `sharp` (dev dependency) for image compression, `ffmpeg` for video.

## Content Updates

### Adding a podcast episode
Add to the `season1` or `season2` array in the frontmatter of `index.astro`. Each entry needs: `{ id: 'youtube_video_id', title: '...', guests: '...', ep: 'Ep N' }`. The YouTube thumbnail is auto-pulled from `img.youtube.com/vi/{id}/maxresdefault.jpg`.

### Adding a testimonial
Add a new `<a>` block in the quotes section. Needs: LinkedIn post URL, quote text, profile photo (compress to 120px webp in `public/quotes/`), name, and role.

### Adding event photos
Drop raw JPGs in `public/raw_photos/`, compress with sharp to `public/photos/`, then add `<img>` tags in the masonry section.

### Updating the contact form
The form uses Formspree. Update the `action` URL in the `<form>` tag with your Formspree endpoint. Honeypot field `_gotcha` provides basic bot protection.

## Deployment

Push to `main` auto-deploys via `.github/workflows/deploy.yml` (Node 24, GitHub Pages). Typical deploy takes ~35 seconds.

## External Services

- **Formspree** — contact form backend (needs account setup + form ID)
- **YouTube** — podcast episodes embedded as thumbnail cards + sponsor video iframe
- **Spotify** — linked (not embedded)
- **LinkedIn** — testimonials link to original posts; team cards link to profiles
- **Fontshare** — Satoshi font CDN
- **Google Fonts** — Outfit + Fraunces font CDN
