---
name: open-pr
description: Open or update a pull request for this repo. Use whenever work is ready to ship (user says "commit", "PR", "push", or approves changes). Never push to main directly; merging (and thus deploying) is the user's call.
---

# Open a PR for tdlx-homepage

Merging to main triggers the Deploy workflow that rsyncs straight to the live tdlx.nl server, so everything ships via PR and the user merges.

## Steps

1. **Sync first, always.** `git fetch origin` and check `git log --oneline HEAD..origin/main`. Other sessions and PRs land on main frequently in this repo; a branch cut hours ago is often already stale.
2. **Branch** from `origin/main` (or rebase the existing branch onto it): `git rebase origin/main`.
3. **Resolve conflicts with care.** Main's side is usually the newer redesign (CI pipeline, layouts). When taking a whole file from main (`git checkout --ours` during rebase), re-check that branch-specific includes survive. Known trap: `layouts/index.html` does NOT use `baseof.html`, so partials added to both (e.g. `cookieConsent.html`) must be re-added to `index.html` by hand after a whole-file resolution.
4. **Validate** before pushing: `hugo --quiet -d <scratchpad>/hugo-build` must exit 0. For content/layout changes, grep the generated HTML for what changed (new partial markup, links, meta tags).
5. **Push and open**: `git push -u origin <branch>` then `gh pr create` with a body listing changes and validation performed. Amended/rebased branches: `git push --force-with-lease`.
6. **Verify the PR is actually mergeable** (the step that gets forgotten):
   `gh pr view <n> --json mergeable,mergeStateStatus`
   - `CONFLICTING`: go back to step 2, rebase and resolve now, not later.
   - `MERGEABLE BLOCKED`: usually the CI build check still running; confirm with `gh pr checks <n>`.
   - Report the final state to the user with the PR URL.

## Notes

- CI: `ci.yml` builds PRs; `hugo_deploy.yml` deploys on push to main only. Hugo version is pinned in both (keep in sync).
- No em dashes in any user-facing text (site content, PR descriptions).
- Update this skill when the workflow bites again: add the failure to the "known trap" list in step 3 or the states in step 6.
