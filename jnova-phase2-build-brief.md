# jnova.com Phase 2 — Claude Code Build Brief

> Feed this file to Claude Code at the start of the session.
> Context: Nova_Can_Help.html is live at jnova.com on Vercel. It is a single static HTML file.
> Goal: convert it into a proper multi-page site, add two new interactive pages, connect to GitHub for auto-deploy.

---

## What we are building

A small, fast, multi-page site at jnova.com that keeps the existing aesthetic (dark constellation, Bricolage Grotesque + Fraunces serif, blue/violet/mint palette) and adds two new experiences:

1. A **use case builder** where visitors modularly compose a picture of how Nova could help them, and see a visual result.
2. A **contact page** with a smart form that maps to Nova's three audience lanes.

The site should be deployable on Vercel as a static export (no server required) and connected to GitHub so every push auto-deploys.

---

## Phase 2A — Repository and project setup

### Step 1: scaffold the repo

The repository `jevonia/jnova-consults` already exists on GitHub and is connected to Vercel. Clone it locally if not already done:

```bash
git clone https://github.com/jevonia/jnova-consults.git
cd jnova-consults
```

The target folder structure inside the repo is:

```
jnova-consults/
  index.html          ← the existing Nova_Can_Help.html, renamed
  use-cases/
    index.html        ← the use case builder page (new)
  contact/
    index.html        ← the contact page (new)
  assets/
    nova.jpg          ← placeholder for the avatar photo (copy here when ready)
  vercel.json         ← routing config (see below)
  README.md
```

### Step 2: vercel.json

Vercel needs this to serve subdirectory index files cleanly:

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

### Step 3: Vercel is already connected

`jevonia/jnova-consults` is already linked to Vercel. Every push to `main` auto-deploys to jnova.com. No further setup needed. Confirm by checking the Vercel dashboard after the first push.

### Step 4: update nav in index.html

The existing nav has placeholder anchors. Update to real links:

```html
<a href="/use-cases">Use case builder</a>
<a href="/contact">Work with me</a>
```

Keep the existing same-page anchor links (What I help with, Proof, etc.) for sections on index.html.

---

## Phase 2B — Use Case Builder page (`/use-cases`)

### What it does

A visitor answers three modular questions in sequence, clicking cards rather than filling a form. After each step the page updates. When all three are answered, a "here is what Nova can do for you" panel assembles dynamically from the combination they chose. It should feel like building something, not filling out a survey.

### The three questions and their options

**Step 1: Who are you?**
- A university, college, or K-12 institution
- A nonprofit or community organization
- A small team or founder
- An individual with a project

**Step 2: What is the core problem?**
- Our knowledge lives in people's heads and old documents
- We need AI but our data cannot leave our walls
- Our workflows are too manual and slow
- We want to try AI but do not know where to start
- We need a technical collaborator who can also explain the work

**Step 3: What matters most right now?**
- Seeing a working prototype fast
- Getting the architecture right before we build
- Training the people who will use it
- Measuring whether it actually helps
- Just a conversation to think it through

### The result panel

Once all three are chosen, render a panel with:

- A headline generated from the combination (see logic below)
- 2 to 4 specific capability cards drawn from Nova's skill set that match
- A short paragraph scoped to their situation
- A "This sounds right, let me reach out" button linking to `/contact` with the combination pre-filled as a query string so the contact form can read it

### Headline + card mapping logic

Use a lookup object or switch structure. Some examples to seed the logic:

| Who | Problem | Priority | Headline | Cards shown |
|-----|---------|----------|----------|-------------|
| University | Data cannot leave our walls | Working prototype | "Private AI for your institution, running on your hardware" | Local LLM deployment, FERPA-aware design, RAG pipeline, Federated learning |
| Nonprofit | Knowledge in people's heads | Architecture first | "Structure your institutional knowledge before it walks out the door" | Knowledge graph, Data pipeline, Human review workflow |
| Small team | Too manual and slow | Fast prototype | "Automate the right things, keep humans where they matter" | Agentic workflows, API integration, Full-stack delivery |
| Individual | Want to try AI | Just a conversation | "A single good conversation can save months of wrong turns" | Advisory, AI literacy, Scoping |

Build enough combinations to cover the matrix meaningfully. For combinations not explicitly mapped, fall back to a "broad fit" panel that shows Nova's top three generalist capabilities plus an invitation to talk.

### Interaction design

- Each step is a row of clickable cards. Selected card gets a glowing accent border and a checkmark.
- Steps below the current one are faded and locked until the prior step is answered. Answering a step unlocks the next with a smooth entrance animation.
- An "edit" affordance on each completed step lets the visitor go back and change their answer without losing later steps (they do get cleared and re-answered).
- The result panel slides up from below when all three steps are complete.
- A "Start over" link resets the whole builder.

### Visual design

Match the existing aesthetic exactly:
- Background: `#07080d`, panel cards `#10131f` / `#161b2b`
- Fonts: Bricolage Grotesque for headings, Fraunces italic for accent words, Sora for body, Space Mono for labels
- Accent colors: mint `#7ce7c0` (rare/privacy), blue `#7fb3ff` (in-demand), violet `#c9a6ff` (foundational), gold `#f0c66e` (advisory)
- Card selection state: `border-color` set to the relevant accent, `box-shadow: 0 0 0 1px accent, 0 20px 50px rgba(0,0,0,.5)`
- No em dashes anywhere in copy

---

## Phase 2C — Contact page (`/contact`)

### What it does

A focused, calm page. Not a generic contact form. The framing is "start a conversation," not "submit a ticket." It should make a person feel like they are reaching a thoughtful human, not a form processor.

### Layout

Two-column on desktop, single column on mobile:

**Left column:** copy and framing
- Heading: "Let's think through it together."
- Subhead (Fraunces italic): "Whether it's a quick question or a real project, the conversation is free."
- A short list of good reasons to reach out (pull from the CTA section of index.html: the "good ways to start a conversation" bullets)
- Nova's availability pill (the pulsing green "Open to projects, consultations, and collaborations" from the hero)

**Right column:** the form

### Form fields

```
Name                          (text, required)
Email                         (email, required)
You are...                    (select: a university / a nonprofit / a small team / an individual / something else)
What are you working on?      (textarea, 4 rows, optional — "No need to have it figured out")
How would you like to start?  (radio cards):
    "A quick 30-minute call"
    "An email exchange first"
    "Send me a brief proposal scope"
    "I am not sure yet"
Submit button text: "Start the conversation"
```

If the page is reached via the use case builder (query string present), pre-fill the "You are..." select and surface a small banner: "Based on what you shared in the use case builder" with a summary of their selections.

### Form handling

Since this is a static site, use **Formspree** (https://formspree.io) for the form backend. It is free for low volume, requires no server, and works with a single `action` attribute on the form. Nova will need to create a free Formspree account and paste her form endpoint URL in. Leave a clear `<!-- PASTE YOUR FORMSPREE ENDPOINT HERE -->` comment in the action attribute placeholder.

Do NOT use mailto: links. Do not build a custom backend for this.

### Success state

On successful submission, replace the form with a calm confirmation panel:
- "Got it. I will be in touch."
- A small constellation graphic (can reuse the canvas from index.html at a smaller scale, or a static SVG version)
- A link back to the use case builder in case they want to share it with someone

### Visual design

Same palette as the rest of the site. The form inputs should feel premium, not generic:
- Input background: `rgba(255,255,255,.04)`, border: `1px solid rgba(160,180,230,.14)`
- Focus state: border-color `var(--blue)`, box-shadow `0 0 0 3px rgba(127,179,255,.15)`
- Radio cards: same card component as the use case builder selection cards
- No floating labels (they are clever and annoying). Standard labels above each field.
- Submit button: full-width on mobile, gradient background matching the nav orb

---

## Phase 2D — Shared assets and consistency rules

### Shared CSS / design tokens

If the site grows beyond three pages, extract the shared CSS (variables, nav, fonts, footer) into `assets/nova.css` and link it from all pages. For now, inline is acceptable as long as the token values are identical across pages.

### Nav consistency

Every page gets the same nav from index.html. The active page link should have `color: var(--ink)` and a subtle underline or dot indicator. Use a `<script>` block at the bottom of each page to auto-detect and highlight the current path.

### No em dashes anywhere. Use "and", commas, or colons instead.

### Fonts

All three pages load the same Google Fonts link:
```
Bricolage+Grotesque:opsz,wght@12..96,400;12..96,600;12..96,700;12..96,800
Fraunces:ital,opsz,wght@0,9..144,400;0,9..144,500;0,9..144,600;0,9..144,900;1,9..144,400;1,9..144,500;1,9..144,600
Sora:wght@200;300;400;500;600;700
Space+Mono:wght@400;700
```

### Avatar / Gravatar

The index.html avatar currently shows a monogram "N" placeholder. When a real photo is ready, swap the `<span class="avatar-mono">N</span>` for:

```html
<img src="https://gravatar.com/avatar/f10943a48512fc1af56f4de95c85a995e4c056358f53a9bba58010d0be8d2b53?s=400" alt="Nova" referrerpolicy="no-referrer">
```

The hash above is the SHA-256 of `jevonia@gmail.com`. A photo must be uploaded to gravatar.com/jevonia first.

---

## Checklist for Claude Code

Work through these in order. Each item is a self-contained deliverable.

- [ ] Create the GitHub repository and scaffold the folder structure
- [ ] Move index.html into root, update nav links
- [ ] Add vercel.json
- [ ] Build `/use-cases/index.html` with step-by-step builder and result panel
- [ ] Build `/contact/index.html` with smart form and Formspree integration
- [ ] Add active-nav detection script to all pages
- [ ] Verify no em dashes in any copy across all three pages
- [ ] Push to GitHub and confirm Vercel auto-deploys
- [ ] Confirm jnova.com, jnova.com/use-cases, and jnova.com/contact all load correctly
- [ ] Test use-case-to-contact query string handoff

---

## What Nova needs to do before or during the session

1. Create a free Formspree account at formspree.io and get the form endpoint URL for Claude Code to paste in
2. Decide: public or private GitHub repo (does not affect the build, just visibility)
3. In Vercel dashboard: Settings > Git, connect the new repo after Claude Code creates and pushes it
4. Optionally: upload a photo to gravatar.com/jevonia so the avatar is live

---

*Brief compiled June 2026. Aesthetic reference: jnova.com (live). No em dashes in any copy.*
