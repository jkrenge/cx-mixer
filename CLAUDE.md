# CX Mixer — Development Context

## What This Is

Single-page Astro static site for CX Mixer, a podcast and event series for e-commerce CX leaders. Hosted by Larry Thoma. Deployed to GitHub Pages at `jkrenge.github.io/cx-mixer/`.

## Architecture

- **Single page:** Everything is in `src/pages/index.astro` — all sections, all styles, all scripts. No routing, no subpages.
- **Content from YAML:** All copy, data, and links live in `src/content/site.yaml`. The page loads it via Vite `?raw` import + `js-yaml` at build time. Edit YAML to change content — no need to touch Astro templates.
- **Layout:** `src/layouts/Base.astro` handles header, footer, nav, grain overlay, scroll reveal observer, mobile nav toggle, and scroll-to-top on load.
- **Styles:** `src/styles/global.css` is the design system (tokens, fonts, buttons, badges, grids). Page-specific styles are scoped `<style>` blocks in `index.astro`.
- **No build tools beyond Astro:** No Tailwind, no PostCSS, no bundler config. Just Astro + CSS + vanilla JS + js-yaml.

## Key Conventions

- **Base URL:** Site uses `base: '/cx-mixer/'` in `astro.config.mjs`. All asset refs use `` `${import.meta.env.BASE_URL}filename` ``. The trailing slash is critical — without it, paths like `cxmixer_wordmark.svg` break.
- **Fonts:** Satoshi from Fontshare (display headings), Outfit from Google Fonts (body), Fraunces from Google Fonts (italic serif emphasis). Loaded via CSS `@import`.
- **CSS variables:** All design tokens live in `:root` in `global.css`. Use `--sp-*` for spacing, `--r-*` for radius, `--fast/--med/--slow` for durations, `--ease/--ease-bounce` for timing functions.
- **Color palette:** Deep purple base (`--ink: #1A0A2E`), magenta primary accent (`--coral/--magenta: #E91E8C`), cyan secondary (`--teal/--cyan: #53C6D6`), neon green highlight (`--neon: #7FFF00`), purple mid-tone (`--electric: #8B31C7`).
- **Scroll reveals:** Elements with class `reveal` start invisible and animate in when they enter the viewport (IntersectionObserver in Base.astro). Add `reveal-delay-1` through `reveal-delay-5` for staggered timing.
- **Mobile scroll spy:** On screens <= 768px, a `requestAnimationFrame` scroll loop highlights the single card closest to viewport center with `.scroll-active` class. Targets: `.intro__card, .events__card, .yt-card, .quotes__card, .community__float`. Only one element is highlighted at a time.
- **Emphasis in YAML titles:** Use `*word*` in YAML title fields — the template auto-converts to `<em class="serif">word</em>` (Fraunces italic).
- **Page always loads at top:** `history.scrollRestoration = 'manual'` + `window.scrollTo(0, 0)` in Base.astro.

## Content System (site.yaml)

All site content is in `src/content/site.yaml`. Structure:

```yaml
meta:          # Page title + description
links:         # Spotify, YouTube, LinkedIn, Instagram, sponsor_video URLs
hero:          # Headline, subtitle, CTAs, video labels
intro:         # Section copy + feature cards array
events:        # Label, title, subtitle + cards array (each with url for linking)
brands:        # Title + names array (for marquee)
gallery:       # Label, title, featured photo name, photos array
collective:    # Label, title, body, quote, notifications array, values array
podcast:       # Label, title, subtitle + season1/season2 episode arrays
quotes:        # Array of testimonials (text, name, role, photo, url, accent flag)
sponsors:      # Title, body, CTA text
about:         # Label, title, body paragraphs + team array
contact:       # Label, title, subtitle, form_action, inquiry_types array
footer:        # Tagline
```

### Dynamic rendering from YAML

- **Events:** Grid auto-adapts to card count (1=centered 50%, 2=50/50, 3=33% each, 4+=wraps at 3/row). Each card links to its `url` field.
- **Community notifications:** Auto-positioned vertically based on array length, alternating left/right with staggered animation delays.
- **Podcast episodes:** YouTube thumbnail cards auto-pulled from `img.youtube.com/vi/{id}/maxresdefault.jpg`.
- **Quotes:** Rendered from array with profile photos from `public/quotes/` and LinkedIn post links.
- **Team:** Rendered from array — add a person by adding an entry with name, role, bio, photo filename, and LinkedIn URL.

## Media Handling

- **Raw originals** go in `public/raw_photos/` and `public/raw_videos/` — both gitignored.
- **Compressed photos** go in `public/photos/` — webp, 600px max width, ~72 quality. Use `sharp`:
  ```sh
  node -e "require('sharp')('public/raw_photos/IMG.jpg').resize(600).webp({quality:72}).toFile('public/photos/IMG.webp')"
  ```
- **Compressed videos** go in `public/videos/` — mp4, h264, 720p. Use `ffmpeg`:
  ```sh
  ffmpeg -i raw.mp4 -c:v libx264 -preset slow -crf 26 -vf "scale=1280:720" -c:a aac -b:a 128k -movflags +faststart output.mp4
  ```
- **Quote avatars** go in `public/quotes/` — 120px webp thumbnails:
  ```sh
  node -e "require('sharp')('public/quotes/Name.jpg').resize(120,120,{fit:'cover'}).webp({quality:80}).toFile('public/quotes/Name.webp')"
  ```
- **Logo files:** `cxmixer_wordmark.svg` (header), `cxmixer_splash.svg` (footer + hero decoration), `favicon.svg` + `favicon.ico`.

## Content Updates

All content changes go through `src/content/site.yaml`. After editing, either:
- Push to `main` (auto-deploys via GitHub Actions), or
- Edit directly on GitHub (pencil icon on the file) and commit — same auto-deploy.

### Adding a podcast episode
Add entry to `podcast.season1` or `podcast.season2` in YAML:
```yaml
- id: "youtube_video_id"
  title: "Episode Title"
  guests: "Guest A & Guest B"
  ep: "Ep N"
```

### Adding a testimonial
1. Compress profile photo: `120px webp` in `public/quotes/`
2. Add entry to `quotes` array in YAML:
```yaml
- text: "Quote text here"
  name: "Person Name"
  role: "Title, Company"
  photo: "Filename_Without_Extension"
  url: "https://linkedin.com/posts/..."
  accent: false  # true for dark card variant
```

### Adding an event
Add entry to `events.cards` in YAML:
```yaml
- type: "Event Type"
  name: "Event Name"
  desc: "Description"
  status: "MM/DD/YY - time"
  url: "https://partiful-or-event-link"
  featured: true  # optional, highlights the card
```

### Adding event photos
1. Drop raw JPGs in `public/raw_photos/`
2. Compress with sharp to `public/photos/` (600px webp)
3. Add filename (without extension) to `gallery.photos` array in YAML

### Adding a team member
Add entry to `about.team` in YAML:
```yaml
- name: "Person Name"
  role: "Their Role"
  bio: "Short bio"
  photo: "filename_without_extension"
  linkedin: "https://linkedin.com/in/..."
```

## Interactive Features

- **Hero video:** Autoplays muted as preview. Click/tap unmutes, restarts from beginning, shows controls. After video ends, reverts to muted loop. Play button + "Tap to play with sound" CTA at bottom of frame (avoids overlapping video captions). "Sound off" badge in corner hidden via JS `display:none` on play.
- **Photo lightbox:** Click any masonry photo to view full-size in modal. ESC or click outside to close.
- **Mobile scroll spy:** Single-element highlight using rAF loop that finds element closest to viewport center.
- **Scroll reveals:** IntersectionObserver-based, one-shot (unobserved after triggering).

## Deployment

Push to `main` auto-deploys via `.github/workflows/deploy.yml` (Node 24, GitHub Pages). Typical deploy takes ~35 seconds. The workflow also supports `workflow_dispatch` for manual triggers.

## External Services

- **Formspree** — contact form backend. Honeypot `_gotcha` field for bot protection. Update `contact.form_action` in YAML with your Formspree endpoint.
- **YouTube** — podcast episodes as thumbnail cards + sponsor video iframe. Thumbnails auto-pulled from YouTube CDN.
- **Spotify** — linked in hero, footer, and podcast CTAs (not embedded).
- **LinkedIn** — testimonials link to original posts; team cards link to profiles. Company page: `/company/the-cx-mixer/`.
- **Instagram** — linked in footer and contact section.
- **Fontshare** — Satoshi font CDN.
- **Google Fonts** — Outfit + Fraunces font CDN.

## File Structure

```
src/
  content/site.yaml        # ← ALL site content lives here
  layouts/Base.astro       # Header, footer, nav, grain, scroll reveal
  pages/index.astro        # Single-page site (template + styles + scripts)
  styles/global.css        # Design system tokens + components
public/
  cxmixer_wordmark.svg     # Header logo
  cxmixer_splash.svg       # Footer + hero branding
  favicon.svg / .ico       # Favicons
  photos/                  # Compressed webp event photos + headshots
  videos/                  # Compressed mp4 (hero hype video)
  quotes/                  # LinkedIn profile photo thumbnails
  raw_photos/              # (gitignored) Original JPGs
  raw_videos/              # (gitignored) Original MP4s
  quote_input/             # (gitignored) LinkedIn screenshot inputs
```
