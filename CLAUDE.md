# tdlx-homepage

Hugo static site for tdlx.nl. Content in `content/`, templates in `layouts/`,
site config in `config.toml`.

## Merging to main deploys to production

`.github/workflows/hugo_deploy.yml` runs on every push to main and rsyncs `public/`
straight to the live server. There is no staging environment and no manual approval.

So: never commit or push to main directly. Branch, open a PR, and let the user merge.
`ci.yml` builds every PR. Both workflows pin `HUGO_VERSION: 0.165.0` (extended); keep
the two in sync when bumping.

Main moves fast here because sessions run in parallel, so fetch and rebase before every
push, not just when cutting the branch.

## Validate before pushing

```
hugo --quiet -d <scratchpad>/hugo-build
```

Must exit 0. For content or layout changes, also grep the generated HTML for what you
changed: new partial markup, links, meta tags.

## Layout trap

`layouts/index.html` does NOT use `baseof.html`. A partial added to `baseof.html` (for
example `cookieConsent.html`) is missing from the homepage unless it is added to
`index.html` by hand. This also bites after resolving a conflict by taking a whole file
from one side: re-check that branch-specific includes survived.

## Writing

No em dashes in site content or PR descriptions. Use a comma, colon, or period.
