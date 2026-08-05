# dorukerel.com (Academic Portfolio & CV)

The personal portfolio and CV website of **Doruk Erel**, a science high school student in Ankara, Turkey, focused on embedded systems, hardware design, and network administration. The site is built as a single self-contained HTML file with no build step, ships a fully client-side PDF CV generator, and keeps its project list current by fetching live from the GitHub API on every page load.

**Live site:** [https://dorukerel.com](https://dorukerel.com)

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Client-Side CV Generation](#client-side-cv-generation)
- [Internationalization](#internationalization)
- [Analytics and Consent](#analytics-and-consent)
- [Repository Structure](#repository-structure)
- [Deployment](#deployment)
- [Local Development](#local-development)
- [License](#license)

---

## Overview

The website serves as a living portfolio and downloadable CV. Rather than relying on a framework or a static site generator, the entire experience is delivered from one `index.html` file, keeping the project easy to host, audit, and maintain. Dynamic content, such as the project list and the generated CV, is assembled in the browser at runtime so the site stays current without a rebuild.

| Attribute | Detail |
|-----------|--------|
| Owner | Doruk Erel |
| Purpose | Academic portfolio and downloadable CV |
| Architecture | Single-file, no build step |
| Languages | English and Turkish, switchable in-page |
| Hosting | Cloudflare Pages |

---

## Key Features

- **Single-file architecture.** The entire site (HTML, CSS, JavaScript) lives in one `index.html` with no build step and no external dependencies beyond CDN libraries.
- **Bilingual interface (EN / TR).** Instant language switching via a slide toggle, with the choice persisted in a cookie and English shown by default.
- **Live GitHub projects.** The Projects section fetches public repositories from the GitHub API on every page load, so it stays current without manual updates.
- **Client-side CV generation.** The "Download CV" button builds a one-page, print-quality PDF entirely in the browser using jsPDF, in whichever language the page is currently displaying.
- **Document archive.** A categorized, collapsible archive of report cards, certificates, and awards, with an in-page PDF viewer and per-file downloads.
- **Built-in analytics.** Google Analytics 4 and Microsoft Clarity, with custom events for CV downloads, document views and downloads, outbound link clicks, and contact clicks.

---

## Architecture

The site follows a deliberately minimal, single-file design. All markup, styling, and scripting are contained within `index.html`, with no bundler, transpiler, or server-side component involved. The only runtime dependencies are a small number of CDN-hosted libraries loaded directly in the page.

This approach keeps the deployment surface tiny and the source fully transparent: the whole site can be read top to bottom in a single file, and publishing is as simple as uploading that file. Dynamic behavior is layered on at runtime rather than baked in at build time, which is why the project list and CV always reflect the latest state without any regeneration step.

---

## Client-Side CV Generation

The "Download CV" button produces a one-page, print-quality PDF entirely in the browser using jsPDF. The generated document is created in whichever language the page is currently displaying, so an English visitor receives an English CV and a Turkish visitor receives a Turkish one.

The CV pulls the bio and project list live from the page itself, meaning the downloaded document always matches the current on-screen content. To guarantee correct rendering of Turkish characters such as ı, ş, ğ, and İ, the generator embeds a subsetted Liberation Sans font directly in the PDF.

---

## Internationalization

The interface is fully bilingual, offering English and Turkish. Visitors switch languages instantly through a slide toggle, with no page reload required. The selected language is stored in a cookie so the preference persists across visits, and English is presented by default. Language selection propagates through the whole experience, including the dynamically generated CV.

---

## Analytics and Consent

The site integrates Google Analytics 4 and Microsoft Clarity for usage insight. Beyond standard page views, it records custom events for CV downloads, document views and downloads, outbound link clicks, and contact clicks, giving a clear picture of how visitors engage with the portfolio. Cookie consent is managed through CookieHub, so analytics respect the visitor's stated preferences.

---

## Repository Structure

```text
.
├── index.html              Entire site: markup, styles, and scripts
├── favicon.ico             Multi-size ICO favicon
├── favicon-32.png          PNG favicons (32 / 192 / 512)
├── favicon-192.png
├── favicon-512.png
├── apple-touch-icon.png    iOS home screen icon
├── CNAME                   Custom domain configuration
└── files/                  Document archive (PDFs) and profile photo
```

---

## Deployment

The site is deployed on **Cloudflare Pages**. Any push to the repository, or a drag-and-drop upload of `index.html` and its assets, publishes the update. No build command is required.

---

## Local Development

No tooling is needed. Open `index.html` directly in a browser to run the site locally. Note that the GitHub API fetch and the analytics scripts require an internet connection; without one, the Projects section falls back to a static featured list.

---

## License

© Doruk Erel. All rights reserved. The source is public for transparency, but the content (documents, texts, and personal materials) is not licensed for reuse.
