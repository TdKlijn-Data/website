# tdklijndata.nl — website

Static one-pager for TdKlijn Data. No build step, no dependencies: `index.html` plus
`images/logos/`. Everything is referenced with relative paths, so it works from a domain
root as well as from a repo subpath.

```
index.html            the whole site (inline CSS, no framework)
images/logos/         company logos used in the "Companies I've worked for" grid
fonts/                IBM Plex Sans (self-hosted variable woff2) + its OFL licence
favicon.svg           icon, built from the wordmark glyphs (modern browsers)
favicon.ico           16/32/48 raster fallback for older browsers
apple-touch-icon.png  180px iOS home-screen icon
og-image.png          1200x630 social preview card (Open Graph)
og-source.html        generator for og-image.png; not part of the site (noindex)
.nojekyll             tells GitHub Pages to serve files as-is (no Jekyll processing)
```

## Social preview (Open Graph)

`index.html` declares Open Graph + Twitter card metadata, so a shared link renders as a card
with the wordmark instead of a bare URL. The image is `og-image.png` (1200x630).

**Absolute URLs:** `og:url`, `og:image`, `twitter:image` and `link rel=canonical` currently
point at `https://tdklijn-data.github.io/website/` (scrapers require absolute URLs). When
`tdklijndata.nl` serves the site, change those four values in the `<head>` to
`https://tdklijndata.nl/` and `https://tdklijndata.nl/og-image.png`.

To regenerate the image after a copy change, open `og-source.html` in a 1200x630 viewport and
screenshot it (it uses the same self-hosted font and the wordmark from `index.html`).

## Fonts

IBM Plex Sans is self-hosted, so the page makes no third-party requests at all (relevant for
GDPR, and no extra DNS/TLS handshakes on first paint).

`fonts/ibm-plex-sans-latin.woff2` is the **latin subset variable font** (wght 100–700, 45 KB) —
it covers all three weights the site uses (400/500/600) in one file. The `@font-face` rule
lives in the inline `<style>` and declares a `unicode-range`, so scripts outside Latin fall
back to the system font instead of downloading glyphs they cannot use.

To update it, pull `src: url(...)` for the `latin` subset from the Google Fonts CSS API:

```sh
curl -A "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120 Safari/537.36" \
  "https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@400;500;600&display=swap"
```

The last `woff2` of each weight block is the latin subset — they are all the same file.
Licence: SIL Open Font License 1.1, see `fonts/OFL.txt` (must stay with the font).

## Local preview

```sh
python3 -m http.server 8000
# open http://localhost:8000
```

## Before publishing

No placeholders left. The footer of `index.html` carries the registration number:

```html
TdKlijn Data B.V. · KvK 42209778
```

The VAT number was removed for now. If it should appear later, append it to the same line:

```html
TdKlijn Data B.V. · KvK 42209778 · VAT NL123456789B01
```

Also worth a check: the contact address (`info@tdklijndata.nl`) and the LinkedIn URL.

## Publishing to GitHub Pages

Repo: `TdKlijn-Data/website` (currently **private**, org on the **Free** plan).

GitHub Pages needs either a **public** repo or a **paid** plan. Pick one:

### Option A — make the repo public, then enable Pages (free)

```sh
gh repo edit TdKlijn-Data/website --visibility public --accept-visibility-change-consequences
gh api -X POST repos/TdKlijn-Data/website/pages \
  -f source[branch]=main -f source[path]=/
```

Site goes live at <https://tdklijn-data.github.io/website/> after the first build
(~1 min). Check status with:

```sh
gh api repos/TdKlijn-Data/website/pages/builds/latest --jq '.status'
```

### Option B — keep it private, upgrade the org to GitHub Team

Same `gh api` call as above; Pages from a private repo requires Team or higher.

### Option C — keep it private, host somewhere else

Cloudflare Pages or Netlify both serve private GitHub repos on free tiers; connect the repo,
leave the build command empty and set the output directory to `/`.

## Custom domain (tdklijndata.nl)

Only after the site is live on `github.io`:

1. Add a file `CNAME` containing just `tdklijndata.nl` (no protocol, no path), commit, push.
2. DNS at the registrar:

   | Type  | Name | Value                                    |
   |-------|------|------------------------------------------|
   | A     | @    | 185.199.108.153                          |
   | A     | @    | 185.199.109.153                          |
   | A     | @    | 185.199.110.153                          |
   | A     | @    | 185.199.111.153                          |
   | CNAME | www  | tdklijn-data.github.io                   |

3. Wait for DNS, then tick **Enforce HTTPS** in the repo's Pages settings.

`www` and the apex both work; pick one as canonical and redirect the other in the Pages
settings.

> Alternative: rename the repo to `tdklijn-data.github.io` to serve the site at the
> org root (`https://tdklijn-data.github.io/`) instead of `/website/`.
