# tdklijndata.nl — website

Static one-pager for TdKlijn Data. No build step, no dependencies: `index.html` plus
`images/logos/`. Everything is referenced with relative paths, so it works from a domain
root as well as from a repo subpath.

```
index.html          the whole site (inline CSS, no framework)
images/logos/       company logos used in the "Companies I've worked for" grid
.nojekyll           tells GitHub Pages to serve files as-is (no Jekyll processing)
```

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
