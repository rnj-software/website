# Architecture — rnj-software.com

> Living reference for how the RnJ Software marketing site is built, hosted,
> secured, and how the contact form actually reaches a human. Update this file
> whenever any of the below changes.

**Live site:** https://rnj-software.com
**Repo:** https://github.com/rnj-software/website
**Owner:** RnJ Software LLC · `support@rnj-software.com`

---

## 1. What this repo is

A **static** marketing website built with Astro, output as pre-rendered HTML +
CSS + a small amount of JS, and served by **GitHub Pages** behind a
Let's Encrypt certificate. There is **no backend server** we operate — the
contact form is handled by a third-party service (Web3Forms), and mail delivery
is handled by ImprovMX forwarding into Gmail.

### Hosting map at a glance

```
                 ┌───────────────────────────────────────────────┐
   Visitor ────▶ │ https://rnj-software.com (GitHub Pages + CDN) │
                 └───────────────────────────────────────────────┘
                                    ▲
                                    │ deploy on push to main
                                    │
                         ┌──────────────────────┐
                         │  GitHub Actions      │
                         │  .github/workflows/  │
                         │   deploy.yml         │
                         └──────────┬───────────┘
                                    │
                     ┌──────────────┴──────────────┐
                     │ Repo: rnj-software/website  │
                     │ Branch: main                │
                     └─────────────────────────────┘
```

---

## 2. Tech stack

| Layer            | Choice                                         | Version    |
| ---------------- | ---------------------------------------------- | ---------- |
| Static generator | [Astro](https://astro.build)                   | `^6.3.7`   |
| Styling          | [Tailwind CSS v4](https://tailwindcss.com/)    | `^4.3.0`   |
| Tailwind bundler | `@tailwindcss/vite` (Astro Vite plugin)        | `^4.3.0`   |
| Sitemap          | `@astrojs/sitemap`                             | `^3.7.2`   |
| Runtime          | Node.js                                        | `>=22.12`  |
| Hosting          | GitHub Pages (custom domain)                   | —          |
| CI/CD            | GitHub Actions (`withastro/action@v3`)         | —          |
| Contact form     | [Web3Forms](https://web3forms.com) (free tier) | —          |
| Email forwarding | [ImprovMX](https://improvmx.com) (free tier)   | —          |
| DNS registrar    | Squarespace Domains                            | —          |
| Delivered inbox  | Gmail (`rnj.software@gmail.com`)               | —          |

### Why these

- **Astro**: zero-JS by default, fast builds, first-class Markdown/MDX later if
  needed. Static output = free hosting on GitHub Pages.
- **GitHub Pages**: free, fast global CDN, native custom-domain + HTTPS.
- **Web3Forms + ImprovMX**: contact form working without operating a server.
- **Tailwind v4 via `@theme` in CSS**: design tokens live in one CSS file
  (`src/styles/global.css`), no config file needed.

---

## 3. Repository layout

```
rnj-software-website/
├── .github/workflows/
│   └── deploy.yml            # CI: build + publish to GitHub Pages
├── astro.config.mjs          # site URL, sitemap, tailwind vite plugin
├── package.json              # deps + node engine
├── public/                   # copied verbatim into the build
│   ├── CNAME                 # tells GH Pages our custom domain
│   ├── favicon.ico/.svg      # RnJ </> code-mark favicons
│   ├── logo.png              # circular RnJ badge (invert for dark bg)
│   ├── og-image.png          # social share card
│   ├── vanihla-mockup.png    # product image on /products
│   ├── robots.txt            # allow all, points to sitemap
│   └── .nojekyll             # disable Jekyll on Pages
├── src/
│   ├── layouts/
│   │   └── BaseLayout.astro  # <head>, header, footer wrapper for every page
│   ├── components/
│   │   ├── Header.astro      # logo + nav + mobile menu
│   │   ├── Footer.astro      # legal, contact, sitemap links
│   │   └── ServiceCard.astro # tile used on /services and home
│   ├── pages/                # one file = one route
│   │   ├── index.astro        (/)
│   │   ├── about.astro        (/about)
│   │   ├── services.astro     (/services)
│   │   ├── products.astro     (/products)
│   │   ├── contact.astro      (/contact)   ← form + JS submit
│   │   ├── privacy.astro      (/privacy)
│   │   ├── terms.astro        (/terms)
│   │   └── 404.astro          (/404)
│   └── styles/
│       └── global.css        # design tokens, resets, base type
└── docs/
    ├── ARCHITECTURE.md       # ← this file
    └── superpowers/          # historical spec + implementation plan
```

---

## 4. Pages & routes

| Route       | Purpose                                        | Notable                                         |
| :---------- | :--------------------------------------------- | :---------------------------------------------- |
| `/`         | Home / hero / capabilities / featured product  | Single `<h1>`. Hero animates in on load.        |
| `/about`    | Company narrative + location + ownership       |                                                 |
| `/services` | 6 service tiles (`ServiceCard.astro`)          | Add new tiles by extending the array in-page.   |
| `/products` | Vanihla feature + "Coming to App Store/Play"   | Uses `public/vanihla-mockup.png`.               |
| `/contact`  | Contact form + direct-contact details          | See §8 for full flow.                           |
| `/privacy`  | Privacy Policy (effective July 2025)           | Static content; no tracking implemented.        |
| `/terms`    | Terms of Service (effective July 2025)         | Static content.                                 |
| `/404`      | Not-found page                                 | Astro/GH Pages serves this automatically.       |

Routing is filesystem-based: **filename → route**. To add a new page, drop a
new `.astro` file in `src/pages/`. Include `<BaseLayout>` so header/footer/SEO
tags are consistent.

---

## 5. Design system

Everything lives in one place: **`src/styles/global.css`**.

- **Tokens (`@theme`)**: colors, fonts, spacing, radii, container widths.
  - `--color-ink-900` — near-black (`#1a1a1a`) — primary surface
  - `--color-paper` — off-white — light sections
  - `--color-signal` — Signal Blue (`#2563ff`) — accent / links / CTAs
- **Typography**: Space Grotesk (display), IBM Plex Sans (body),
  IBM Plex Mono (labels/eyebrows). Loaded from Google Fonts in
  `BaseLayout.astro`.
- **Motion**: subtle enter animations gated by `@media (prefers-reduced-motion: reduce)`.
- **Layout**: `--container-site` sets the max content width; consistent gutter
  via `--spacing-gutter`.

To rebrand colors, change the token — every component follows.

---

## 6. Build & deploy

### Build

- `npm install` then `npm run build` produces `dist/` — pure static files.
- Sitemap is emitted at `dist/sitemap-index.xml`.

### Deploy pipeline

```
   git push main
        │
        ▼
  ┌───────────────────────────────┐
  │  .github/workflows/deploy.yml │
  │  1. checkout                  │
  │  2. withastro/action@v3       │
  │     (node 22, npm install,    │
  │      astro build, upload      │
  │      artifact)                │
  │  3. actions/deploy-pages@v4   │
  └───────────────────────────────┘
        │
        ▼
   GitHub Pages (rnj-software/website, main)
```

- **Trigger**: push to `main` or manual `workflow_dispatch`.
- **Node version**: pinned to `22` in the workflow (Astro 6 needs ≥ 22.12).
- **Concurrency**: `group: pages, cancel-in-progress: true` — a newer push
  cancels an in-flight build so we never deploy stale output.
- **Pages source** (in repo Settings → Pages): **GitHub Actions** (not
  branch-based).

### Rolling back

Revert the offending commit on `main` and push. The next workflow run
re-publishes the previous state (there is no manual "rollback" button in
GH Pages — CI is the mechanism).

---

## 7. Custom domain, DNS, HTTPS

### DNS records (all live at Squarespace for `rnj-software.com`)

Multiple services share this DNS zone. **Do not delete records you don't
recognize without checking here.**

| Record | Host | Value | Purpose |
| :----- | :--- | :---- | :------ |
| A      | `@`  | `185.199.108.153`               | GitHub Pages (apex) |
| A      | `@`  | `185.199.109.153`               | GitHub Pages (apex) |
| A      | `@`  | `185.199.110.153`               | GitHub Pages (apex) |
| A      | `@`  | `185.199.111.153`               | GitHub Pages (apex) |
| CNAME  | `www`| `rnj-software.github.io`        | GitHub Pages (www)  |
| MX 10  | `@`  | `mx1.improvmx.com`              | Email forwarding    |
| MX 20  | `@`  | `mx2.improvmx.com`              | Email forwarding    |
| TXT    | `@`  | `v=spf1 include:spf.improvmx.com ~all` | SPF for ImprovMX |

Additional records will exist for DKIM if/when configured, and for future
services (e.g. domain verification for App Store Connect, etc.).

### GitHub Pages custom domain

- `public/CNAME` contains the string `rnj-software.com` — this is how Pages
  knows which host to serve.
- Repo Settings → Pages → Custom domain = `rnj-software.com`.
- **Enforce HTTPS** is enabled — every HTTP request is `301`-redirected to
  HTTPS.

### Certificate

- **Let's Encrypt**, provisioned automatically by GitHub Pages after DNS
  verification.
- Auto-renewed by GitHub; nothing for us to do.
- Verify with: `openssl s_client -connect rnj-software.com:443 -servername rnj-software.com </dev/null | openssl x509 -noout -subject`
  → expected: `subject=CN=rnj-software.com`.

---

## 8. Contact form — end-to-end

This is the most important non-obvious flow. The form on `/contact` reaches a
human without us running any server.

### Wire diagram

```
┌──────────────────────────────────────────────────────────────────────┐
│ Visitor browser — https://rnj-software.com/contact                   │
│                                                                       │
│   <form> in src/pages/contact.astro                                   │
│   Hidden fields:                                                      │
│     access_key = "4018e3cb-…-2e914eaab999"   ← public form ID         │
│     subject    = "New message from rnj-software.com"                  │
│     redirect   = "https://rnj-software.com"                           │
│   Honeypot:                                                           │
│     botcheck   (hidden, off-screen, aria-hidden — bots fill it)       │
│                                                                       │
│   Inline <script> intercepts submit and POSTs JSON to:                │
│     https://api.web3forms.com/submit                                  │
└───────────────────────────────────┬──────────────────────────────────┘
                                    │  HTTPS POST
                                    ▼
┌──────────────────────────────────────────────────────────────────────┐
│ Web3Forms (SaaS)                                                     │
│   1. Validates access_key                                             │
│   2. Rejects submissions where honeypot `botcheck` is non-empty       │
│   3. Applies built-in spam scoring                                    │
│   4. Emails the submission → support@rnj-software.com                 │
└───────────────────────────────────┬──────────────────────────────────┘
                                    │  SMTP delivery
                                    ▼
┌──────────────────────────────────────────────────────────────────────┐
│ DNS lookup for rnj-software.com MX → mx1/mx2.improvmx.com            │
└───────────────────────────────────┬──────────────────────────────────┘
                                    │
                                    ▼
┌──────────────────────────────────────────────────────────────────────┐
│ ImprovMX (free forwarding)                                           │
│   Alias: support@rnj-software.com  →  rnj.software@gmail.com         │
└───────────────────────────────────┬──────────────────────────────────┘
                                    │
                                    ▼
┌──────────────────────────────────────────────────────────────────────┐
│ Gmail inbox — rnj.software@gmail.com  ✉︎                              │
└──────────────────────────────────────────────────────────────────────┘
```

### Access key

- Location: `src/pages/contact.astro`, in the hidden input `name="access_key"`.
- Value: `4018e3cb-2619-4118-aa61-2e914eaab999`.
- **It is public by design** (rendered in HTML the browser downloads). Safe to
  commit. Not a secret. It only authorizes "send an email to
  `support@rnj-software.com`" — no other permissions.

### Spam protection

- **Honeypot (`botcheck`)** — active. Off-screen hidden input; bots that fill
  every field get rejected server-side by Web3Forms.
- **Domain restriction** — Web3Forms Pro-only, not enabled. Not needed.
- **hCaptcha** — not enabled. Free upgrade if inbox ever floods with junk.
  Add via `h-captcha-response` field per Web3Forms docs.
- **Web3Forms built-in spam filter** — always on.

### Failure modes & how to debug

| Symptom | Likely cause | Where to check |
| :------ | :----------- | :------------- |
| Form shows success but no email arrives | ImprovMX not forwarding, or Gmail spam-filtered it | ImprovMX dashboard delivery log; Gmail *All Mail* + Spam |
| Form shows error message | Invalid/expired access key, or Web3Forms outage | web3forms.com dashboard |
| Web3Forms says free quota exceeded | 250 submissions/month on free tier — likely spam wave | Enable hCaptcha; consider upgrade |
| DNS check fails (`dig MX rnj-software.com` empty) | Someone removed the MX records | Squarespace DNS settings |

### The `rnj-software.com` email situation

- **We do not run a mailbox on this domain.** `support@rnj-software.com` is an
  ImprovMX alias that forwards to a Gmail inbox.
- **Zoho Mail hosts `vanihla.com`, not `rnj-software.com`.** Adding
  `rnj-software.com` to Zoho would require the multi-domain paid plan; we
  chose forwarding to stay free.
- If we ever want to *send* mail *from* `support@rnj-software.com` (not just
  receive), we would either upgrade Zoho and add the domain, or use Gmail's
  "Send mail as" with an SMTP relay + SPF/DKIM setup at ImprovMX.

---

## 9. Assets & branding

| File                          | Purpose                                                    |
| :---------------------------- | :--------------------------------------------------------- |
| `public/logo.png`             | Circular RnJ Software badge — inverted in the dark header. |
| `public/favicon.svg`          | Custom `</>` code-mark favicon (modern browsers).          |
| `public/favicon.ico`          | Same design as PNG-format `.ico` — legacy/tab fallback.    |
| `public/og-image.png`         | Social share card (Open Graph + Twitter).                  |
| `public/vanihla-mockup.png`   | Real app screenshot used on `/products`.                   |

Regenerating the favicon (if the SVG changes):

```
# render SVG → PNG on macOS with built-in tools
sed 's/width="32" height="32"/width="512" height="512"/' \
  public/favicon.svg > /tmp/f.svg
qlmanage -t -s 512 -o /tmp /tmp/f.svg
sips -z 64 64 /tmp/f.svg.png --out public/favicon.ico
```

---

## 10. SEO

- `astro.config.mjs` sets `site: 'https://rnj-software.com'` — required for
  correct absolute URLs in the sitemap and canonical tags.
- `@astrojs/sitemap` emits `sitemap-index.xml` + `sitemap-0.xml` at build.
- `public/robots.txt` allows all crawlers and points to the sitemap.
- `<meta>` tags (title, description, OG, Twitter) are set per-page via the
  frontmatter passed into `BaseLayout.astro`.

---

## 11. Runbook — common changes

**Add or edit copy on a page**
Edit the relevant `.astro` in `src/pages/`. Commit and push. CI deploys.

**Add a new service tile**
Extend the services array inside `src/pages/services.astro`. Re-uses
`ServiceCard.astro` — no other changes needed.

**Add a new page**
1. Create `src/pages/foo.astro`.
2. Wrap contents in `<BaseLayout title="…" description="…">`.
3. Add a nav link in `src/components/Header.astro` and `Footer.astro` if
   user-facing.
4. Push.

**Change global color / font**
Edit the `@theme` block at the top of `src/styles/global.css`. Every
component picks it up.

**Change an email address / forwarding target**
Log in to ImprovMX → change the alias target. No repo change needed.

**Update the Web3Forms key**
Replace the value of the hidden `access_key` input in
`src/pages/contact.astro`, commit, push.

**Rotate the Web3Forms key (rare)**
Create a new form in Web3Forms → copy the new key → replace as above. Old key
stops working immediately when deleted in their dashboard.

---

## 12. Emergency / rollback

| Situation                           | Action                                                        |
| :---------------------------------- | :------------------------------------------------------------ |
| Bad deploy — site broken            | `git revert <sha> && git push` — CI republishes.              |
| Contact form silently failing       | Check ImprovMX log + Gmail spam. If Web3Forms is down, page still loads; only the form is affected. |
| DNS/HTTPS broken after registrar change | Re-check the table in §7. Missing GH Pages A records = site down. Missing MX records = form loses email. |
| Web3Forms outage                    | Nothing to do server-side; failure is graceful (error message on the form). |
| GitHub Pages outage                 | Nothing to do; wait it out. Consider Cloudflare Pages / Vercel as a future secondary host if resilience matters. |

---

## 13. Non-goals / what's intentionally NOT here

- **No analytics / tracking.** No GA, no Plausible, no Fathom. Privacy Policy
  reflects this. Adding any would require a policy update.
- **No cookies set by us.** GitHub Pages / Google Fonts may set caching-related
  cookies; nothing from application code.
- **No user accounts, auth, or database.** Static content only.
- **No CMS.** Copy is edited in Astro files.
- **No blog** (yet). If added, use Astro Content Collections + MD/MDX.
