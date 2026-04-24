# CX Mixer Website

Single-page marketing site for **CX Mixer** — a podcast, event series, and community for e-commerce CX leaders. Hosted by Larry Thoma.

**Live site:** [cxmixer.com](https://cxmixer.com/)

## Stack

- **Astro** — static site generator
- **GitHub Pages** — hosting, auto-deploys on push to `main`
- **Formspree** — contact form backend
- **js-yaml** — YAML content loading at build time
- No frameworks, no component libraries — pure Astro + vanilla CSS + vanilla JS

## Quick Start

```sh
npm install
npm run dev        # http://localhost:4321/
npm run build      # outputs to ./dist/
```

The site is served from the root at the custom domain `cxmixer.com` (no base path).

## Editing Content

**All site copy lives in [`src/content/site.yaml`](src/content/site.yaml).** You can edit it directly on GitHub (pencil icon) and commit — the site auto-rebuilds and deploys.

The YAML file contains: hero text, event cards, brand names, podcast episodes (YouTube IDs), testimonial quotes, team bios, contact form config, and all external links. See the file for the full structure and comments.

Titles support `*emphasis*` syntax which renders as italic Fraunces serif text.

## Deployment

Push to `main` triggers `.github/workflows/deploy.yml` (Node 24 + GitHub Pages). Takes ~35 seconds.

## Project Structure

```
src/
  content/site.yaml        # ALL editable content
  layouts/Base.astro       # Header, footer, nav, grain overlay
  pages/index.astro        # Single-page template + styles + scripts
  styles/global.css        # Design system tokens + components
public/
  cxmixer_wordmark.svg     # Header logo
  cxmixer_splash.svg       # Footer / hero branding logo
  favicon.svg / .ico       # Favicons
  photos/                  # Compressed webp event photos + headshots
  videos/                  # Compressed mp4 (hero hype video)
  quotes/                  # LinkedIn profile photo thumbnails
```

## Sections

Single page with anchor navigation (`#events`, `#collective`, `#podcast`, `#sponsors`, `#about`, `#contact`):

1. **Hero** — headline, CTAs, hype video (click-to-play with sound)
2. **Intro** — what CX Mixer is + 4 feature cards
3. **Events** — dynamic event cards linked to Partiful/event pages
4. **Brand Marquee** — dual-row scrolling brand pills
5. **Photo Masonry** — CSS columns gallery with lightbox modal
6. **The CX Collective** — community section with floating notification cards
7. **Podcast** — YouTube thumbnail cards (Season 1 + Season 2)
8. **Testimonials** — LinkedIn quotes with profile photos, linked to posts
9. **Sponsors** — CTA + YouTube sponsor video embed
10. **About** — origin story + team cards with LinkedIn links
11. **Contact** — Formspree form + social links

## Adding Content

### New podcast episode
Add to `podcast.season1` or `podcast.season2` in `site.yaml`:
```yaml
- id: "youtube_video_id"
  title: "Episode Title"
  guests: "Guest A & Guest B"
  ep: "Ep N"
```

### New testimonial
1. Save profile photo as `public/quotes/Name.jpg`, compress to 120px webp
2. Add to `quotes` array in `site.yaml`

### New event
Add to `events.cards` in `site.yaml` with `url` for the event page link

### New team member
Add to `about.team` in `site.yaml` with photo in `public/photos/`

### New event photos
1. Drop in `public/raw_photos/`, compress with `sharp` to `public/photos/`
2. Add filename to `gallery.photos` array in `site.yaml`

## Media Pipeline

Raw originals in `public/raw_photos/` and `public/raw_videos/` (gitignored).

```sh
# Photos → 600px webp
node -e "require('sharp')('public/raw_photos/IMG.jpg').resize(600).webp({quality:72}).toFile('public/photos/IMG.webp')"

# Quote avatars → 120px webp
node -e "require('sharp')('public/quotes/Name.jpg').resize(120,120,{fit:'cover'}).webp({quality:80}).toFile('public/quotes/Name.webp')"

# Videos → 720p mp4
ffmpeg -i raw.mp4 -c:v libx264 -preset slow -crf 26 -vf "scale=1280:720" -c:a aac -b:a 128k -movflags +faststart output.mp4
```

## Design System

- **Fonts:** Satoshi (display), Outfit (body), Fraunces (italic emphasis)
- **Colors:** deep purple `#1A0A2E`, magenta `#E91E8C`, cyan `#53C6D6`, neon green `#7FFF00`, purple `#8B31C7`
- **Features:** scroll reveals, mobile scroll spy (single-card highlight), noise grain overlay, gradient dividers, bounce easing hovers, photo lightbox, click-to-play hero video

## Links

- [YouTube](https://www.youtube.com/@CXMixer)
- [Spotify](https://open.spotify.com/show/4a2lEUxl4dGm4CqdNm3HvZ)
- [LinkedIn](https://www.linkedin.com/company/the-cx-mixer/)
- [Instagram](https://www.instagram.com/cxmixer)
