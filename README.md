# jnova.com

Personal consulting site for Nova. Static HTML/CSS/JS, deployed on Vercel at [jnova.com](https://jnova.com).

## Structure

```
jnova-consults/
  index.html          Main page: hero, work, proof, timeline, CTA
  use-cases/
    index.html        Interactive use case builder
  contact/
    index.html        Contact form (Formspree backend)
  vercel.json         Clean URL routing config
```

## Pages

### `/` — Main page
Hero with animated constellation graph, availability pill, intro, what I help with, where it fits (institutions / nonprofits / teams), proof section (PATHS Engine, projects), and a working-with-me CTA.

### `/use-cases` — Use case builder
Three-step interactive builder: Who are you, What is the core problem, What matters most. Supports up to 2 selections per step. Generates a tailored result panel with a headline, body, relevant capability cards, and a CTA that passes selections to the contact page via query string.

### `/contact` — Contact
Two-column layout. Reads `?who=&problem=&priority=` from the use case builder and pre-fills the form. Fields: Name, Email, You are... (select), textarea (optional), preferred contact mode (radio cards). Submits via Formspree. Required fields validated before submission.

## Deploy

This site deploys from the `main` branch via Vercel CLI. GitHub auto-deploy is blocked on the Hobby plan for private repos with multiple committers, so deploy manually:

```bash
vercel deploy --prod
```

Push to GitHub first to keep the repo in sync:

```bash
git push origin main && vercel deploy --prod
```

## Design system

| Token | Value |
|---|---|
| Background | `#07080d` |
| Panel | `#10131f` / `#161b2b` |
| Blue (in-demand) | `#7fb3ff` |
| Violet (foundational) | `#c9a6ff` |
| Mint (privacy/rare) | `#7ce7c0` |
| Gold (advisory) | `#f0c66e` |
| Heading font | Bricolage Grotesque |
| Accent font | Fraunces (italic) |
| Body font | Sora |
| Mono font | Space Mono |

No framework. No build step. All fonts from Google Fonts.

## Mobile behavior

- Fixed nav bar with animated orb and avatar photo
- Nav auto-hides after 3 seconds of inactivity on mobile, reappears on touch or scroll
- Hamburger opens a full-screen overlay nav
- Hero content padded below the fixed nav bar
- Avatar photo shown in nav bar on mobile screens

## Key integrations

- **Formspree** — `https://formspree.io/f/maqzlrpq` — handles contact form POST, no backend required
- **Gravatar** — avatar pulled from `gravatar.com` using SHA-256 of `jevonia@gmail.com`
- **PATHS Engine** — referenced in the proof section and timeline with IP disclosure (invented by Nova, IP held by University of Delaware)

## Routing

`vercel.json` maps clean URLs to subdirectory index files:

```json
{
  "cleanUrls": true,
  "trailingSlash": false,
  "rewrites": [
    { "source": "/use-cases", "destination": "/use-cases/index.html" },
    { "source": "/contact",   "destination": "/contact/index.html"   }
  ]
}
```

## Style notes

- No em dashes anywhere in copy. Use commas, colons, or "and" instead.
- `pointer-events: none` on `.hero-content` (canvas interaction passthrough); clickable children need `pointer-events: auto`.
- Nav `z-index: 60`, mobile overlay `z-index: 55`.
