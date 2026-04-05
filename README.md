# CX Mixer Website

Single-page marketing site for **CX Mixer** — a podcast, event series, and community for e-commerce CX leaders. Hosted by Larry Thoma.

**Live site:** [jkrenge.github.io/cx-mixer](https://jkrenge.github.io/cx-mixer/)

## Stack

- **Astro** (static site generator)
- **GitHub Pages** (hosting, auto-deploys on push to `main`)
- **Formspree** (contact form)
- No frameworks, no JS libraries — pure Astro + vanilla CSS + vanilla JS

## Development

```sh
npm install
npm run dev        # http://localhost:4321/cx-mixer/
npm run build      # outputs to ./dist/
```

Note: the site uses `base: '/cx-mixer/'` in `astro.config.mjs` for GitHub Pages. During local dev, navigate to `http://localhost:4321/cx-mixer/`.

## Deployment

Push to `main` triggers the GitHub Actions workflow at `.github/workflows/deploy.yml`. It builds with Node 24 and deploys to GitHub Pages automatically.

## Project Structure

```
src/
  layouts/Base.astro     # Global layout: header, footer, nav, grain overlay
  pages/index.astro      # Single-page site with all sections
  styles/global.css      # Design system: tokens, typography, components
public/
  cxmixer_wordmark.svg   # Header logo
  cxmixer_splash.svg     # Footer / branding logo
  favicon.svg / .ico     # Favicons
  photos/                # Compressed webp event photos + headshots
  videos/                # Compressed MP4 videos (hero hype video)
  quotes/                # LinkedIn profile photos for testimonials
```

## Sections

The site is a single page with anchor navigation (`#events`, `#collective`, `#podcast`, `#sponsors`, `#about`, `#contact`):

1. **Hero** — headline, CTAs, hype video (click-to-play with sound)
2. **Intro** — what CX Mixer is, 4 feature cards
3. **Events** — 3 upcoming event cards
4. **Brand Marquee** — scrolling brand name pills (18 brands)
5. **Photo Masonry** — 22 compressed event photos, CSS columns, lightbox on click
6. **The CX Collective** — community section with floating notification cards
7. **Podcast** — YouTube thumbnail cards for all 11 episodes (Season 1 + Season 2)
8. **Testimonials** — 7 LinkedIn quotes with profile photos, linked to original posts
9. **Sponsors** — CTA + YouTube sponsor video embed
10. **About** — origin story + Larry Thoma & Adam Raftshol cards with LinkedIn links
11. **Contact** — form (Formspree) + social links

## Media Pipeline

Raw photos and videos are kept in `public/raw_photos/` and `public/raw_videos/` (gitignored). Compressed versions go in `public/photos/` and `public/videos/`.

```sh
# Photos: compress to 600px webp
node -e "require('sharp')('public/raw_photos/IMG.jpg').resize(600).webp({quality:72}).toFile('public/photos/IMG.webp')"

# Videos: compress with ffmpeg
ffmpeg -i raw.mp4 -c:v libx264 -preset slow -crf 26 -vf "scale=1280:720" -c:a aac -b:a 128k -movflags +faststart output.mp4
```

## Design System

- **Fonts:** Satoshi (display), Outfit (body), Fraunces (italic emphasis)
- **Colors:** deep purple `#1A0A2E`, magenta `#E91E8C`, cyan `#53C6D6`, neon green `#7FFF00`, purple `#8B31C7`
- **Features:** CSS scroll reveals, mobile scroll spy (single-card highlight), noise grain overlay, gradient dividers, bounce easing on hovers

## Contact Form

Uses [Formspree](https://formspree.io) with honeypot spam protection. Update the form action URL in `src/pages/index.astro` with your Formspree form ID.

## External Links

- YouTube: [@CXMixer](https://www.youtube.com/@CXMixer)
- Spotify: [The CX Mixer](https://open.spotify.com/show/4a2lEUxl4dGm4CqdNm3HvZ)
- LinkedIn: [The CX Mixer](https://www.linkedin.com/company/the-cx-mixer/)
