# Handover — shivanijaiswalart.com

Static, hand-built HTML. Ten pages, no build step, no framework. Open any
`.html` file in a browser and it runs. This folder is everything a WordPress
developer needs to rebuild it faithfully.

```
handover/
  README.md          this file — build order, behaviours, gotchas
  tokens.css         design tokens + the utility classes the layouts depend on
  content-model.md   CPTs, ACF fields, template map, component inventory
  assets.csv         all 47 images: dimensions, weight, where used, alt text
```

## The source

| File | Page | Size | Interactive parts |
|---|---|---|---|
| `index.html` | Home | 86 KB | tilt cards, sticky list, testimonial carousel, FAQ toggle, reel, WhatsApp float |
| `Collections.dc.html` | Collections | 56 KB | 9 FAQ accordions |
| `Live Wedding Painting.dc.html` | Live Weddings | 67 KB | 17 FAQ accordions + View More |
| `Gallery.dc.html` | Gallery | 44 KB | reel |
| `About.dc.html` | About | 37 KB | reel |
| `Kind Words.dc.html` | Kind Words | 50 KB | reel |
| `Blog.dc.html` | Journal | 39 KB | reel |
| `BlogPost.dc.html` | Journal post | 24 KB | — |
| `Enquire.dc.html` | Enquire | 44 KB | form markup, no handler |
| `Privacy.dc.html` | Privacy | 29 KB | — |

`support.js` is the static-site runtime that renders these files. **Do not port it.**
It only exists to run the `<script type="text/x-dc">` block at the bottom of each
page. In WordPress, take the JavaScript *inside* that block and enqueue it as a
normal script; drop everything else.

Fonts are Google Fonts: **Cormorant Garamond** (300/400 + italics) for display,
**Jost** (300/400/500) for UI and body. Self-host them in the theme.

## Build order

1. **Tokens and base** — drop in `tokens.css`, set the two font families, get
   `body { background:#120E1A; color:#F3EDE4 }` and the section rhythm right.
2. **Header and footer partials** — the footer is byte-identical on all ten
   pages; headers differ only by which nav item is highlighted.
3. **Static pages** — About, Privacy, then Collections, Live Weddings.
4. **CPTs and ACF** — see `content-model.md`. Testimonials, Journal, Artwork, FAQ.
5. **Home last** — it carries every interactive component.

## Interactive behaviour to reproduce

All of it is vanilla JS in the `<script type="text/x-dc">` block at the bottom of
each page. No dependencies. Each behaves as follows:

- **Scroll reveal** (`[data-reveal]`, every page) — IntersectionObserver adds a
  rise-in animation once, then unobserves. Never leaves content hidden if JS fails.
- **Testimonial carousel** (`.kw-track`, home) — native horizontal scroll with
  `scroll-snap`, 4 cards visible on desktop, 2 at ≤1080px, 1 at ≤640px. Prev/next
  buttons and dots scroll by one full track width. Autoplays every 5s, pauses on
  hover, focus and touch, and does not autoplay under `prefers-reduced-motion`.
  Uses real `<button>`s with `aria-label`s. **Keep the native scrolling** — it gives
  keyboard and touch support for free and avoids layout shift.
- **FAQ View More** (`[data-faq-toggle]`, home + Live Weddings) — first four
  questions render normally, the rest live in `<div id="faq-more" hidden>`.
  The button toggles `el.hidden` and swaps its label and `aria-expanded`.
  Questions themselves are native `<details>`/`<summary>` — keep them native.
- **Tilt cards** (`[data-cs-card]`, home) — scroll-driven `rotateX` + scale on the
  four "A Few Pieces" cards, calculated per frame from viewport position. Disabled
  under reduced motion. The whole card is a link.
- **Sticky list** (`[data-sticky-list]`, home "What I Create") — the image column
  sticks while the list scrolls; the active list item is highlighted and its
  matching image crossfades in.
- **Reel scroller** (`[data-video-reel]`, every page) — CSS-only horizontal
  scroll-snap strip of 9/16 cards linking to Instagram. No JS.
- **Mobile nav** (≤780px) — CSS-only, a checkbox toggles a slide-in panel. Port it
  as-is or replace with JS; either is fine, but keep it working without JS if you can.

## Known gaps — the client owes content

These are live on the site as placeholders. Wire them to ACF fields (see
`content-model.md`) so they can be filled without a developer:

- **WhatsApp number** — the float button and 8 "WhatsApp Shivani" CTAs point at
  `#` or a bare `wa.me/`. Highest-value fix; it is the primary contact route.
- **Facebook and Pinterest URLs** — footer icons currently point at the sites' homepages.
- **Google review URL** — "Leave a Google Review" button on Kind Words.
- **Three testimonials** — Kind Words shows 9 real reviews plus 3 marked placeholders.
- **Pet portrait, abstract and fabric photography** — three "Image to supply"
  tiles on Collections, one on Gallery.
- **Journal posts** — six cards all link to one placeholder post.
- **Gallery captions** — the Live Weddings section carries a caption template with
  `[City]` / `[Wedding moment]` bracketed fields.
- **International shipping answer** — one FAQ on Collections says
  "[CLIENT: Please confirm international shipping details.]"

Search the source for `[` and `href="#"` to find every one.

## Performance — do this during the rebuild

The static site ships images unoptimised. **23.8 MB across 47 files.**

- `logo.png` is **2.3 MB at 2000×2000** and renders at 44×44. This one file is a
  bigger download than most whole pages.
- 32 photographs are over 400 KB; `spiritual-painting-durga.jpg` is 1 MB.
- The home page loads 25 images, Gallery 41.

WordPress will generate sized variants automatically, but the originals need
compressing before upload and the theme should emit `srcset`, `loading="lazy"`
below the fold, and WebP/AVIF. Only the hero and the first row of any grid should
load eagerly. `assets/paint-portrait.jpg` is unused — do not migrate it.

## Accessibility — already done, please keep

- Every image has descriptive alt text (in `assets.csv`); decorative crossfade
  layers are `aria-hidden`.
- Focus rings: 2px gold, 3px offset, on every link, button and summary.
- Carousel controls are real buttons with labels; dots report position.
- FAQ toggle carries `aria-expanded` and `aria-controls`.
- Ratings expose `aria-label="5 out of 5 stars"`.
- `prefers-reduced-motion` stops the tilt, the carousel autoplay and the reveals.
- Verified: no horizontal overflow at 1440 / 1024 / 768 / 390, no broken internal
  links or anchors, no console errors on any page.

## Layout rules that matter

- Content is capped at **1440px**, centred, with `clamp(20px,4vw,60px)` side padding.
- Sections alternate `#120E1A` and `#1B1526` backgrounds.
- Four-up grids are `.g4` — explicit `repeat(4,...)`, not `auto-fit`. The original
  build used `auto-fit` and it produced orphan rows (5 items resolving to 4+1);
  that is why every four-up is now explicit. **Please keep them explicit.**
- Section lead-ins are centred at 760px with copy capped at 56ch.
- Every content image is `object-fit:cover` at a fixed ratio — 3/4 for gallery
  tiles, 4/5 for category and about images, 16/10 for Journal cards. Never let an
  image size a grid row by its intrinsic height.

## Repository

`github.com/pronoy1004/shivani-jaiswal-portfolio` — currently deployed on Vercel
as a static site. Git history documents the recent design pass if you want context
on why something is the way it is.
