# Portfolio — Samyak Shakya

Personal portfolio site for Samyak Shakya (QA engineer & seasonal wedding photographer,
Kathmandu). Static site, no build step, hosted on **GitHub Pages** behind the custom
domain **shakyasamyak.com.np**.

## Repository layout

- `index.html` — the whole site: one page with a CSS-only QA/Photographer mode toggle.
  Page-specific CSS lives in its inline `<style>`; a small inline script handles the
  contact form and a baseline-grid debug overlay.
- `styles.css` — design-system stylesheet (colors, spacing, `.btn`, `.tag`, `.seg`,
  `.nav`, form fields). Shared tokens are CSS variables on `:root`.
- `fonts/` — Archivo variable font, three unicode-range subsets (latin, latin-ext,
  vietnamese) shared by all weights. Referenced from `@font-face` in `styles.css`.
- `assets/` — (to be added) wedding photos, résumé PDF lives at repo root.
- `CNAME` — custom domain for GitHub Pages; must contain exactly `shakyasamyak.com.np`.

The original Claude-artifact export this site was unbundled from is preserved in git
history (first commit).

## Conventions

- No framework, no build system, no dependencies. Keep it that way — plain HTML/CSS
  with minimal vanilla JS.
- Vertical rhythm is a 28px baseline grid (`--leading`); page CSS aligns to it with
  `text-box: trim-both`. Press `b` on the page (or add `?baselines` to the URL) to
  see the grid overlay when adjusting spacing.
- The QA/Photographer toggle is pure CSS (`body:has(#mode-qa:checked)` hides the
  other panel) — don't reintroduce JS for it.
- Photo placeholders are `<div class="ph">` boxes; each has an adjacent HTML comment
  showing the `<img>` line to swap in when a real photo lands. Photos should go in
  `assets/`, compressed to roughly 200–400 KB (WebP or JPEG), `loading="lazy"`,
  meaningful `alt` text.

## Local preview

```sh
python3 -m http.server 8000   # then http://localhost:8000
```

(Opening `index.html` directly also works; fonts load fine from file://.)

## Deployment (GitHub Pages + custom domain)

1. Push to GitHub, then Settings → Pages → Deploy from branch → `main` / root.
2. Set the custom domain to `shakyasamyak.com.np` (the `CNAME` file must match).
3. DNS — `.com.np` domains from register.com.np require external nameservers
   (Cloudflare free tier is the usual choice):
   - Apex `shakyasamyak.com.np` → A records `185.199.108.153`, `185.199.109.153`,
     `185.199.110.153`, `185.199.111.153` (optionally AAAA `2606:50c0:8000::153`
     through `:8003::153`).
   - `www` → CNAME `<github-username>.github.io`.
   - On Cloudflare, keep records DNS-only (grey cloud) until GitHub issues the TLS
     cert, then enable "Enforce HTTPS" in Pages settings.
4. Email at the domain (`shakyasamyak21@gmail.com`) is separate from Pages —
   Cloudflare Email Routing can forward it to a personal inbox for free.

## Launch checklist (remaining)

- [ ] Add `resume.pdf` at repo root — all three résumé buttons already point to it.
- [ ] Replace the stock Unsplash placeholders in `assets/` with real photos —
      overwrite each `wedding-*.jpg` keeping the same filename; no HTML changes
      needed.
- [ ] Add an `og:image` (a 1200×630 card) once a lead photo exists.
- [ ] Verify `shakyasamyak21@gmail.com` actually receives mail before launch.
- [ ] Optional: replace the mailto contact form with a real endpoint
      (Formspree/Web3Forms) if mailto proves unreliable for visitors.
