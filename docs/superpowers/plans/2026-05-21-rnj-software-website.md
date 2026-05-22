# RnJ Software Website Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the static marketing website for RnJ Software LLC (`rnj-software.com`) to serve as the company's public presence and satisfy Apple Developer business verification.

**Architecture:** A static, multi-page site built with Astro. Shared layout/header/footer components are defined once and reused across 8 pages. The site builds to plain static HTML hosted free on GitHub Pages, with a working contact form powered by Web3Forms (no backend). The visual design is produced with the `frontend-design` skill.

**Tech Stack:** Astro, Tailwind CSS (via `@astrojs/` integration), `@astrojs/sitemap`, GitHub Actions, GitHub Pages, Web3Forms.

---

## Notes for the implementer

- This is a static brochure website, not a TDD codebase. The "test" for each task is: the production build succeeds and the page renders correctly in the dev server. Verification commands are given per task.
- **Visual design:** When building any page or visual component, invoke the `frontend-design` skill and follow it. The plan specifies *content, structure, and requirements*; `frontend-design` produces the polished markup and styling. Do not hand-write generic/templated markup.
- **Brand:** The RnJ logo is strict black & white (charcoal `~#1a1a1a`, white, a `</>` code-screen icon). Build a modern technical aesthetic: charcoal base, off-white surfaces, one sharp accent color chosen during Task 2.
- All copy below is real, usable text. Anything the owner may want to tweak later is still complete enough to ship.
- Working directory is the repo root: `~/Documents/projects/rnj-software-website` (git already initialized, branch `main`, with the design spec committed).
- Commit after every task with the message shown.

---

## Task 1: Scaffold the Astro project

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tsconfig.json`, `src/env.d.ts`
- Create: `public/.nojekyll`

- [ ] **Step 1: Scaffold Astro into the existing directory**

Run from the repo root:
```bash
npm create astro@latest . -- --template minimal --typescript strict --no-install --no-git --skip-houston
```
When prompted that the directory is not empty, choose to continue (the existing `docs/`, `.git/`, `.gitignore` must be preserved).

- [ ] **Step 2: Add the Tailwind and sitemap integrations**

```bash
npx astro add tailwind --yes
npx astro add sitemap --yes
npm install
```

- [ ] **Step 3: Set the site config**

Replace `astro.config.mjs` with:
```js
import { defineConfig } from 'astro/config';
import sitemap from '@astrojs/sitemap';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  site: 'https://rnj-software.com',
  integrations: [sitemap()],
  vite: { plugins: [tailwindcss()] },
});
```
(If `astro add tailwind` already wired the Vite plugin, keep its version and just ensure `site` and `sitemap()` are present.)

- [ ] **Step 4: Add `public/.nojekyll`**

Create an empty file `public/.nojekyll` so GitHub Pages does not run Jekyll over the Astro output.

- [ ] **Step 5: Verify the build**

Run: `npm run build`
Expected: build completes with no errors; a `dist/` directory is produced.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "Scaffold Astro project with Tailwind and sitemap"
```

---

## Task 2: Brand assets and design system

**Files:**
- Create: `public/logo.png`, `public/favicon.svg`
- Create/modify: `src/styles/global.css`

- [ ] **Step 1: Copy the logo into the project**

```bash
cp ~/Downloads/RnJ_Software_logo.png public/logo.png
```

- [ ] **Step 2: Establish the design system with the frontend-design skill**

Invoke the `frontend-design` skill. Define, for the whole site:
- Color palette: charcoal/near-black base (`~#1a1a1a`), off-white surface(s), one sharp accent color, plus neutral grays. Record the chosen accent as a CSS variable.
- Typography: a modern, technical sans-serif pairing (heading + body). Use a self-hosted or Google Fonts choice loaded in the base layout.
- Spacing, container width, and radius conventions.

Write these as CSS custom properties / Tailwind theme tokens in `src/styles/global.css` so every page draws from one source.

- [ ] **Step 3: Create a favicon**

Create `public/favicon.svg` — a simple monochrome mark derived from the logo (the `</>` motif or "RNJ" lettermark). Keep it legible at 16px.

- [ ] **Step 4: Verify**

Run: `npm run build`
Expected: build succeeds.

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Add brand assets and site design system"
```

---

## Task 3: Base layout, header, and footer

**Files:**
- Create: `src/layouts/BaseLayout.astro`
- Create: `src/components/Header.astro`
- Create: `src/components/Footer.astro`

- [ ] **Step 1: Build `BaseLayout.astro`**

Invoke `frontend-design`. The layout accepts props `title` and `description` and renders:
- `<head>`: charset, viewport, `<title>{title} — RnJ Software</title>`, `<meta name="description">`, canonical link, favicon link, font loading, global stylesheet import.
- Open Graph + Twitter Card meta tags (`og:title`, `og:description`, `og:type=website`, `og:url`, `og:image` → `/og-image.png`, `twitter:card=summary_large_image`).
- `<body>`: `<Header />`, a `<slot />` for page content, `<Footer />`.

- [ ] **Step 2: Build `Header.astro`**

Sticky/top header with the RnJ logo (links to `/`) and primary nav: Home, About, Services, Products, Contact. Include an accessible, keyboard-navigable mobile menu (hamburger) for small screens. The current page link is visually marked.

- [ ] **Step 3: Build `Footer.astro`**

Footer containing:
- Legal name: **RnJ Software LLC**
- Mailing address: 5944 Coral Ridge Dr #1091, Coral Springs, FL 33076
- Email: `support@rnj-software.com` (mailto link)
- Footer nav: links to all pages including Privacy Policy and Terms of Service
- Copyright line: `© 2025 RnJ Software LLC. All rights reserved.` (year rendered dynamically as `2025–{currentYear}` once past 2025).

- [ ] **Step 4: Verify**

Temporarily wrap a placeholder page in `BaseLayout`, run `npm run dev`, and confirm the header and footer render on desktop and mobile widths with working nav links.

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Add base layout, header, and footer components"
```

---

## Task 4: Home page

**Files:**
- Create: `src/pages/index.astro`
- Create (as needed): `src/components/Hero.astro`, `src/components/ServiceCard.astro`

- [ ] **Step 1: Build the home page with `frontend-design`**

`index.astro` uses `BaseLayout` (title: `Software Engineering & Consulting`, description: a one-line summary of RnJ). Sections, top to bottom:

1. **Hero** — H1 wordmark "RnJ Software"; subhead: *"A software engineering and consulting firm based in Coral Springs, Florida."*; supporting line: *"We design, build, and ship custom software — from mobile apps to cloud platforms — for businesses that need it done right."*; primary CTA button "Get in touch" → `/contact`.
2. **Services summary** — heading "What we do"; the five services as cards (reuse `ServiceCard.astro`), each with name + one-line description (full copy in Task 6); a link "Explore our services" → `/services`.
3. **About teaser** — short paragraph: *"RnJ Software LLC is a software engineering and consulting firm founded in 2025. We partner with businesses to build software that works — and we build our own products, too."*; link "About RnJ Software" → `/about`.
4. **Products callout** — brief mention of Vanihla: *"We're the team behind Vanihla, an AI-powered app that turns cooking videos into recipes."*; link "See our products" → `/products`.
5. **Contact CTA** — closing band with a "Contact us" button → `/contact`.

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; home page renders all five sections, responsive on mobile and desktop, all links resolve.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add home page"
```

---

## Task 5: About page

**Files:**
- Create: `src/pages/about.astro`

- [ ] **Step 1: Build the About page with `frontend-design`**

`about.astro` uses `BaseLayout` (title: `About`). Content:

- Heading: "About RnJ Software"
- Intro: *"RnJ Software LLC is a software engineering and consulting firm based in Coral Springs, Florida. Founded in 2025, we partner with businesses to design, build, and maintain custom software — web and mobile applications, cloud infrastructure, and AI-powered systems."*
- Approach section: *"Our approach is straightforward: understand the problem, build the right thing, and ship it. We bring engineering discipline to every project, whether we're developing software for a client or building our own products."*
- A short "What sets us up" / values block — three brief points: **Engineering rigor** (we build software to last), **End-to-end ownership** (from architecture to deployment), **Practical delivery** (working software, shipped).
- Company facts block (also good for verification): legal name **RnJ Software LLC**, a Florida limited liability company; founded 2025; based in Coral Springs, Florida.

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; About page renders and is responsive.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add about page"
```

---

## Task 6: Services page

**Files:**
- Create: `src/pages/services.astro`

- [ ] **Step 1: Build the Services page with `frontend-design`**

`services.astro` uses `BaseLayout` (title: `Services`). Heading "Services" with a short intro line. Then the five services, each as a clear section/card with the copy below:

1. **Custom Software Development** — "We build web applications and backend systems tailored to your business — from initial architecture through deployment and ongoing maintenance."
2. **Mobile App Development (iOS & Android)** — "Native and cross-platform mobile apps designed for performance and a polished experience on both iOS and Android."
3. **AI & Machine Learning Integration** — "We integrate modern AI and machine learning into products and workflows — from large language models to custom data pipelines."
4. **Cloud Architecture & DevOps** — "Scalable, secure cloud infrastructure and automated deployment pipelines built on modern cloud platforms."
5. **Technical Consulting & Strategy** — "Architecture reviews, technology strategy, and hands-on guidance to help your team make sound engineering decisions."

End with a CTA to `/contact`.

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; all five services render; responsive.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add services page"
```

---

## Task 7: Products page

**Files:**
- Create: `src/pages/products.astro`

- [ ] **Step 1: Build the Products page with `frontend-design`**

`products.astro` uses `BaseLayout` (title: `Products`). This page must clearly establish RnJ as Vanihla's owner — it is the key page for Apple verification. Content:

- Heading: "Products"
- Intro: *"Alongside our client work, RnJ Software builds and operates its own software products."*
- **Featured Vanihla showcase** — a rich section (not a lonely card):
  - Product name: **Vanihla**
  - Tagline: *"AI that turns cooking videos into recipes."*
  - Description: *"Vanihla extracts complete, structured recipes from cooking videos using AI — so what you watch becomes something you can actually cook."*
  - Explicit ownership line: **"Vanihla is a product of RnJ Software LLC."**
  - Links: "Visit vanihla.com" → `https://vanihla.com` (opens in new tab, `rel="noopener"`); an "Download on the App Store" link — include it but commented/disabled with a note until the App Store URL exists.
  - If a Vanihla screenshot/asset is available, include it; otherwise use a clean branded panel.
- A quiet closing line: *"More products are in development."*

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; Vanihla showcase renders; the ownership line is present; `vanihla.com` link works.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add products page featuring Vanihla"
```

---

## Task 8: Contact page with Web3Forms

**Files:**
- Create: `src/pages/contact.astro`

- [ ] **Step 1: Build the Contact page with `frontend-design`**

`contact.astro` uses `BaseLayout` (title: `Contact`). Content:

- Heading: "Get in touch"
- Intro: *"Have a project in mind or a question for our team? Send us a message and we'll get back to you."*
- **Contact form** posting to Web3Forms (`https://api.web3forms.com/submit`):
  - Hidden input `access_key` set to the Web3Forms key. Until the owner provides the real key, use the literal value `WEB3FORMS_ACCESS_KEY_PLACEHOLDER` and add an HTML comment: `<!-- Replace with the real Web3Forms access key before launch -->`.
  - Visible fields: Name (text, required), Email (email, required), Message (textarea, required).
  - A honeypot field named `botcheck` (hidden via CSS, not `type=hidden`) for spam protection.
  - A hidden `subject` field: "New message from rnj-software.com".
  - On submit, show an inline success message without a full page reload (small progressive-enhancement script that POSTs via `fetch` and displays success/error states; the form still degrades to a normal POST if JS is disabled).
- **Direct contact details** shown alongside the form:
  - Email: `support@rnj-software.com` (mailto link)
  - Mailing address: RnJ Software LLC, 5944 Coral Ridge Dr #1091, Coral Springs, FL 33076

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; the form renders with all fields; the success/error script runs without console errors. (Actual delivery is verified by the owner after the real access key is added.)

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add contact page with Web3Forms form"
```

---

## Task 9: Privacy Policy page

**Files:**
- Create: `src/pages/privacy.astro`

- [ ] **Step 1: Build the Privacy Policy page**

`privacy.astro` uses `BaseLayout` (title: `Privacy Policy`). Render the following content as clean, readable legal text (use `frontend-design` for typographic styling of long-form text). Use `RnJ Software's website launch date` wording for the effective date — set it to the placeholder text `Effective date: [to be set at launch]` with an HTML comment to fill it in.

> **Privacy Policy**
>
> RnJ Software LLC ("RnJ Software," "we," "us," or "our") operates the website rnj-software.com (the "Site"). This Privacy Policy explains what information we collect when you visit the Site and how we use it.
>
> **Information We Collect.** The only personal information we collect through this Site is information you choose to provide when you contact us. If you submit our contact form, we collect your name, email address, and the contents of your message. We do not use advertising cookies or third-party tracking technologies on this Site.
>
> **How We Use Your Information.** We use the information you submit solely to respond to your inquiry and communicate with you about your request. We do not sell, rent, or trade your personal information.
>
> **Contact Form Processing.** Our contact form is processed by Web3Forms, a third-party form-delivery service, which transmits your submission to us by email. Your information is handled for the sole purpose of delivering your message to us.
>
> **Data Retention.** We retain contact-form submissions for as long as needed to respond to your inquiry and maintain a record of our communications, after which they may be deleted.
>
> **Children's Privacy.** This Site is not directed to children under the age of 13, and we do not knowingly collect personal information from children.
>
> **Third-Party Links.** This Site may link to other websites, including vanihla.com and the Apple App Store. We are not responsible for the privacy practices of those sites.
>
> **Changes to This Policy.** We may update this Privacy Policy from time to time. Changes will be posted on this page with a revised effective date.
>
> **Contact Us.** If you have questions about this Privacy Policy, contact us at support@rnj-software.com or RnJ Software LLC, 5944 Coral Ridge Dr #1091, Coral Springs, FL 33076.

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; page is readable and responsive.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add privacy policy page"
```

---

## Task 10: Terms of Service page

**Files:**
- Create: `src/pages/terms.astro`

- [ ] **Step 1: Build the Terms of Service page**

`terms.astro` uses `BaseLayout` (title: `Terms of Service`). Same effective-date placeholder approach as Task 9. Render:

> **Terms of Service**
>
> These Terms of Service ("Terms") govern your use of the website rnj-software.com (the "Site"), operated by RnJ Software LLC ("RnJ Software," "we," "us," or "our"). By accessing or using the Site, you agree to these Terms.
>
> **Use of the Site.** You may use the Site for lawful, personal, and informational purposes. You agree not to use the Site in any way that could damage, disable, or impair it, or interfere with any other party's use of the Site.
>
> **Intellectual Property.** All content on this Site — including text, graphics, logos, and the RnJ Software name and marks — is the property of RnJ Software LLC or its licensors and is protected by applicable intellectual property laws. You may not reproduce or redistribute Site content without our prior written permission.
>
> **No Warranty.** The Site is provided "as is" and "as available" without warranties of any kind, express or implied. We do not warrant that the Site will be uninterrupted, error-free, or free of harmful components.
>
> **Limitation of Liability.** To the fullest extent permitted by law, RnJ Software LLC will not be liable for any indirect, incidental, or consequential damages arising from your use of, or inability to use, the Site.
>
> **External Links.** The Site may contain links to third-party websites. We are not responsible for the content or practices of those websites.
>
> **Governing Law.** These Terms are governed by the laws of the State of Florida, without regard to its conflict-of-law principles.
>
> **Changes to These Terms.** We may update these Terms from time to time. Continued use of the Site after changes are posted constitutes acceptance of the revised Terms.
>
> **Contact Us.** Questions about these Terms? Contact us at support@rnj-software.com or RnJ Software LLC, 5944 Coral Ridge Dr #1091, Coral Springs, FL 33076.

- [ ] **Step 2: Verify**

Run: `npm run build` then `npm run dev`
Expected: build succeeds; page renders.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add terms of service page"
```

---

## Task 11: 404 page

**Files:**
- Create: `src/pages/404.astro`

- [ ] **Step 1: Build the 404 page with `frontend-design`**

`404.astro` uses `BaseLayout` (title: `Page Not Found`). Friendly message — *"That page doesn't exist."* — and a button "Back to home" → `/`. Keep it on-brand.

- [ ] **Step 2: Verify**

Run: `npm run build`
Expected: `dist/404.html` is produced.

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Add 404 page"
```

---

## Task 12: SEO assets — robots, OG image

**Files:**
- Create: `public/robots.txt`
- Create: `public/og-image.png`

- [ ] **Step 1: Add `robots.txt`**

Create `public/robots.txt`:
```
User-agent: *
Allow: /

Sitemap: https://rnj-software.com/sitemap-index.xml
```

- [ ] **Step 2: Create the Open Graph image**

Create `public/og-image.png` (1200×630) — a branded card with the RnJ logo/wordmark on the charcoal background. Use the `frontend-design` skill's design tokens for consistency.

- [ ] **Step 3: Verify**

Run: `npm run build`
Expected: build succeeds; `dist/robots.txt`, `dist/og-image.png`, and `dist/sitemap-index.xml` all exist.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "Add robots.txt and Open Graph image"
```

---

## Task 13: Deployment — GitHub Actions, CNAME, README

**Files:**
- Create: `.github/workflows/deploy.yml`
- Create: `public/CNAME`
- Create: `README.md`

- [ ] **Step 1: Add the custom-domain CNAME**

Create `public/CNAME` containing exactly one line:
```
rnj-software.com
```

- [ ] **Step 2: Add the GitHub Actions deploy workflow**

Create `.github/workflows/deploy.yml`:
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Build Astro site
        uses: withastro/action@v3
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 3: Write `README.md`**

Create `README.md` documenting:
- What the repo is: the `rnj-software.com` website for RnJ Software LLC.
- Tech stack: Astro + Tailwind, static, GitHub Pages.
- Local development: `npm install`, `npm run dev`, `npm run build`, `npm run preview`.
- Deployment: pushes to `main` auto-deploy via GitHub Actions.
- **Launch checklist** (the items only the owner can do):
  1. Create the `website` repo in the `rnj-software` GitHub org; push `main`.
  2. Repo Settings → Pages → Source: "GitHub Actions".
  3. Sign up at web3forms.com with `support@rnj-software.com`; paste the real access key into `src/pages/contact.astro` (replace `WEB3FORMS_ACCESS_KEY_PLACEHOLDER`).
  4. Set the effective date on the Privacy Policy and Terms pages.
  5. At Squarespace DNS for `rnj-software.com`, add four `A` records for the apex pointing to GitHub Pages IPs (`185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`) and a `CNAME` for `www` → `rnj-software.github.io`. Then in repo Settings → Pages, set the custom domain to `rnj-software.com` and enable "Enforce HTTPS".
  6. Review the Privacy Policy and Terms of Service (ideally with legal counsel).

- [ ] **Step 4: Verify**

Run: `npm run build`
Expected: build succeeds; `dist/CNAME` exists and contains `rnj-software.com`.

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Add GitHub Pages deployment workflow, CNAME, and README"
```

---

## Task 14: Final review — build, links, accessibility

**Files:** none (verification only)

- [ ] **Step 1: Full production build**

Run: `npm run build && npm run preview`
Expected: build succeeds with no warnings about broken assets; preview server serves the site.

- [ ] **Step 2: Manual checklist**

In the preview server, confirm:
- All 8 pages load and are reachable from the header and footer nav.
- No broken internal links; external links (`vanihla.com`) open in a new tab.
- Header/footer identical on every page; footer shows legal name, address, email.
- Site is responsive at 375px, 768px, and 1280px widths.
- Apple verification content is present: "RnJ Software LLC" visible site-wide, Florida address on Contact + footer, the "Vanihla is a product of RnJ Software LLC" line on Products, Privacy + Terms reachable.

- [ ] **Step 3: Accessibility pass**

Check: every image has `alt` text, headings are hierarchical (one `h1` per page), the mobile menu and contact form are keyboard-navigable, and text/background contrast meets WCAG AA. Fix any issues found, rebuild, and confirm.

- [ ] **Step 4: Commit any fixes**

```bash
git add -A
git commit -m "Final accessibility and link fixes"
```

---

## Self-review against the spec

- **Tech stack & hosting** (spec §3) → Task 1, 13.
- **Business facts displayed** (spec §4) → Header/Footer (Task 3), About (Task 5), Contact (Task 8).
- **Positioning, consulting-first, no slogan** (spec §5) → Home hero (Task 4).
- **All 8 pages** (spec §6) → Tasks 4–11.
- **Five services** (spec §6) → Task 6.
- **Vanihla showcase + ownership link** (spec §6, Apple) → Task 7.
- **Components & repo structure** (spec §7) → Tasks 1, 3.
- **Web3Forms contact form** (spec §8) → Task 8.
- **Brand & design direction** (spec §9) → Task 2, applied throughout via `frontend-design`.
- **SEO & accessibility** (spec §10) → BaseLayout meta (Task 3), Task 12, Task 14.
- **Legal pages** (spec §11) → Tasks 9, 10.
- **Apple verification coverage** (spec §2 table) → verified in Task 14 Step 2.
- **User launch items** (spec §13) → documented in README (Task 13 Step 3).
