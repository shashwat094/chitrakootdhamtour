# ChitrakootDhamTour — Website

Single-page website for ChitrakootDhamTour, a Chitrakoot pilgrimage tour
operator offering Normal/VIP packages, boat tours, celebrations, guided
tours, and hotel bookings.

Live site: https://chitrakootdhamtour.in

---

## Tech Stack

Static site, no build step, no framework.

| Layer | Choice |
|---|---|
| Markup / styling | HTML5 + CSS3 (single `<style>` block, CSS custom properties for theming) |
| Icons | [Bootstrap Icons](https://icons.getbootstrap.com/) via CDN |
| Fonts | Google Fonts — Cormorant Garamond, Inter, Outfit |
| Smooth scroll | [Lenis](https://github.com/darkroomengineering/lenis) via CDN |
| Reviews backend | Firebase Firestore |
| Analytics | Firebase Analytics |
| Ads | Google AdSense |

Everything lives in one file, `index.html`. Deploy by uploading the files
as-is to any static host.

---

## File Structure

```
/
├── index.html
├── sitemap.xml
├── robots.txt
└── assets/
    └── images/
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

Image filenames are referenced by exact name throughout `index.html` — don't
rename anything in `assets/images/` without updating the references.

---

## Page Sections

1. Header — top bar (phone / offer / location / language switch) + main nav
2. Hero
3. Marquee
4. Feature grid
5. Packages — Normal (₹5,999) vs VIP (₹9,999)
6. Comparison table
7. Image slider
8. Places to Visit
9. Gallery
10. Stats
11. Boat Tours
12. Journey Timeline
13. Celebrations
14. Tour Guide booking
15. CTA banner
16. Reviews
17. Founders
18. FAQ
19. Contact
20. Policies
21. Footer
22. Floating WhatsApp button + image lightbox

---

## Editing Content

- **Prices / packages**: search `pkg-amt`
- **Phone / WhatsApp**: search `919302720332` (main line), `917024487353` /
  `917275248347` (founders' personal WhatsApp)
- **Reviews**: not hardcoded — they come from Firebase Firestore
  (`chitrakootdhamtour-2e91f` project, `reviews` collection). Moderate or
  delete via the Firebase Console.
- **FAQ answers**: also duplicated in the `FAQPage` JSON-LD block in
  `<head>` for SEO — update both places if you change one.

---

## Language Switch (EN / हिं)

Toggle lives in the top bar. Swaps `textContent`/`innerHTML` on elements
carrying `data-en` / `data-hi` (or `data-en-html` / `data-hi-html` for
content with inline tags), and remembers the visitor's choice in
`localStorage`.

Currently covers: top bar, main nav, mobile drawer nav, hero section.
Package/boat/guide copy, FAQ answers, policy text, and reviews are
English-only for now.

To add a translated string:

```html
<span data-en="English text" data-hi="हिन्दी पाठ">English text</span>
```

`setLang()` near the bottom of the file handles the swap.

---

## SEO

- `sitemap.xml` and `robots.txt` included — submit the sitemap URL in
  Google Search Console after deploying.
- Structured data: `TravelAgency`, `BreadcrumbList`, `FAQPage` (JSON-LD in
  `<head>`).
- `og:image`, canonical URL, and meta description are set for
  `chitrakootdhamtour.in` — update if deploying to a different domain.

---

## Deploying

- **Netlify / Vercel**: drag-and-drop the folder, or connect the repo.
- **GitHub Pages**: push to a repo, enable Pages on the branch.
- **cPanel / traditional hosting**: upload everything to `public_html/` via FTP.

Firebase reviews need the page served over `http(s)://`, not `file://` — use
`npx serve` or similar for local testing of that feature.

---

## Notes

- Firebase config (API key, project ID) is visible in the page source —
  that's normal for a Firebase client app. Keys are restricted by
  Firestore Security Rules, not secrecy. Double-check the rules restrict
  writes to the `reviews` collection sensibly.
- No build pipeline or component system — if the site grows into needing
  a blog, checkout flow, or multiple pages, that's a proper migration
  (Astro is a good fit), not a quick edit.
