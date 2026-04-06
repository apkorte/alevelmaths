# Spec: alevelmathsonline.co.uk Landing Page

---

## SECTION 1: SPEC

**One-line purpose**
A single static HTML landing page for alevelmathsonline.co.uk that captures UK A-level Maths and Further Maths search traffic and funnels it to physicsandmathscourses.com for booking.

**Users and use cases**

- As a Year 12 or Year 13 student searching for A-level Maths revision, I want to find a credible course page so that I can book a place on an intensive revision course.
- As a parent searching for A-level Further Maths tuition, I want to see tutor credentials and course details so that I can decide whether to book.
- As a returning visitor familiar with physicscourses.co.uk, I want a consistent brand experience so that I trust this site is the same provider.

**Requirements**

1. Single `index.html` file with no external CSS, JS, or font dependencies.
2. Hosted on GitHub Pages; canonical URL is `https://alevelmathsonline.co.uk/`.
3. Title: `A-level Maths Revision Courses | alevelmathsonline.co.uk`
4. Meta description mentions: Maths, Further Maths, online, small groups, Easter, summer, Dr Anthony Korte, Oxford, Imperial.
5. H1 text: `A-level Maths Revision Courses` (line break between "Maths" and "Revision Courses" via `<br>`).
6. Hero kicker above H1: `Online · Small Groups · UK A-level` — styled as uppercase, small, letter-spaced, white/65% opacity, with a decorative line via `::before`.
7. Hero uses `hero.png` as background image with `rgba(10, 20, 50, 0.68)` dark overlay; fallback background-color `#111`.
8. Primary brand colour `#0077bb` — matches physicscourses.co.uk.
9. System font stack: `"Helvetica Neue", Helvetica, Arial, sans-serif`.
10. Tutor section: Dr Anthony Korte with Oxford First, UCL MSc with Distinction, Imperial PhD.
11. Subjects covered: A-level Mathematics, A-level Further Mathematics.
12. Format details: small group tuition, online via Zoom, Easter/Summer/Christmas intensives.
13. Exam boards: AQA, Edexcel, OCR, WJEC Eduqas.
14. All CTAs link to `https://physicsandmathscourses.com` with `target="_blank" rel="noopener noreferrer"`.
15. FAQ section with exactly 6 questions, all expanded (no accordion/toggle).
16. Footer cross-links to `https://physicscourses.co.uk` and `https://oxfordpat.co.uk` — no labels like "coming soon".
17. Footer privacy policy link to `https://physicsandmathscourses.com/our-services/privacy-policy.html`.
18. Copyright `© 2026`.
19. `<link rel="canonical">`, `<link rel="sitemap">`, `<link rel="icon">` in `<head>`.
20. JSON-LD `@graph` in `<head>` containing: WebSite, Person, Course (×2), EducationalOrganization, FAQPage.
21. EducationalOrganization: `url` = `https://alevelmathsonline.co.uk/`, `sameAs` = `https://physicsandmathscourses.com`.
22. Mobile responsive — no horizontal scroll at 320px viewport width.
23. No portrait orientation or high-DPI media queries.
24. `body { -webkit-text-size-adjust: 100%; text-size-adjust: 100%; }`
25. No hero kicker mobile-specific overrides.
26. `lang="en-GB"` on `<html>`.
27. Open Graph and Twitter Card meta tags.
28. `<meta name="theme-color" content="#0077bb">` and referrer meta.
29. `<link rel="preload" as="image" href="hero.png">` in `<head>`.

**Edge cases**

- `hero.png` unavailable: background-color `#111` fallback ensures hero section remains readable.
- Narrow viewport (320px): all grid layouts collapse to single column; no horizontal overflow.
- JSON-LD FAQ text must match HTML FAQ text exactly — divergence would cause schema validation errors.
- CTAs must never link directly to a booking sub-page — all go to physicsandmathscourses.com root or the booking page.

**Acceptance criteria**

```
Given a visitor lands on https://alevelmathsonline.co.uk/
When the page loads
Then the H1 "A-level Maths Revision Courses" is visible above the fold on desktop and mobile

Given a visitor clicks any CTA button
When the link is followed
Then a new tab opens to physicsandmathscourses.com

Given the page is viewed at 320px viewport width
When the layout renders
Then no horizontal scrollbar appears and all text is readable

Given a search engine crawls the page
When it reads the <head>
Then it finds: canonical, JSON-LD @graph, OG tags, meta description under 160 characters, and lang="en-GB"

Given the JSON-LD FAQPage is validated
When checked against schema.org
Then all 6 Question/Answer pairs are present and match the visible HTML FAQ text exactly

Given hero.png fails to load
When the browser renders the hero section
Then the section displays with background-color #111 and the overlay, remaining fully readable
```

---

## SECTION 2: PLAN

**Stack and architecture**

Single static HTML file. All CSS inline in `<style>` block in `<head>`. All JSON-LD in `<script type="application/ld+json">` block in `<head>`. No JavaScript. No build step. GitHub Pages serves `index.html` directly.

CSS architecture matches physicscourses.co.uk exactly:
- CSS custom properties (`--blue`, `--ink`, `--bg-grey`, etc.) in `:root`
- Section-level class naming (`.hero`, `.about`, `.format`, `.faq`, `footer`)
- Responsive grid via CSS Grid with `@media (min-width: N)` breakpoints only
- No CSS framework

**Data model changes**

None — static file only.

**API contracts**

None — static file only. All outbound links are plain `<a>` elements.

**Patterns to follow**

Follow physicscourses.co.uk (`/home/owner/sites/physicscourses/index.html`) exactly for:
- CSS custom property names and values
- Section HTML structure (header → hero → main sections → footer)
- `.section-label` uppercase kicker above each `<h2>`
- `.credentials` / `.credential-row` / `.credential-label` / `.credential-value` pattern for tutor credentials
- `.format-grid` / `.format-item` / `.fi-label` / `.fi-value` / `.fi-detail` for course format
- `.faq-list` / `.faq-item` / `.faq-q` / `.faq-a` for FAQ
- `.btn` and `.btn--outline` for CTAs
- Footer structure: CTA block → rule → cross-links → meta row
- JSON-LD `@graph` structure with `@id` on WebSite, Person, Course, EducationalOrganization
- `hasCourseInstance` with `Offer` for course dates and pricing
- `hasCredential` as `EducationalOccupationalCredential` typed objects
- Person `image` as `ImageObject`

**Differences from physicscourses.co.uk:**
- Subject: Mathematics / Further Mathematics (not Physics)
- Exam boards section replaces syllabus/module cards
- No `hasCourseInstance` pricing details needed unless known [ASSUMPTION: omit specific pricing from schema; add to copy as "see physicsandmathscourses.com for current pricing"]
- Two Course schema nodes (A-level Maths + A-level Further Maths) rather than one
- Hero overlay: `rgba(10, 20, 50, 0.68)` (slightly different from physics — as specified)
- Hero image: `hero.png` (not `hero.jpeg`)

**Testing strategy**

Manual verification only (static HTML):
1. Open in browser at 320px, 768px, 1200px — check layout
2. Click all CTAs — verify correct destination and `target="_blank"`
3. Validate JSON-LD via Google Rich Results Test
4. Check meta description length < 160 characters
5. Verify FAQ HTML text matches JSON-LD FAQ text exactly

**Security and performance constraints**

- All external links use `rel="noopener noreferrer"`
- No inline event handlers
- No user input, no forms, no data collection on this page
- `<meta name="referrer" content="strict-origin-when-cross-origin">`
- Hero image is `hero.png` — [ASSUMPTION: file is already optimised; convert to WebP post-launch if > 200KB]
- Preload hero image for LCP optimisation

---

## SECTION 3: TASKS

## Task 1: HTML scaffolding and `<head>`

**What to build:** Complete `<head>` block including: `<!DOCTYPE html>`, `<html lang="en-GB">`, charset, viewport, title, meta description (< 160 chars), canonical, sitemap, favicon, preload, theme-color, referrer, Open Graph tags (og:type, og:locale, og:url, og:title, og:description, og:site_name, og:image, og:image:secure_url, og:image:alt), Twitter Card tags (card, site, title, description, image, image:alt). OG image: `[ASSUMPTION: use https://physicsandmathscourses.com/images/anthony-korte.jpg — same as physicscourses.co.uk]`. Twitter site: `[ASSUMPTION: @PhysMathCourses — same handle as physicscourses.co.uk]`.

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html` (create)

**Acceptance criteria:**
1. `<title>` is exactly `A-level Maths Revision Courses | alevelmathsonline.co.uk`
2. `<meta name="description">` is under 160 characters and includes: Maths, Further Maths, online, small groups, Easter, summer, Dr Anthony Korte, Oxford, Imperial
3. Canonical href is `https://alevelmathsonline.co.uk/`

**Dependencies:** none

---

## Task 2: CSS — base, typography, layout, components

**What to build:** Full inline `<style>` block matching physicscourses.co.uk CSS architecture. Must include: reset, `:root` custom properties (identical colour palette), `html`/`body` base styles with text-size-adjust, typography helpers (`.section-label`, `h2`, `h3`, `p`), layout (`.container`, `section`, alternating `background`), `.site-header`, `.hero` (with `hero.png` background, `rgba(10, 20, 50, 0.68)` overlay, `::before` pseudo-element), `.hero-kicker` (inline-flex, 0.65rem, weight 800, letter-spacing 0.22em, uppercase, white/65%, `::before` decorative line), `.btn` / `.btn--outline`, `.credentials` block, `.format-grid` / `.format-item`, `.faq-list` / `.faq-item`, footer styles, `.visually-hidden` utility. No portrait/high-DPI media queries. No kicker mobile override.

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html`

**Acceptance criteria:**
1. Page renders without horizontal scroll at 320px viewport
2. Hero section displays with dark overlay over hero.png
3. All colour values use `--blue: #0077bb` custom property — no hardcoded hex for brand colour

**Dependencies:** Task 1

---

## Task 3: JSON-LD schema `@graph`

**What to build:** Single `<script type="application/ld+json">` block in `<head>` containing a `@graph` array with these nodes:

- **WebSite**: `@id` = `https://alevelmathsonline.co.uk/#website`, `url`, `name` = "alevelmathsonline.co.uk", `description`
- **Person**: `@id` = `https://physicsandmathscourses.com/#anthony-korte`, `name` = "Dr Anthony Korte", `honorificPrefix`, `jobTitle`, `description`, `image` as ImageObject, `url` = `https://physicsandmathscourses.com`, `sameAs` array (LinkedIn `www.linkedin.com/...`, Twitter), `affiliation` referencing EducationalOrg `@id`, `alumniOf` (Oxford, UCL, Imperial), `hasCredential` as three `EducationalOccupationalCredential` objects (Oxford First, UCL MSc Distinction, Imperial PhD), `award` array, `memberOf` Institute of Physics, `knowsAbout` array
- **Course** (A-level Maths): `@id` = `https://alevelmathsonline.co.uk/#course-maths`, `name`, `description`, `provider` and `instructor` referencing Person `@id`, `courseMode` = "online", `inLanguage` = "en-GB", `educationalLevel`, `coursePrerequisites`, `url` = `https://alevelmathsonline.co.uk/`
- **Course** (A-level Further Maths): `@id` = `https://alevelmathsonline.co.uk/#course-further-maths`, same pattern
- **EducationalOrganization**: `@id` = `https://physicsandmathscourses.com/#organization`, `name` = "Physics and Maths Courses", `url` = `https://physicsandmathscourses.com`, `sameAs` = `https://alevelmathsonline.co.uk/`
- **FAQPage**: `mainEntity` array of exactly 6 Question/Answer pairs — text must match HTML FAQ verbatim

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html`

**Acceptance criteria:**
1. JSON-LD is valid JSON (no syntax errors)
2. All 6 FAQ answers in JSON-LD are character-for-character identical to the HTML FAQ answers (strip HTML entities to plain text equivalents for comparison)
3. EducationalOrganization `url` = `https://physicsandmathscourses.com` and `sameAs` = `https://alevelmathsonline.co.uk/`

**Dependencies:** Task 1

---

## Task 4: Page body — header, hero, ESAT/maths intro section

**What to build:**
- `<header class="site-header">`: wordmark `alevelmathsonline.co.uk` (with `.co.uk` in `--blue`), CTA button linking to physicsandmathscourses.com
- `<section class="hero">`: kicker span, H1 `A-level Maths<br>Revision Courses`, hero-sub paragraph, CTA button
- First `<main>` section: "What is an A-level Maths revision course?" — 2-column grid on desktop, lead paragraph left, subject list right (Mathematics, Further Mathematics with brief descriptions). Section label: "The Courses". `[ASSUMPTION: no module breakdown cards needed — subject list replaces the physics module cards]`

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html`

**Acceptance criteria:**
1. H1 text is `A-level Maths Revision Courses` with line break rendering correctly
2. Hero kicker reads `Online · Small Groups · UK A-level`
3. All CTAs in this section open physicsandmathscourses.com in a new tab

**Dependencies:** Tasks 2, 3

---

## Task 5: Page body — tutor, format, exam boards sections

**What to build:**
- **About section**: section-label "Your Tutor", H2 "About Dr Anthony Korte", about-lede paragraph, `.credentials` DL with rows: Undergraduate (Oxford First), Postgraduate (UCL MSc Distinction), Doctorate (Imperial PhD), Research (peer-reviewed publications), Teaching (IOP membership, experience)
- **Format section**: section-label "How It Works", H2 "Course Format", format-grid with 4 items: Group size (small groups), Delivery (online via Zoom), Timing (Easter / Summer / Christmas), Focus (exam technique and past papers). Below grid: format-detail paragraph mentioning all three intensives and linking to physicsandmathscourses.com for pricing/dates
- **Exam boards section**: section-label "Exam Boards", H2 "All Major Exam Boards Covered", brief paragraph, then a simple grid or list showing AQA, Edexcel, OCR, WJEC Eduqas. `[ASSUMPTION: styled as a 4-column pill/badge grid on desktop, single column on mobile]`

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html`

**Acceptance criteria:**
1. All three qualifications appear in the credentials block with correct institution names
2. All four exam boards are listed: AQA, Edexcel, OCR, WJEC Eduqas
3. Format section links to physicsandmathscourses.com for pricing/dates

**Dependencies:** Task 4

---

## Task 6: Page body — testimonials, FAQ, footer

**What to build:**
- **Testimonials section**: `[ASSUMPTION: use 2 placeholder testimonials consistent with maths context — e.g. A-level Maths grade improvement or university offer. User to replace with real quotes.]` 2-column grid matching physicscourses.co.uk `.testi` / `.testi__quote` / `.testi__attr` pattern. Attribution as `<p class="testi__attr">` not `<footer>` (avoids global footer CSS bleed).
- **FAQ section**: section-label "Common Questions", H2 "Frequently Asked Questions", `<dl class="faq-list">` with exactly 6 `<div class="faq-item">` entries — all expanded, no toggle. Questions:
  1. What is an A-level Maths revision course?
  2. Do you offer Further Maths tuition?
  3. Which exam boards do you cover for Maths?
  4. How does online Maths tuition work?
  5. When are the Easter and summer Maths revision courses?
  6. Is the course suitable for Year 12 and Year 13?
- **Footer**: CTA block (label + heading + button to physicsandmathscourses.com), footer-rule div, footer-links (A-level Physics courses → physicscourses.co.uk; ESAT & Oxford PAT preparation → oxfordpat.co.uk — both `target="_blank" rel="noopener noreferrer"`), footer-meta row (© 2026 physicsandmathscourses.com, tagline, privacy policy link)

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html`

**Acceptance criteria:**
1. FAQ contains exactly 6 items, all visible without interaction
2. Footer contains both cross-links (physicscourses.co.uk and oxfordpat.co.uk) with no "coming soon" labels
3. Testimonial attribution uses `<p class="testi__attr">` not `<footer>`

**Dependencies:** Tasks 4, 5

---

## Task 7: Final QA pass

**What to build:** Review the completed `index.html` for:
- Meta description character count (must be < 160)
- JSON-LD FAQ answers match HTML FAQ answers verbatim
- All external links have `target="_blank" rel="noopener noreferrer"`
- No hardcoded `#0077bb` outside `:root` (all uses via `var(--blue)`)
- No `<footer>` element inside blockquotes (use `<p>` for attribution)
- `hero.png` referenced correctly in CSS and preload
- `lang="en-GB"` on `<html>`
- Valid JSON in `<script type="application/ld+json">`
- No portrait/high-DPI media queries present
- No horizontal scroll at 320px

**Files likely affected:** `/home/owner/sites/alevelmaths/index.html`

**Acceptance criteria:**
1. Zero items from the QA checklist fail
2. File is saved at `/home/owner/sites/alevelmaths/index.html`
3. File contains no `</html>` content after the closing `</html>` tag

**Dependencies:** Task 6

---

## Review checkpoint

Before handing to a coding agent: open the completed `index.html` in a browser, resize to 320px, click every CTA to confirm physicsandmathscourses.com opens in a new tab, and paste the JSON-LD block into Google's Rich Results Test to confirm FAQPage and Course schemas are valid.

---

## Assumptions to review

1. [ASSUMPTION: OG image is https://physicsandmathscourses.com/images/anthony-korte.jpg — same portrait as physicscourses.co.uk] — Impact: LOW
   Correct this if: a maths-specific image is preferred

2. [ASSUMPTION: Twitter handle is @PhysMathCourses] — Impact: LOW
   Correct this if: a different handle should be used

3. [ASSUMPTION: no specific course pricing in schema or copy — link to physicsandmathscourses.com for current pricing] — Impact: MEDIUM
   Correct this if: you want to add hasCourseInstance with Offer/pricing to the schema

4. [ASSUMPTION: no specific course dates in schema or copy — link to physicsandmathscourses.com for current dates] — Impact: MEDIUM
   Correct this if: you want specific Easter/Summer/Christmas 2026 dates added

5. [ASSUMPTION: exam boards section styled as pill/badge grid — 4 columns desktop, 1 column mobile] — Impact: LOW
   Correct this if: a different layout is preferred

6. [ASSUMPTION: 2 placeholder maths testimonials used — user to replace with real quotes] — Impact: MEDIUM
   Correct this if: you have real testimonials to include now

7. [ASSUMPTION: no hasCourseInstance nodes needed without confirmed dates/pricing] — Impact: MEDIUM
   Correct this if: dates are confirmed (Easter 2027 / Summer 2027 / Christmas 2026)

8. [ASSUMPTION: hero.png is already web-optimised] — Impact: MEDIUM
   Correct this if: file is > 200KB — convert to WebP before or after launch
