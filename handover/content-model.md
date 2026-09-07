# Content model for WordPress

Everything on the site that repeats is listed here with the fields it needs.
Anything not listed is static template markup and does not need to be editable.

Recommended stack: classic PHP templates + ACF (Pro, for repeaters). The site
has no blog-style archive needs beyond the Journal, so ACF options pages and
a couple of CPTs cover it. Blocks are optional; the layouts are fixed enough
that ACF fields will be faster to build and safer for the client to edit.

---

## 1. Custom post types

### `testimonial` — Kind Words
Used on: Kind Words page (grid of 12), Home (carousel of 9).

| Field | Type | Notes |
|---|---|---|
| `name` | post title | e.g. "Deep Chakraborty" |
| `initials` | text, 1–2 chars | shown in the gold avatar circle; auto-generate from title, allow override |
| `work_type` | select | Live wedding painting / Portrait commission / Custom commission |
| `rating` | number 1–5 | renders as ★ characters, `aria-label="5 out of 5 stars"` |
| `quote` | textarea | wrapped in curly quotes by the template, do not store the quotes |
| `featured_on_home` | true/false | Home carousel shows the 9 flagged; Kind Words shows all |

Three "reserved slot" cards are currently placeholders in the markup. In WP they
should simply not exist — publish real testimonials as they arrive and let the
grid grow. Ask the client for the three outstanding reviews.

### `journal_post` — the Journal
Or use core `post` with a custom category. Six are live.

| Field | Type | Notes |
|---|---|---|
| title | post title | |
| `category` | taxonomy | Live Wedding Painting, From the Painting Stand, Wedding Planning, Portraits and Custom Art, On Painting |
| featured image | core | rendered at 16/10, `object-fit:cover` |
| `excerpt` | textarea | one short paragraph — card heights depend on this staying short |
| body | editor | single post template exists as `BlogPost.dc.html` |

All six cards currently link to the same placeholder post. Real posts need writing.

### `artwork` — Gallery
Used on: Gallery page (three grids), Home "From My Easel" strip.

| Field | Type | Notes |
|---|---|---|
| image | core featured image | rendered at 3/4, `object-fit:cover` |
| `alt` | text | required, descriptive — see `assets.csv` for the current wording |
| `gallery_section` | taxonomy | live-weddings / portraits / pets / spiritual / custom / behind |
| `caption_couple` | text | optional, e.g. "Shreya and Onkar" |
| `caption_city` | text | optional |
| `caption_moment` | text | optional, e.g. "Sunset couple portrait" |
| `caption_spec` | text | optional, e.g. "18 x 24 inches, acrylic on canvas" |
| `caption_note` | textarea | optional short story |

Keep counts to multiples of four per section so rows stay full: currently 12 / 4 / 8.

### `faq`
Used on: Home (4 + 2 hidden), Live Weddings (4 + 13 hidden), Collections (3 per category).

| Field | Type | Notes |
|---|---|---|
| question | post title | |
| answer | textarea | |
| `faq_group` | taxonomy | home / live-weddings / portraits / pets / custom |
| `order` | menu_order | first four in each group show; the rest sit behind "View More Questions" |

---

## 2. ACF options pages

### Site settings
| Field | Current value | Notes |
|---|---|---|
| `instagram_url` | https://www.instagram.com/shivanijaiswalart/ | live |
| `facebook_url` | *(placeholder)* | **client to supply** |
| `pinterest_url` | *(placeholder)* | **client to supply** |
| `whatsapp_number` | *(missing)* | **client to supply** — powers the float button and every "WhatsApp Shivani" CTA |
| `email` | hello@shivanijaiswalart.com | |
| `google_review_url` | *(missing)* | **client to supply** — "Leave a Google Review" button |
| `wedmegood_url` | live | still linked in Kind Words body copy, removed from the footer |

Wire every social icon, the WhatsApp float and each WhatsApp CTA to these fields.
Do not hardcode. Several are currently `href="#"` because the values do not exist yet.

### Footer
The footer is byte-identical on all 10 pages — build it once as a partial. Contents:
CTA panel (heading, copy, two buttons), four link columns, social icon row, legal bar.

---

## 3. Page templates

| Template | Source file | Notes |
|---|---|---|
| `front-page.php` | `index.html` | most complex: tilt cards, sticky list, carousel, FAQ toggle |
| `page-collections.php` | `Collections.dc.html` | six category sections, all one shared layout |
| `page-live-weddings.php` | `Live Wedding Painting.dc.html` | longest page, 17-item FAQ |
| `page-gallery.php` | `Gallery.dc.html` | three artwork grids + two split sections |
| `page-about.php` | `About.dc.html` | |
| `page-kind-words.php` | `Kind Words.dc.html` | testimonial grid |
| `archive-journal.php` | `Blog.dc.html` | 6-card grid |
| `single-journal.php` | `BlogPost.dc.html` | |
| `page-enquire.php` | `Enquire.dc.html` | contact form — see below |
| `page-privacy.php` | `Privacy.dc.html` | |

### Header partial
Headers are identical except for the active nav item — pass the current page slug
and compare, rather than duplicating markup. The Collections and Gallery items each
carry a hover/focus dropdown of section anchors.

### Enquiry form
`Enquire.dc.html` contains the field markup only; there is no submit handler.
Rebuild with the form plugin of your choice (Gravity Forms / WPForms / CF7),
keeping the existing labels, field order and the gold pill submit button.

---

## 4. Reusable components

| Component | Where | Notes |
|---|---|---|
| Section lead-in | every inner page (7 instances) | centred, 760px, `.sec-lead` |
| Four-up card grid | Home ×2, Collections ×2, Live Weddings ×2 | `.g4`, 4 → 2 → 1 |
| Category split | Collections ×6 | `.cat-split`, copy + 4/5 image |
| Gallery tile | Gallery ×32 | `.gtile`, 3/4, shared hover |
| Testimonial card | Home carousel, Kind Words grid | identical markup in both |
| Journal card | Journal ×6 | 16/10 image, eyebrow, title, excerpt, link |
| FAQ list + View More | Home, Live Weddings | first 4 visible, rest in `#faq-more[hidden]` |
| Reel scroller | every page | horizontal scroll-snap, links to Instagram |
| Footer CTA panel | every page | gradient panel, two buttons |
| WhatsApp float | **home only** — should be site-wide in WP | animated rings + green button |
