# gyankosh-site

Corporate one-pager for **gyankosh.ca** — the Gyankosh Inc. org home.

Static HTML. No build step, no dependencies, no framework, no JavaScript.

## Why this exists

`gyankosh.ca` is the holding-brand domain (`Brand_Guidelines.md` § Domain Strategy) —
deliberately **not** consumer-facing. Products targeting different audiences get their
own names and domains.

The page's job is to represent Gyankosh Inc. as a real operating company. Google Play
organization-account verification and the Dun & Bradstreet record both cite
`https://gyankosh.ca` as the company website, so it needs to resolve to something
credible.

## Structure

```
index.html                 Entire page — content and CSS inline
netlify.toml               Publish config + security headers
favicon.ico
assets/
  logo-lockup.svg          Full lockup for dark grounds (white GYANKOSH wordmark)
  favicon.png
  fonts/                   Self-hosted woff2, no external requests
    spectral-300/600/700   Display serif
    inter-400/600          Body sans
    plexmono-400           Letterspaced labels and small caps
    yatra-one-400          Devanagari, motto only
```

### Logo

`logo-lockup.svg` is a copy of the shared Gyankosh logo, which also lives in
`Smriti/2-Operations/Brand/` and in the Academy repos. **This copy has one fix the
others don't:** a `viewBox` attribute. Every other copy in the workspace ships with
`width`/`height` but no `viewBox`, so the artwork will not scale — it blurs or clips
when sized with CSS. If you re-copy from source, re-add:

```
viewBox="0 0 293.91422 191.2019"
```

Note there are two different "dark" assets in the workspace and they are not
interchangeable:

| Asset | What it is |
|---|---|
| `Smriti/.../Logo-Dark_Gyankosh.png` | Full lockup for dark grounds — white wordmark |
| `academy/public/logo-dark.png` | Mark only, cropped, no wordmark |
| `logo-dark.svg` (all copies) | Full lockup, vector — the one this site uses |

### Fonts

All self-hosted from `Buddhi/2-Areas/Gyankosh_Academy/Templates/`. Latin subsets only.

`yatra-one-400.woff2` is from Google Fonts, subset with `pyftsubset` to just the
glyphs in the motto and `ज्ञानकोश` — 12.6 KB rather than ~120 KB. To change the
motto text you must re-subset, or characters will silently fall back to a system
Devanagari font mid-line:

```
python -m fontTools.subset yatra-raw.woff2 --text="॥ज्ञानम्परमंध्येयम्कोश" \
  --layout-features="*" --flavor=woff2 --output-file=yatra-one-400.woff2
```

Yatra One ships a single weight. Keep `font-weight: 400` on anything using it —
synthetic bolding smears the brush strokes.

## Design

Follows the Academy **Oxford Slate** cover treatment, not the corporate letterhead
styling. See `Brand_Guidelines.md` § Corporate Website for why, and
`Templates/Academy-Deck-System/Deck - Oxford Slate.html` for the source values.

Committed light design with dark bands — no `prefers-color-scheme` switching. All
colors are set explicitly so a browser's dark mode cannot distort it.

## Deploy

Netlify, connected to this repo. Repo must be **public** — Netlify's free Starter plan
cannot connect private org-owned repos (`Decisions_Log.md:225`). Nothing here is
sensitive. If the repo is ever made private, Netlify Pro becomes a prerequisite.

No build command; publish directory is the repo root.

**DNS** stays at Namecheap. Apex `gyankosh.ca` points at Netlify via an ALIAS record.
Do not touch the MX records (`mx1`/`mx2.improvmx.com`) or the SPF TXT on `@` —
those carry all inbound mail and Resend's outbound authorization.

## Content policy

- **No product names.** Curio, Stacks, Times Ninja, and the PDF editor are unreleased
  and codenamed. Add products only once a name is final and public.
- **No client work.** Gyankosh does not do contract development. "Custom development"
  in the D&B record and NAICS 541511 refers to building our own applications; the site
  must not imply commissions are accepted.
- The **legal address is deliberately city-level only** (Brampton, Ontario). The full
  street address appears on the letterhead and will be published by Google Play for
  organization accounts, but is not put on the website.

## Local preview

```
python -m http.server 8765 --directory .
```

Opening `index.html` directly also works — all paths are relative.
