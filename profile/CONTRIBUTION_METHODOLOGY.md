# Contribution Methodology (Refresh Guide)

This document explains how contribution numbers in `README.md` are calculated and how to refresh them later.

## Scope

- Timeframe for README stats: `2025+` (from `2025-01-01` onward).
- Organizations covered:
  - `opentensor`
  - `bittensor-church`
- Team members currently included in OpenTensor calculations:
  - `ppolewicz`
  - `andreea-popescu-reef`
  - `zyzniewski-reef`
  - `konrad0960`

## OpenTensor Calculation Logic

We collect PR interaction sets per user using GitHub search:

```bash
gh search prs --author <user> --owner opentensor --limit 200 --json number,title,url,state,createdAt,repository
gh search prs --reviewed-by <user> --owner opentensor --limit 200 --json number,title,url,state,createdAt,repository
gh search prs --commenter <user> --owner opentensor --limit 200 --json number,title,url,state,createdAt,repository
gh search prs --involves <user> --owner opentensor --limit 200 --json number,title,url,state,createdAt,repository
```

For each PR, we use a stable key:

- `<repo>#<number>` (example: `opentensor/subtensor#2462`)

Then we classify de-duplicated team contributions:

- `Authored`: PR is authored by at least one team member.
- `Involved (Not Authored)`: PR is not team-authored, but at least one team member reviewed/commented/involved.
- `Total`: union of all team-touched PRs (authored + non-authored involvement).

State buckets are from GitHub PR `state`:

- `open`
- `merged`
- `closed` (closed without merge) 

Time filter for README:

- Keep only PRs where `createdAt >= 2025-01-01`.

README OpenTensor table values should be computed directly from query output:

- Build a deduplicated PR key set per category using `<repo>#<number>`.
- Filter to `createdAt >= 2025-01-01`.
- Count by state (`open`, `merged`, `closed`) and total.
- Fill README rows:
  - `Authored`
  - `Involved (Not Authored)`
  - `Total`

## bittensor-church Calculation Logic

This is org-level (not split by user).

1. List visible repositories:

```bash
gh repo list bittensor-church --limit 100 --json nameWithOwner,name,isPrivate,isArchived
```

2. For each repo, fetch all visible PRs:

```bash
gh api --paginate "repos/bittensor-church/<repo>/pulls?state=all&per_page=100"
```

3. Count `open` / `merged` / `closed` by `created_at` bucket.
4. For README, aggregate `2025` + `2026` into `2025+`.

README bittensor-church table values should be computed directly from API output:

- Collect all visible PRs from all visible `bittensor-church` repos.
- Keep PRs with `created_at >= 2025-01-01`.
- Count by state (`open`, `merged`, `closed`) and total.

## Link Generation Rules (README) 

README includes OpenTensor repo-level GitHub links for:

- `Authored by team`
- `All interactions by team, including authored`

Important constraints:

- Long search URLs can fail or return no results.
- Very long per-repo number lists should be split into `Part 1`, `Part 2`, etc.
- Numeric search links can occasionally produce false positives.

### btcli caveat

In the `Authored by team` section, `btcli` is intentionally **excluded**.

Reason:

- The authored `btcli` search link can include at least one PR not belonging to the intended team-authored set.
- Direct example: `https://github.com/opentensor/btcli/pulls?q=is%3Apr+403` also shows another PR that is not authored by our team.

In `All interactions`, `btcli` remains included because that section intentionally tracks any team interaction, not only authorship.

## Refresh Checklist

1. Re-run OpenTensor queries for all team members.
2. Compute OpenTensor 2025+ counts directly from raw query output.
3. Re-run bittensor-church org API collection and compute 2025+ counts directly.
4. Update `README.md`:
   - opentensor `2025+` table
   - bittensor-church `2025+` table
   - OpenTensor link lists
5. Verify links in browser:
   - especially long `bittensor` links and any `btcli` authored link behavior
6. Commit changes.
