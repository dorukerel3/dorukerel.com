# dorukerel.com (Academic Portfolio & CV)

The personal portfolio and CV website of **Doruk Erel**, a science high school student in Ankara, Turkey, focused on embedded systems, hardware design, and network administration. The site is a single self contained HTML file with no build step. It ships a fully client-side PDF CV generator, renders a live project list straight from the GitHub REST API, and pulls preview images out of each repository's README at runtime, all with no backend of any kind.

**Live site:** [https://dorukerel.com](https://dorukerel.com)

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Live GitHub Project Feed](#live-github-project-feed)
- [Client-Side CV Generation](#client-side-cv-generation)
- [Internationalization](#internationalization)
- [Analytics and Consent](#analytics-and-consent)
- [Document Archive](#document-archive)
- [Technology Stack](#technology-stack)
- [Repository Structure](#repository-structure)
- [Deployment](#deployment)
- [Local Development](#local-development)
- [License](#license)

---

## Overview

The website serves as a living portfolio and downloadable CV. Rather than relying on a framework or a static site generator, the entire experience is delivered from one `index.html` file, keeping the project easy to host, audit, and maintain. Dynamic content, the project list, the README preview carousels, and the generated CV, is assembled in the browser at runtime, so the site stays current without a rebuild.

| Attribute | Detail |
|-----------|--------|
| Owner | Doruk Erel |
| Purpose | Academic portfolio and downloadable CV |
| Architecture | Single file, no build step, no backend |
| Languages | English and Turkish, switchable in page |
| Data source | GitHub REST API, read at runtime |
| Hosting | Cloudflare Pages |

---

## Key Features

- **Single file architecture.** The entire site (HTML, CSS, JavaScript) lives in one `index.html` with no build step and no server side code. The only runtime dependencies are three CDN scripts.
- **Bilingual interface (EN / TR).** Instant language switching via a slide toggle. Every translatable string exists twice in the markup as `.en-text` and `.tr-text` spans, and a single `lang-en` class on `<body>` shows one set and hides the other through CSS. The choice is stored in a `lang` cookie (365 day expiry, `SameSite=Lax`) and English is shown by default.
- **Live GitHub project feed.** The Projects section calls the GitHub REST API on every page load, filters out forks and archived repositories, ranks the rest, and renders the top results as cards.
- **Automatic README previews.** For each shown repository the site fetches the README through the API, extracts its images, skips badges and SVGs, resolves relative paths to raw URLs, and builds an inline carousel on the card.
- **Client-side CV generation.** The "Download CV" button builds a one page, print quality PDF entirely in the browser with jsPDF, in whichever language the page is currently showing, with a subsetted font embedded so Turkish glyphs render correctly.
- **Document archive with in page viewer.** A categorized, collapsible archive of report cards, certificates, and awards, each openable in an inline PDF viewer or downloadable directly.
- **Privacy aware analytics.** Google Analytics 4 and Microsoft Clarity, gated behind CookieHub consent, with custom events for CV downloads, document views, outbound clicks, and contact clicks.

---

## Architecture

The site follows a deliberately minimal, single file design. All markup, styling, and scripting are contained within `index.html`, with no bundler, transpiler, or server side component involved. The only runtime dependencies are three CDN scripts: jsPDF for CV generation, Microsoft Clarity for analytics, and CookieHub for consent management.

This approach keeps the deployment surface tiny and the source fully transparent: the whole site can be read top to bottom in a single file, and publishing is as simple as uploading that file. Dynamic behavior is layered on at runtime rather than baked in at build time, which is why the project list, the README previews, and the CV always reflect the latest state without any regeneration step.

All network calls are read only and unauthenticated. Because no API token is present in a static page, the GitHub calls are subject to the API's unauthenticated rate limit, and the project feed degrades gracefully when that limit is reached (see below).

---

## Live GitHub Project Feed

The Projects section is built entirely from the GitHub REST API at runtime. The username and the featured topic are the only configuration:

```js
const GH_USER = 'dorukerel3';
const FEATURE_TOPIC = 'portfolio';
const MAX_REPOS = 12;
```

On load it requests the user's repositories:

```text
GET https://api.github.com/users/dorukerel3/repos?per_page=100&sort=updated
```

then processes the result client side:

1. **Filter.** Forks and archived repositories are dropped.
2. **Rank.** Repositories carrying the `portfolio` topic are promoted first, then the rest are ordered by star count, with most recently updated as the tiebreak.
3. **Trim.** The top 12 are rendered as cards; each card shows the description, up to five topic tags, the primary language, star count when nonzero, and a relative "updated" date.

### README preview carousels

For every rendered repository the site makes a second call:

```text
GET https://api.github.com/repos/dorukerel3/{repo}/readme
```

The Base64 README payload is decoded and parsed as HTML, and its `<img>` elements are collected (honoring `data-canonical-src` when present). Images matching a badge pattern (shields.io, badgen, CI status, and similar) are skipped, SVGs are skipped, and relative paths are resolved against the repository's `raw/HEAD/` URL. Whatever remains becomes an inline image carousel injected into that repository's card, so each project shows its own screenshots with no manual curation.

### Offline and rate-limit fallback

If the API cannot be reached or the rate limit is exhausted, the feed does not break. A hardcoded `FALLBACK` list of featured projects is rendered instead, flagged with an offline style, and the status line explains that live data is temporarily unavailable and links to the GitHub profile directly.

---

## Client-Side CV Generation

The "Download CV" button produces a one page, print quality PDF entirely in the browser using jsPDF (UMD build, loaded from a CDN). The document is generated in whichever language the page is currently displaying, so an English visitor receives an English CV and a Turkish visitor receives a Turkish one.

The CV pulls the bio and project list live from the page itself, meaning the downloaded document always matches the current on screen content. To guarantee correct rendering of Turkish characters such as ı, ş, ğ, and İ, the generator registers a subsetted Liberation Sans font in jsPDF's virtual file system in both regular and bold weights:

```js
doc.addFileToVFS('LiberationSans-Regular.ttf', CV_FONT_REGULAR_B64);
doc.addFont('LiberationSans-Regular.ttf', 'LiberationSans', 'normal');
doc.addFileToVFS('LiberationSans-Bold.ttf', CV_FONT_BOLD_B64);
doc.addFont('LiberationSans-Bold.ttf', 'LiberationSans', 'bold');
```

If PDF generation throws for any reason, the handler falls back to the browser's print dialog so the visitor can still capture the CV.

---

## Internationalization

The interface is fully bilingual, offering English and Turkish. Every translatable element is authored twice in the HTML, as an `.en-text` and a `.tr-text` span, and visibility is driven purely by CSS keyed off a `lang-en` class on `<body>`; switching languages toggles that one class with no page reload and no rerender. The selected language is written to a `lang` cookie so the preference persists across visits, with English presented by default. Language selection propagates through the whole experience, including the dynamically generated CV and the analytics dimension recorded on download.

---

## Analytics and Consent

The site integrates Google Analytics 4 and Microsoft Clarity for usage insight, both gated behind CookieHub consent so they respect the visitor's stated preferences. Beyond standard page views it records these custom events:

| Event | Fires when |
|-------|------------|
| `download_main_cv` | The visitor generates and downloads the CV (also tagged in Clarity with the CV language) |
| `contact_click` | A contact link (email or social) is clicked |
| `outbound_link_click` | An external link is followed |

Document interactions are additionally tagged in Clarity via a `document` custom tag when a file is opened in the viewer.

---

## Document Archive

The site includes a categorized, collapsible archive of academic and extracurricular documents, report cards, exam score reports, certificates, and awards, stored as PDFs under `files/`. Each entry can be opened in an inline PDF viewer without leaving the page, or downloaded directly. Filenames are shown verbatim so the source of each document is transparent, and sensitive reports are redacted before publishing (for example, the AP score report).

---

## Technology Stack

| Layer | Choice |
|-------|--------|
| Markup / styling / logic | Hand written HTML, CSS, and vanilla JavaScript in one file |
| PDF generation | jsPDF 4.0.0 (UMD, CDN) with an embedded Liberation Sans subset |
| Project data | GitHub REST API (`/users/{user}/repos`, `/repos/{user}/{repo}/readme`) |
| Analytics | Google Analytics 4 and Microsoft Clarity |
| Consent | CookieHub |
| Hosting / DNS | Cloudflare Pages with a `CNAME` custom domain |

No framework, no bundler, no package manager, and no backend are used anywhere in the project.

---

## Repository Structure

```text
.
├── index.html              Entire site: markup, styles, and scripts
├── favicon.ico             Multisize ICO favicon
├── favicon-32.png          PNG favicons (32 / 192 / 512)
├── favicon-192.png
├── favicon-512.png
├── apple-touch-icon.png    iOS home screen icon
├── CNAME                   Custom domain (dorukerel.com) for Cloudflare Pages
├── LICENSE                 License file
├── README.md               This document
└── files/                  Document archive (report cards, certificates, awards) as PDFs
```

---

## Deployment

The site is deployed on **Cloudflare Pages**. Any push to the repository, or a drag and drop upload of `index.html` and its assets, publishes the update. No build command is required. The `CNAME` file binds the custom domain `dorukerel.com` to the deployment.

---

## Local Development

No tooling is needed. Open `index.html` directly in a browser to run the site locally. Note that the GitHub API calls and the analytics scripts require an internet connection; without one, the Projects section falls back to its static featured list and the rest of the page works normally. Because unauthenticated GitHub calls are rate limited per IP, repeated hard refreshes during development can temporarily trigger the offline fallback; this is expected and clears on its own.

---

## License

© Doruk Erel. All rights reserved. The source is public for transparency, but the content (documents, texts, and personal materials) is not licensed for reuse.
