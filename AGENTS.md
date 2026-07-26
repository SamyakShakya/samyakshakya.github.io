# Portfolio — Samyak Shakya

Personal portfolio site for Samyak Shakya (QA engineer & seasonal wedding photographer,
Kathmandu). The end goal is a static site hosted on **GitHub Pages** behind the custom
domain **shakyasamyak.com.np**.

## Repository layout

- `index.html` — the entire site, exported as a self-contained "bundled" artifact.
  It is NOT plain HTML: the real page markup, CSS, and JS are stored as
  base64/zlib-compressed payloads inside `<script type="__bundler/manifest">` and
  `<script type="__bundler/template">` tags, and a loader script unpacks them into
  blob URLs at runtime. The visible ~389 lines are mostly the loader.
- `AGENTS.md` — this file.

There is no build system, package.json, or framework. Everything is static.

## Working with the bundle

Do not hand-edit the base64 payloads. To change page content or styles:

1. Extract: parse the JSON inside `script[type="__bundler/template"]` (the page HTML,
   ~13.6 KB) and `script[type="__bundler/manifest"]` (a `{uuid: {mime, compressed,
   data}}` map; `data` is base64, `compressed: true` means raw zlib — Python:
   `zlib.decompress(base64.b64decode(data))`).
2. Edit the extracted template/CSS/JS.
3. Either re-pack (reverse the steps: recompress, re-base64, re-embed the JSON), or —
   preferred for this repo's future — **unbundle permanently** into plain
   `index.html` + `styles.css` + `site.js` + `assets/` so the site is crawlable,
   diff-able, and editable without the extract/re-pack dance.

Bundle contents (current):
- Template: page markup (hero, QA/Photographer mode toggle, feature rows, tools,
  résumé banner, photo gallery, contact form, footer).
- CSS (~337 KB): design-system stylesheet; the bulk is 9 embedded `@font-face`
  Archivo font payloads.
- JS: design-system runtime + `<image-slot>` custom element (user-fillable image
  placeholders — all six gallery slots and the lead photo are still empty).

## Local preview

No server needed for the bundled file — open `index.html` directly, or:

```sh
python3 -m http.server 8000   # then http://localhost:8000
```

The page shows "Unpacking…" briefly, then renders. JavaScript is required
(a `<noscript>` notice exists). Press `b` on the page to toggle a baseline grid
(debug aid); `?baselines` in the URL does the same.

## Site structure & behavior

- Single page, two "modes" switched by a CSS-only radio toggle
  (`body:has(#mode-qa:checked)` / `#mode-photo`) — QA panel is default.
- Anchors: `#work` (mode toggle), `#skills` (QA features), `#contact`.
- Contact form is inert (`onsubmit="return false"`) — no backend yet.
- Both "Download résumé" buttons point to `#` — the résumé PDF does not exist
  in the repo yet.

## Deployment (GitHub Pages + custom domain)

Target setup:

1. GitHub repo (e.g. `samyak/portfolio` or `<user>.github.io`), default branch `main`,
   Pages enabled: Settings → Pages → Deploy from branch → `main` / root.
2. `CNAME` file at repo root containing exactly: `shakyasamyak.com.np`
3. DNS at the domain's nameserver (`.com.np` domains from register.com.np require
   external nameservers — Cloudflare free tier works well):
   - Apex `shakyasamyak.com.np` → A records `185.199.108.153`, `185.199.109.153`,
     `185.199.110.153`, `185.199.111.153` (optionally AAAA `2606:50c0:8000::153`
     through `:8003::153`).
   - `www` → CNAME `<github-username>.github.io`.
   - If using Cloudflare, set records to DNS-only (grey cloud) until GitHub issues
     the TLS cert, then proxying is optional.
4. In GitHub Pages settings, set the custom domain and enable "Enforce HTTPS"
   (available once the cert is provisioned, usually within an hour of DNS resolving).

## Known TODOs before the site is launch-ready

- Fill the 7 `<image-slot>` placeholders with real wedding photos (or hide the
  Photographer panel until photos exist).
- Add the résumé PDF and point both download buttons at it.
- Wire the contact form to a backend (Formspree/Web3Forms/Netlify-style endpoint)
  or replace it with a plain `mailto:` link.
- Add SEO/meta: `<meta name="description">`, Open Graph/Twitter tags, favicon,
  canonical URL — easiest after unbundling to plain HTML.
- Mobile nav: below 560 px the nav links are hidden with no replacement menu.
