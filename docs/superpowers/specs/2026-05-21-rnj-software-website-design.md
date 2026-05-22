# RnJ Software LLC — Website Design Spec

**Date:** 2026-05-21
**Status:** Awaiting user review

## 1. Purpose

Build the official website for **RnJ Software LLC** at `rnj-software.com`. The
immediate business driver is Apple Developer Program verification: Apple
requires a legitimate, publicly reachable company website to verify RnJ
Software LLC as a business entity and to confirm that RnJ is the company
behind the apps it publishes (notably **Vanihla**). The site must therefore
read as a genuine, complete, operating software business — not a placeholder.

Beyond verification, the site serves as RnJ's public presence: describing its
software engineering and consulting services and its owned products.

## 2. Goals

1. Present RnJ Software LLC as a credible, established software firm.
2. Display the legal entity name, Florida location, and contact details that
   Apple will cross-check against business registration / D-U-N-S records.
3. Explicitly establish that **Vanihla is a product of RnJ Software LLC**.
4. Provide the legal pages (Privacy Policy, Terms of Service) Apple and general
   web best practice expect.
5. Be cheap and low-maintenance to host (static site, free hosting, HTTPS).

### Apple verification coverage

| Requirement | How the site satisfies it |
|---|---|
| Legitimate, reachable website on the company domain | `rnj-software.com`, HTTPS (automatic on GitHub Pages) |
| Legal entity name visible | "RnJ Software LLC" in hero, About page, and footer of every page |
| Business location | Coral Springs, FL address on Contact page and footer |
| Working contact method | Web3Forms contact form + `support@rnj-software.com` |
| Company tied to its apps | Products page presents Vanihla as an RnJ Software LLC product, linking to vanihla.com |
| Privacy Policy | Dedicated `/privacy` page |
| Terms of Service | Dedicated `/terms` page |

## 3. Tech stack & hosting

- **Framework:** Astro (latest), with shared layout/components so global
  elements (header, footer, address) are edited once.
- **Styling:** Tailwind CSS via the official Astro integration.
- **Output:** Fully static HTML — no server, no backend.
- **Hosting:** GitHub Pages, free, from the `rnj-software` GitHub org.
- **Repo:** `github.com/rnj-software/website` (local dir:
  `~/Documents/projects/rnj-software-website`).
- **Domain:** `rnj-software.com`, currently registered at Squarespace. DNS
  records will be repointed to GitHub Pages; a `public/CNAME` file in the repo
  binds the custom domain. HTTPS is provisioned automatically by GitHub Pages.
- **Deployment:** GitHub Actions workflow builds the Astro site and publishes
  to GitHub Pages on every push to `main`.

## 4. Business facts (displayed on the site)

- **Legal name:** RnJ Software LLC
- **Entity type / jurisdiction:** Limited Liability Company, Florida
- **Founded:** 2025
- **Address:** 5944 Coral Ridge Dr #1091, Coral Springs, FL 33076
- **Email:** support@rnj-software.com
- **Team:** Presented company-only; no individual names.

## 5. Positioning

RnJ is positioned **consulting-first**: a software engineering and consulting
firm. The homepage leads with services for clients. Owned products (Vanihla)
are presented on the Products page as part of RnJ's portfolio — establishing
the RnJ → Vanihla ownership link required for Apple, without making products
the headline story.

No marketing slogan/tagline is used. The hero states the company name and a
plain factual description.

## 6. Site map

Eight pages:

| Path | Page | Summary |
|---|---|---|
| `/` | Home | Hero (company name + factual description + location), services summary, brief "what we do", Vanihla mention with link to Products, contact CTA |
| `/about` | About | Who RnJ is, mission/approach, legal entity name, founded 2025, Florida LLC, Coral Springs location |
| `/services` | Services | The five service offerings, each with a short description |
| `/products` | Products | Featured Vanihla showcase; "more products in development" note |
| `/contact` | Contact | Web3Forms contact form, `support@rnj-software.com`, mailing address |
| `/privacy` | Privacy Policy | How the website handles visitor data |
| `/terms` | Terms of Service | Terms governing use of the website |
| `/404` | Not Found | Friendly 404 with link home |

### Services (Services page)

1. Custom Software Development
2. Mobile App Development (iOS & Android)
3. AI & Machine Learning Integration
4. Cloud Architecture & DevOps
5. Technical Consulting & Strategy

### Hero content (Home)

- **H1:** "RnJ Software" (company wordmark, large)
- **Subhead:** "A software engineering and consulting firm based in Coral
  Springs, Florida." — plain, factual, editable.
- **Supporting line:** one sentence on what RnJ does for clients.
- **Primary CTA:** "Get in touch" → `/contact`.

### Products page content

- Featured **Vanihla** section: name, one-line description ("AI that extracts
  recipes from cooking videos"), short paragraph, screenshot(s), and links to
  `vanihla.com` and the App Store (App Store link added once live).
- Explicit line: "Vanihla is a product of RnJ Software LLC."
- A quiet "More products in development" note so a single product reads as
  momentum, not emptiness.

## 7. Components & repo structure

```
website/                         (repo: rnj-software/website)
├── .github/workflows/deploy.yml  GitHub Actions → GitHub Pages
├── public/
│   ├── CNAME                     rnj-software.com
│   ├── favicon.svg
│   ├── logo.png / logo-white     RnJ logo assets
│   ├── og-image.png              social share image
│   └── robots.txt
├── src/
│   ├── components/
│   │   ├── Header.astro          logo + nav + mobile menu
│   │   ├── Footer.astro          legal name, address, email, nav, copyright
│   │   ├── Hero.astro
│   │   ├── ServiceCard.astro
│   │   └── (section components as needed)
│   ├── layouts/
│   │   └── BaseLayout.astro      <head>, SEO meta, header + footer slot
│   ├── pages/                    index, about, services, products,
│   │                             contact, privacy, terms, 404
│   └── styles/global.css
├── astro.config.mjs              site: https://rnj-software.com
├── package.json
├── tsconfig.json
└── README.md                     dev/build/deploy + DNS instructions
```

Global elements (header, footer, business address, email) are defined once in
components and reused on every page.

## 8. Contact form

- Provider: **Web3Forms** — free, unlimited submissions, no backend.
- Fields: Name, Email, Message; plus a hidden honeypot field for spam.
- The form POSTs to the Web3Forms API with an access key. Submissions are
  emailed to `support@rnj-software.com`.
- The access key is obtained by signing up at web3forms.com with
  `support@rnj-software.com`. Until then the build uses a clearly-marked
  placeholder key that must be replaced before launch.
- On submit, the user sees an inline success confirmation; no page reload
  required.
- The Contact page also shows the email address and mailing address directly,
  so a working contact path exists even if the form is unused.

## 9. Brand & design direction

- Source asset: the RnJ Software logo — strictly black and white, featuring a
  code-screen `</>` icon and the "RNJ SOFTWARE" wordmark.
- Palette: a charcoal/near-black base (`~#1a1a1a`), off-white surfaces, and a
  single sharp accent color, chosen during implementation via the
  `frontend-design` skill.
- Aesthetic: modern, clean, technical — appropriate for a software engineering
  firm. Distinctive and polished, not a generic template.
- Responsive: mobile-first; works cleanly on phone, tablet, desktop.
- The detailed visual design is produced during implementation using the
  `frontend-design` skill.

## 10. SEO & accessibility

- Per-page `<title>` and meta description; Open Graph + Twitter Card tags.
- `astro.config.mjs` sets the canonical site URL; a `sitemap.xml` and
  `robots.txt` are included.
- Semantic HTML, descriptive `alt` text, sufficient color contrast, keyboard-
  navigable nav and form.

## 11. Legal pages

The Privacy Policy and Terms of Service will be drafted as part of
implementation:

- **Privacy Policy** — covers what the website collects (contact-form data:
  name, email, message), that form data is processed by Web3Forms and emailed
  to RnJ, that the site uses no third-party tracking/advertising cookies,
  data retention, children's privacy, how to contact RnJ about privacy, and an
  effective date. Scoped to the *website*; the Vanihla app has its own privacy
  policy on vanihla.com.
- **Terms of Service** — covers acceptable use of the website, intellectual
  property, disclaimers, limitation of liability, governing law (Florida), and
  changes to the terms.

These are standard template documents tailored to RnJ. **RnJ should review
them (ideally with legal counsel) before launch** — they are a starting point,
not legal advice.

## 12. Out of scope

- Blog / news section.
- CMS or any content-editing UI (content is edited directly in the repo).
- Analytics (none included by default; can be added later).
- Backend services, databases, authentication.
- App Store link on the Products page until Vanihla is live there.

## 13. Items the user must complete before launch

1. Create the `website` repo in the `rnj-software` GitHub org and push.
2. Sign up at web3forms.com with `support@rnj-software.com` and add the real
   access key.
3. Update DNS at Squarespace with the GitHub Pages records (exact values
   provided during implementation).
4. Enable GitHub Pages on the repo and set the custom domain.
5. Review the Privacy Policy and Terms of Service content.
6. Confirm `support@rnj-software.com` mailbox exists and is monitored.
