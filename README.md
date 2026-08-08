# ChitrakootDhamTour — Website

Premium single-page website for **ChitrakootDhamTour**, a Chitrakoot pilgrimage
tour operator offering Normal/VIP packages, boat tours, celebrations, guided
tours, and hotel bookings.

Live site: https://chitrakootdhamtour.in

---

## 1. Tech Stack

Pure static site — no build step, no framework, no bundler. Deploy by
uploading the files as-is to any static host.

| Layer | Choice |
|---|---|
| Markup / styling | Hand-written HTML5 + CSS3 (single `<style>` block, CSS custom properties for theming) |
| Icons | [Bootstrap Icons](https://icons.getbootstrap.com/) via CDN |
| Fonts | Google Fonts — Cormorant Garamond (display serif), Inter (body), Outfit (loaded, available for future use) |
| Scroll | Native browser scrolling — no scroll-hijacking library. An earlier revision added Lenis (eased smooth-scroll); it was removed because eased/decoupled scroll reads as *input lag* to most users, which is exactly what it was doing. Native scroll is instant and correct. |
| Reviews backend | Firebase Firestore (client SDK, ES module import from `gstatic.com`) |
| Analytics | Firebase Analytics |
| Ads | Google AdSense |

Everything ships in **one file**, `index.html`. This is intentional for a
site this size — it's the fastest possible deploy (drag-and-drop to any
static host, no `npm install`, no CI) and keeps every dependency visible in
one place. If the site grows substantially (new pages, a CMS, checkout flow),
migrating to a proper framework (Astro/Next/Vite+React) would be the natural
next step — see "Possible Next Steps" below.

---

## 2. File Structure

```
/
├── index.html          → entire site: markup, styles, and scripts
├── sitemap.xml          → search engine sitemap
├── robots.txt            → crawler rules, points to sitemap
└── assets/
    └── images/           → all site imagery (see list below)
        ├── bg.jpeg
        ├── boat-aarti.jpg
        ├── dharmendra.png
        ├── gupt.jpg
        ├── hanuman.jpg
        ├── hotel1.jpg
        ├── janki.jpg
        ├── kamadgiri.jpg
        ├── logo.png
        ├── ramghat.jpg
        ├── sati.jpg
        ├── shashwat.jpeg
        ├── vipboat.jpeg
        └── viphotel.jpg
```

Image filenames are referenced by exact name throughout `index.html` — do
not rename files in `assets/images/` without updating every reference.

---

## 3. Sections (in page order)

1. **Header** — top utility bar (phone / offer / location / language switch) + main nav, both inside one fixed `<header>` wrapper so they always sit flush against each other regardless of screen size or font-loading timing. Collapses the top bar on scroll for a cleaner sticky nav.
2. **Hero** — full-viewport intro with parallax orb, animated stats, dual CTA.
3. **Marquee** — infinite scrolling highlight strip.
4. **Feature grid** — 6 icon cards (VIP Darshan, Hotels, Boat Rides, Guides, Meals, Support).
5. **Packages** — Normal (₹5,999) vs VIP (₹9,999), WhatsApp-linked booking CTAs.
6. **Comparison table** — side-by-side feature diff between packages.
7. **Image slider** — auto-rotating hero-style slideshow of sacred sites.
8. **Places to Visit** — bento-style photo grid, opens lightbox on click.
9. **Gallery** — filterable masonry grid (Temples / River & Boats / Hotels).
10. **Stats** — animated counters (triggers on scroll into view).
11. **Boat Tours** — Normal vs VIP boat packages.
12. **Journey Timeline** — 6-step visual walkthrough of what a tour day looks like.
13. **Celebrations** — birthday parties & pre-wedding shoots on the river.
14. **Tour Guide booking** — personal vs group guide packages.
15. **CTA banner** — first-booking discount push.
16. **Reviews** — Firestore-backed submission form + live-loaded review cards.
17. **Founders** — team bios with social links.
18. **FAQ** — accordion, keyboard accessible.
19. **Contact** — phone/hours/location + WhatsApp CTA card.
20. **Policies** — Terms, Privacy, Refund, Disclaimer.
21. **Footer** — sitemap-style link columns, newsletter, back-to-top.
22. Floating **WhatsApp button** + **image lightbox** (global, not tied to one section).

---

## 4. Editing Content

Everything is plain HTML — search for the text you want to change and edit
it directly. A few things to know:

- **Prices / packages**: search for `pkg-amt` — each package card has one.
- **Phone numbers / WhatsApp links**: search for `919302720332` (main line)
  and `917024487353` / `917275248347` (founders' personal WhatsApp).
- **Reviews**: these are NOT hardcoded — they come from Firebase Firestore
  (`chitrakootdhamtour-2e91f` project, `reviews` collection) and load on
  page load via the script at the bottom of the file. To moderate or delete
  a review, use the Firebase Console, not this file.
- **FAQ answers**: also duplicated in the `FAQPage` JSON-LD block near the
  top of `<head>` for SEO rich results — if you change an FAQ answer,
  update both places so Google doesn't index a mismatched answer.

---

## 5. Language Switch (EN / हिं)

A functional bilingual toggle lives in the top bar (`EN` / `हिं` buttons).
It works by swapping `textContent`/`innerHTML` on any element carrying
`data-en` / `data-hi` (or `data-en-html` / `data-hi-html` for content with
inline tags like `<br>` or `<em>`) attributes, and remembers the visitor's
choice in `localStorage`.

**Current coverage:** top bar, main nav, mobile drawer nav, and the hero
section are fully bilingual.

**Not yet translated:** package/boat/guide card copy, FAQ answers, policy
text, and reviews remain English-only. This was a deliberate scope
decision — those sections contain pricing, refund terms, and legal
language where a mistranslation is a real liability, and machine
translation isn't a safe way to produce that copy. If you want full-site
Hindi coverage, the pattern is already built — it's a matter of adding
`data-en`/`data-hi` pairs to the remaining text nodes, ideally with
Hindi copy reviewed by a fluent speaker before publishing, especially for
the Policies section.

To add a new translated string anywhere in the file:
```html
<span data-en="English text" data-hi="हिन्दी पाठ">English text</span>
```
The visible text between the tags is the English fallback / initial
render; `setLang()` (defined near the bottom of the file) handles the swap.

---

## 6. Google AdSense

The AdSense loader script (`ca-pub-6041372146127557`) is in `<head>` — that
part was already correct. On its own, though, that one script tag **does
not display any ads**; it only loads Google's library so the page is
*capable* of showing ads once one of the two paths below is completed.

Two manual ad units have now been wired in, at policy-safe spots (not
stacked near each other, not next to booking buttons/forms, each with an
"Advertisement" label and reserved space so it doesn't cause layout shift
when it loads):

- **Ad slot 1** — after the Gallery section, before Boat Tour.
- **Ad slot 2** — after the Founders section, before FAQ.

Both currently use a **placeholder** `data-ad-slot` value
(`0000000001` / `0000000002`). These will not serve real ads as-is —
`data-ad-slot` is an ID Google generates per ad unit, specific to your
AdSense account, and there's no way for me to know or invent a valid one.

**To make ads actually appear, do one of these:**

1. **Manual ad units (what's already wired in)** — Log into
   [AdSense](https://www.google.com/adsense) → Ads → By ad unit → create a
   "Display ad", copy the `data-ad-slot` value it gives you, and paste it
   over the two placeholders in `index.html` (search for `0000000001` and
   `0000000002`). No other code changes needed.
2. **Auto ads instead** — In AdSense, under Ads → Overview, turn on
   "Auto ads" for `chitrakootdhamtour.in`. Google will then place ads
   automatically across the page using the loader script that's already
   here. If you go this route, you can delete the two manual `.ad-wrap`
   blocks from `index.html` (search `AD SLOT 1` / `AD SLOT 2`) to avoid
   double-serving ads in the same area.

**Also required, independent of either path above:** your AdSense account
and this domain need to be **approved** by Google (Sites → Add site →
wait for review) before any ad — manual or auto — will render anything
other than blank space. This is a Google-side approval step, not something
fixable in the code.

**`ads.txt`** is included at the project root. This file has to be served
at exactly `https://chitrakootdhamtour.in/ads.txt` — the domain root, not
inside a subfolder — or AdSense will keep reporting "Not found" no matter
what's in it. After deploying, load that URL directly in a browser and
confirm you see the one line of text; Google typically takes anywhere
from a few hours up to a day to re-crawl and clear the warning after that.

## 7. SEO

- `sitemap.xml` and `robots.txt` are included — after deploying, submit the
  sitemap URL in Google Search Console.
- Structured data included: `TravelAgency`, `BreadcrumbList`, `FAQPage`
  (all in `<head>` as JSON-LD).
- `og:image`, canonical URL, and meta description are already set for
  `chitrakootdhamtour.in` — if you deploy to a different domain, update
  these.

---

## 8. Deploying

Any static host works — no build step required:

- **Netlify / Vercel**: drag-and-drop the folder, or connect the repo.
- **GitHub Pages**: push to a repo, enable Pages on the branch.
- **Traditional hosting (cPanel etc.)**: upload everything to `public_html/`
  via FTP.

Nothing needs to be compiled. If it opens correctly on your local machine
by double-clicking `index.html`, it'll work in production too (Firebase
reviews require the page to be served over `http(s)://`, not `file://`,
so use `npx serve` or similar for local testing of that specific feature).

---

## 9. Performance Notes

Two things were tried in an earlier revision and then removed because they
hurt more than they helped — noting this so nobody re-adds them by accident:

- **Lenis smooth-scroll** was added, then removed. Eased/"smoothed" scroll
  libraries decouple your scroll input from the visual response by design —
  that's the whole feature. It reads as lag/delay to most people, because
  functionally it is one. Native browser scrolling is now used everywhere;
  anchor links (`#packages` etc.) still animate via plain CSS
  `scroll-behavior: smooth`, which is instant-feeling and has no JS cost.
- **Cursor-glow follower** (a blurred circle that tracked the mouse) was
  removed per feedback that it looked bad, and it also added a `pointermove`
  listener writing to the DOM on every mouse tick — never necessary.

Also tightened for real:

- The nav's `backdrop-filter` blur was reduced from `24px` to `14px`. A
  `position: fixed` element with backdrop-filter has to keep resampling
  whatever's behind it as the page scrolls underneath — the larger the
  blur radius, the more expensive that resample is per frame. `14px`
  keeps the glass look at a fraction of the cost.
- The scroll listener (which drives the header collapse + progress bar)
  is now throttled to `requestAnimationFrame`, so it can't fire — and
  force a layout read — more than once per rendered frame, no matter how
  fast the scroll event fires.

Everything else that responds to scroll (section reveals, the animated
counters) already used `IntersectionObserver`, which is the browser-native,
off-main-thread-friendly way to do this and was never a lag source.

## 10. Known Scope Limits (read before assuming "everything")

Being direct about what this codebase is and isn't, so nothing here is a
surprise later:

- **Single-file HTML, not a framework app.** There's no component system,
  no build pipeline, no TypeScript. That's a legitimate choice for a
  site this size, but if the business grows into needing a blog, a
  proper booking/checkout system, or multi-page routing, that's a real
  migration project, not a CSS tweak.
- **Language switch covers primary navigation and hero only** (see §5).
- **Firebase config (API key, project ID) is visible in the page source.**
  This is normal and expected for a Firebase *client* app — these keys are
  meant to be public and are restricted by Firestore Security Rules, not
  by secrecy. If you haven't already, double check your Firestore rules
  restrict writes to the `reviews` collection sensibly (e.g. required
  fields, rate limiting) so the public write endpoint can't be abused.
- **No automated test suite.** Every change in this update was verified
  with static analysis (JS syntax checking via Node, CSS AST/property
  validation, link/ID/alt-text audits) rather than a browser test runner,
  since this project has none set up. Visually spot-check after deploying,
  especially on real mobile devices.

---

## 11. Possible Next Steps

In rough order of value-for-effort, if you want to keep investing in this:

1. **Expand the language switch** to full-site coverage, with Hindi
   copy reviewed by a fluent speaker (especially Policies/FAQ).
2. **Lighthouse audit** on the live deployed URL (scores depend on real
   network/hosting conditions, not just code — can't be meaningfully
   estimated from source alone).
3. **Migrate to a component framework** (Astro is a strong fit here —
   ships static HTML like this file, but with real components) once the
   site needs more than one page or a CMS-backed blog.
4. **Firestore Security Rules review** for the public review-submission
   endpoint (see §8).

---

*Last updated as part of the header-architecture fix, hover/motion polish,
and bilingual navigation pass.*
