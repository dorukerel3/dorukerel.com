# dorukerel.com — Academic Portfolio & CV

Personal portfolio and CV website of **Doruk Erel**, a science high school student in Ankara, Turkey, focused on embedded systems, hardware design, and network administration.

**Live site:** [https://dorukerel.com](https://dorukerel.com)

## Features

- **Single-file architecture** — the entire site (HTML, CSS, JavaScript) lives in one `index.html` with no build step and no external dependencies beyond CDN libraries
- **Bilingual (EN/TR)** — instant language switching via a slide toggle, with the choice persisted in a cookie; English by default
- **Live GitHub projects** — the Projects section fetches public repositories from the GitHub API on every page load, so it stays current without manual updates
- **Client-side CV generation** — the "Download CV" button builds a one-page, print-quality PDF entirely in the browser using jsPDF, in whichever language the page is currently displaying. The CV pulls the bio and project list live from the page itself, and embeds a subsetted Liberation Sans font for full Turkish character support (ı, ş, ğ, İ)
- **Document archive** — categorized, collapsible archive of report cards, certificates, and awards with an in-page PDF viewer and per-file downloads
- **Analytics** — Google Analytics 4 and Microsoft Clarity, with custom events for CV downloads, document views/downloads, outbound link clicks, and contact clicks; cookie consent managed by CookieHub

## Repository structure

```
├── index.html              Entire site: markup, styles, and scripts
├── favicon.ico             Multi-size ICO favicon
├── favicon-32.png          PNG favicons (32 / 192 / 512)
├── favicon-192.png
├── favicon-512.png
├── apple-touch-icon.png    iOS home screen icon
├── CNAME                   Custom domain configuration
└── files/                  Document archive (PDFs) and profile photo
```

## Deployment

The site is deployed on **Cloudflare Pages**. Any push to the repository (or drag-and-drop upload of `index.html` and assets) publishes the update. No build command is required.

## Local development

No tooling needed — open `index.html` directly in a browser. Note that the GitHub API fetch and analytics scripts require an internet connection; without one, the Projects section falls back to a static featured list.

## License

© Doruk Erel. All rights reserved. The source is public for transparency, but the content (documents, texts, and personal materials) is not licensed for reuse.
