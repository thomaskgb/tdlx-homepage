---
name: open-pr
description: Open or update a pull request for tdlx-homepage. Use whenever work is ready to ship (user says "commit", "PR", "push", or approves changes). Never push to main directly; merging (and thus deploying) is the user's call.
---

# Open a PR for tdlx-homepage

Repo-specific additions to the global `open-pr` skill, which owns the generic flow
(sync, rebase, validate, push, verify mergeable). Everything below is what this repo
does differently.

## Merging deploys

`hugo_deploy.yml` runs on push to main and rsyncs straight to the live tdlx.nl server.
There is no staging step, so every change ships via PR and the user merges. Say so when
handing the PR back.

## Validation

`hugo --quiet -d <scratchpad>/hugo-build` must exit 0 before pushing. For content or
layout changes, grep the generated HTML for what changed: new partial markup, links,
meta tags.

CI: `ci.yml` builds PRs, `hugo_deploy.yml` deploys on push to main. The Hugo version is
pinned in both; keep them in sync.

## Known traps

- Main moves fast here (parallel sessions), so a branch cut hours ago is usually stale.
  Fetch and rebase before every push, not just the first.
- Conflicts: main's side is usually the newer redesign (CI pipeline, layouts). After
  taking a whole file from main (`git checkout --ours` during a rebase), re-check that
  branch-specific includes survived. `layouts/index.html` does NOT use `baseof.html`, so
  a partial added to both (for example `cookieConsent.html`) has to be re-added to
  `index.html` by hand.

Update this file when the workflow bites again. Generic lessons belong in the global
skill instead.
