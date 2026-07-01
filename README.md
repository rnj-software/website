# RnJ Software LLC Website

Marketing website for RnJ Software LLC, live at
**[rnj-software.com](https://rnj-software.com)**.

## Full documentation

**→ [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)** — how the site is built,
hosted, secured, and how the contact form actually reaches a human
(Web3Forms → ImprovMX → Gmail). Start there for anything non-obvious.

## Tech stack (short version)

- **[Astro](https://astro.build/)** static site generator (v6, Node ≥ 22.12)
- **[Tailwind CSS v4](https://tailwindcss.com/)** via `@tailwindcss/vite`
- **GitHub Pages** hosting + **GitHub Actions** deploy
- **Web3Forms** for the contact form; **ImprovMX** for email forwarding

## Routes

| Route      | Page              |
| :--------- | :---------------- |
| `/`        | Home              |
| `/about`   | About             |
| `/services`| Services          |
| `/products`| Products          |
| `/contact` | Contact           |
| `/privacy` | Privacy Policy    |
| `/terms`   | Terms of Service  |

## Local development

```bash
npm install
npm run dev       # http://localhost:4321
npm run build     # → dist/
npm run preview   # serve the built site locally
```

## Deploy

Push to `main`. GitHub Actions (`.github/workflows/deploy.yml`) builds with
Astro and publishes to GitHub Pages. See
[ARCHITECTURE §6](docs/ARCHITECTURE.md#6-build--deploy) for details and rollback
procedure.
