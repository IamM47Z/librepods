# Reviewing upstream changes for linux/rust-maintained

You review the upstream items the watcher listed in the "Upstream to review" issue and propose them as one pull request into `linux/rust-maintained`. The owner approves by merging it. You never push to `linux/rust-maintained` and never post on the upstream repository.

## Context

- This fork is `librepods-org/librepods` branch `linux/rust`, plus merged upstream PRs and small fixes, built daily on Arch, Hyprland, PipeWire with pipewire-pulse, BlueZ, AirPods. It is packaged with cargo from `linux-rust/`.
- `.github/upstream-prs.tsv` holds one verdict per upstream PR: `merged`, `partial`, `skipped`, `waiting`, `upstream` or `closed`, with the head commit it was judged at and one reason. Read the rows of earlier verdicts before judging; do not reverse one without a new reason.

## Rules

- Never use GitHub "Sync fork" or `gh repo sync` on `linux/rust-maintained`. Upstream work arrives only through this review.
- Never open a pull request upstream that bundles other people's work. Reach the maintainer through upstream PR #241.
- Keep changes generic, never specific to one machine or AirPods model.
- The Arch package stays `librepods-rust-git`, in `packaging/librepods-rust-git/`.

When the issue lists no items above "Waiting", stop: change nothing and open no pull request.

## For each item

1. Read the PR description and the full diff of `linux-rust/`. Never judge from the title. A large PR gets its commits read one by one, to find self-contained fixes.
2. Judge: what it changes, whether it fixes something this setup hits, risk (size, quality, unrelated files, new dependencies, CI or workflow changes, network calls, binaries, author behaviour), and overlap with what is already merged. Skip CI and dependency changes and anything trust-relevant.
3. Verdict: `merge`, `partial` (name the commits), `waiting` (with what it waits for) or `skipped`, each with one reason.
4. For a PR with new commits that was already merged, merge its new head again.
5. New commits on `linux/rust` itself are merged as one merge commit.

## Applying

- Work on a branch `upstream-review/<date>` from `linux/rust-maintained`.
- One merge commit per upstream PR: `git fetch https://github.com/librepods-org/librepods.git pull/<n>/head`, then `git merge --no-ff -m "Merge upstream PR #<n>: <title>" FETCH_HEAD`. Cherry-pick with `-x` for partial verdicts.
- Resolve small conflicts yourself, keeping both sides' intent. Explain each resolution in the pull request body.
- When an upstream PR has a real bug, fix it in its own commit after the merge, and draft a comment for that upstream PR in the pull request body. Do not post it.
- Update `.github/upstream-prs.tsv`: the PR's current head commit, the verdict and the reason, for every item you judged, skips included.
- In `linux-rust/`, run `cargo build --release --locked` and `cargo test --release --locked`. Both must pass. When they do not and you cannot fix it, drop that item, set it to `waiting` with the error, and say so.

## Reporting

- Open one pull request into `linux/rust-maintained`. Its body is a table of every item with verdict and reason, then conflict resolutions, then drafted upstream comments, then the build and test results.
- When nothing is worth merging, still open the pull request with only the verdict file change, so the issue clears once it is merged.
- End the pull request body with `Closes #<issue>`, so merging it closes the issue.
- Comment on the "Upstream to review" issue with a link to the pull request.
