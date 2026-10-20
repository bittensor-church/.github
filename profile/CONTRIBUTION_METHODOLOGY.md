# Contribution Methodology (Refresh Guide)

This document explains how authored PR numbers in `README.md` are calculated and how to refresh them later.

## Scope

- Timeframe for README stats: `2025+` (from `2025-01-01` onward).
- Repository sets covered:
  - Current `opentensor` repositories and the former OpenTensor repositories listed below
  - Visible `bittensor-church` repositories
- Team members currently included in OpenTensor calculations:
  - `ppolewicz`
  - `andreea-popescu-reef`
  - `zyzniewski-reef`
  - `konrad0960`

## OpenTensor Calculation Logic

We collect authored PR sets per user using GitHub search. Several former
`opentensor` repositories have moved, so search both current and former owners:

```bash
gh search prs --author <user> --owner opentensor --limit 200 --json number,title,url,state,createdAt,repository
gh search prs --author <user> --owner RaoFoundation --limit 200 --json number,title,url,state,createdAt,repository
gh search prs --author <user> --repo latent-to/subnet-template --limit 200 --json number,title,url,state,createdAt,repository
```

Include all current `opentensor` repositories. Of results under other owners,
include only these former OpenTensor repositories: `RaoFoundation/async-substrate-interface`,
`RaoFoundation/bittensor`, `RaoFoundation/bittensor-subnet-template`,
`RaoFoundation/btcli`, `RaoFoundation/subtensor`, and `latent-to/subnet-template`.
Check redirects from existing README links when refreshing this list.

For each PR, we use a stable key:

- `<current repo>#<number>` (example: `RaoFoundation/subtensor#2462`)

Then we classify de-duplicated team-authored contributions:

- `Authored`: PR is authored by at least one team member.

State buckets are from GitHub PR `state`:

- `open`
- `merged`
- `closed` (closed without merge) 

Time filter for README:

- Keep only PRs where `createdAt >= 2025-01-01`.

README OpenTensor table values should be computed directly from query output:

- Build a deduplicated authored PR key set using `<repo>#<number>`.
- Filter to `createdAt >= 2025-01-01`.
- Count by state (`open`, `merged`, `closed`) and total.
- Fill the README `Authored` row.

## bittensor-church Calculation Logic

This is org-level (not split by user).

1. List visible repositories:

```bash
gh repo list bittensor-church --limit 1000 --json nameWithOwner,name,isPrivate,isArchived
```

2. For each repo, fetch all visible PRs:

```bash
gh api --paginate "repos/bittensor-church/<repo>/pulls?state=all&per_page=100"
```

3. Count `merged` when `merged_at` is set; otherwise use `state` (`open` or `closed`).
4. For README, aggregate `2025` + `2026` into `2025+`.

README bittensor-church table values should be computed directly from API output:

- Collect all visible PRs from all visible `bittensor-church` repos.
- Keep PRs with `created_at >= 2025-01-01`.
- Count by state (`open`, `merged`, `closed`) and total.

## Link Generation Rules (README) 

README includes links for every counted OpenTensor-origin team PR under:

- `Authored by team`

Important constraints:

- Long search URLs can fail or return no results.
- Very long per-repo number lists should be split into `Part 1`, `Part 2`, etc.
- Numeric search links can occasionally produce false positives.

### btcli caveat

Link `RaoFoundation/btcli#403` directly. A numeric search for `403` also
shows a PR outside the team-authored set, so the search URL is misleading.

## Refresh Checklist

1. Re-run OpenTensor and transferred-repo queries for all team members.
2. Compute OpenTensor 2025+ authored counts directly from raw query output.
3. Re-run bittensor-church org API collection and compute 2025+ counts directly.
4. Update `README.md`:
   - opentensor `2025+` authored row
   - bittensor-church `2025+` table
   - OpenTensor authored link list
5. Verify links in browser:
   - especially long `bittensor` links and any `btcli` authored link behavior
6. Review `git diff` before committing.
