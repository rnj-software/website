# RnJ Software LLC Website

The [rnj-software.com](https://rnj-software.com) marketing website for RnJ Software LLC.

## Tech Stack

- **[Astro](https://astro.build/)** — static site generator
- **[Tailwind CSS v4](https://tailwindcss.com/)** — utility-first CSS framework
- Output: fully static site (no server required), hosted on **GitHub Pages**

## Site Structure

| Route | Page |
| :---- | :--- |
| `/` | Home |
| `/about` | About |
| `/services` | Services |
| `/products` | Products |
| `/contact` | Contact |
| `/privacy` | Privacy Policy |
| `/terms` | Terms of Service |
| `/404` | 404 Not Found |

## Local Development

```bash
# Install dependencies
npm install

# Start the development server (http://localhost:4321)
npm run dev

# Build for production (output to dist/)
npm run build

# Preview the production build locally
npm run preview
```

## Deployment

Pushes to the `main` branch automatically trigger the GitHub Actions workflow defined in [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml), which builds the site with Astro and deploys the static output to GitHub Pages.

## Launch Checklist

Items that must be completed by the site owner before the site goes live:

- [ ] Create the `website` repository in the `rnj-software` GitHub organization and push the `main` branch.
- [ ] In the repo: **Settings → Pages → Build and deployment → Source** — select **GitHub Actions**.
- [ ] Sign up at [web3forms.com](https://web3forms.com) using `support@rnj-software.com`, obtain the access key, and replace `WEB3FORMS_ACCESS_KEY_PLACEHOLDER` in `src/pages/contact.astro` with the real key.
- [ ] Set the effective date on the Privacy Policy (`src/pages/privacy.astro`) and Terms of Service (`src/pages/terms.astro`) pages — replace `[to be set at launch]` with the actual launch date.
- [ ] At the Squarespace DNS settings for `rnj-software.com`: add four `A` records for the apex domain pointing to the GitHub Pages IPs `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`; and add a `CNAME` record for the `www` subdomain pointing to `rnj-software.github.io`. Then in the repo **Settings → Pages**, set the custom domain to `rnj-software.com` and enable **Enforce HTTPS**.
- [ ] Review the Privacy Policy and Terms of Service content (ideally with legal counsel) before launch.
