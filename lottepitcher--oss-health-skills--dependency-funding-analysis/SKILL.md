---
name: dependency-funding-analysis
description: Analyze funding needs across multiple GitHub repos to identify maintainers who need support Use when this capability is needed.
metadata:
  author: lottepitcher
---

# Dependency Funding Analysis

Analyze open source dependencies to identify maintainers who may need financial support and are positioned to use it effectively.

> **IMPORTANT:** Always verify financial data before reporting. JSON APIs can have decimal parsing errors that produce 10x-100x incorrect values. Cross-check Open Collective figures by visiting the actual webpage, and apply sanity checks (e.g., <100 backers rarely means >$50K/year). See "Data Verification" section below.

## Prerequisites

This skill requires the GitHub CLI (`gh`) to be installed and authenticated.

### Check Prerequisites

Run this first to verify your setup:
```bash
gh --version && gh auth status
```

If `gh` is not installed, you'll see "command not found". Install it from: https://cli.github.com

If `gh` is not authenticated, run:
```bash
gh auth login
```

### Without gh CLI

Without `gh`, this skill cannot function. All data gathering (stars, sponsors, funding links, contributor stats) requires authenticated GitHub API access via `gh api graphql`.

## Platform Compatibility

**Check the platform first.** On Windows, avoid:
- `base64 -d` - doesn't exist
- `grep -oP` - Perl regex not available
- Complex piping with `while read` loops

**Recommended for all platforms:**
- Use `gh api graphql` queries (work everywhere)
- Fetch README via raw GitHub URL: `https://raw.githubusercontent.com/{owner}/{repo}/main/README.md`
- Use WebFetch instead of curl+grep for content analysis

## Usage

```
/dependency-funding-analysis <package-list>
```

Where `<package-list>` is either:
- A comma-separated list of GitHub repos (e.g., `lodash/lodash, expressjs/express`)
- A path to a package.json, requirements.txt, or similar dependency file

## Scoring Criteria

| Category | Signal | Points | Rationale |
|----------|--------|--------|-----------|
| **Solo/Small Team** | 1 person did 80%+ of commits (last 12mo) | +3 | High bus factor risk |
| | 2-3 active maintainers | +1 | Small team, still stretched |
| **Active but Struggling** | Open issues > 50 with slow response | +2 | Overwhelmed |
| | PR backlog > 20 open PRs | +2 | Can't keep up |
| | Recent commits but long gaps | +1 | Sporadic availability |
| **Funding Status** | No funding infrastructure | -1 | May not want/use donations |
| | Has FUNDING.yml but <10 sponsors | +3 | Set up but underfunded |
| | Has FUNDING.yml with 10-30 sponsors | +1 | Partially funded |
| | Has FUNDING.yml with 30+ sponsors | -1 | Likely adequately funded |
| **Funding Gap** | High stars (>5k) but <10 sponsors | +2 | Popular but unsupported |
| | High stars (>5k) but <$5k/yr on OC | +2 | High impact, low funding |
| **Not Abandoned** | Commits in last 6 months | Required | Filter out dead projects |
| | Responds to issues (even if slow) | +1 | Engaged maintainer |
| **High Impact** | High dependent count or downloads | +1 | Your money goes further |
| **Signs of Burnout** | "Looking for maintainers" in README | +3 | Explicitly needs help |
| | Declining response times vs historical | +2 | Reduced capacity |
| | Maintainer mentions needing help/funding in README | +2 | Explicit signal |

## Data Gathering with gh CLI

### Single GraphQL Query (Gets Most Data)

This one query replaces 5+ REST calls:

```bash
gh api graphql -f query='
query($owner: String!, $repo: String!) {
  repository(owner: $owner, name: $repo) {
    stargazerCount
    forkCount
    isArchived
    pushedAt
    description

    issues(states: OPEN) { totalCount }
    pullRequests(states: OPEN) { totalCount }

    fundingLinks {
      platform
      url
    }

    defaultBranchRef {
      target {
        ... on Commit {
          history(first: 1) {
            nodes { committedDate }
          }
        }
      }
    }

    owner {
      ... on User {
        login
        hasSponsorsListing
        sponsors { totalCount }
      }
      ... on Organization {
        login
        hasSponsorsListing
        sponsors { totalCount }
      }
    }
  }
}
' -f owner=OWNER -f repo=REPO
```

### Batch Query (Multiple Repos at Once)

Analyze up to 10 repos in a single API call:

```bash
gh api graphql -f query='
query {
  repo1: repository(owner: "owner1", name: "repo1") { ...RepoFields }
  repo2: repository(owner: "owner2", name: "repo2") { ...RepoFields }
  repo3: repository(owner: "owner3", name: "repo3") { ...RepoFields }
}

fragment RepoFields on Repository {
  nameWithOwner
  stargazerCount
  isArchived
  pushedAt
  issues(states: OPEN) { totalCount }
  pullRequests(states: OPEN) { totalCount }
  fundingLinks { platform url }
  owner {
    ... on User { login hasSponsorsListing sponsors { totalCount } }
    ... on Organization { login hasSponsorsListing sponsors { totalCount } }
  }
}
'
```

### Get Sponsor Count for a User/Org

```bash
gh api graphql -f query='
query($login: String!) {
  user(login: $login) {
    sponsors(first: 0) { totalCount }
    sponsorsListing {
      fullDescription
      activeGoal { targetValue percentComplete }
    }
  }
}
' -f login=USERNAME
```

For organizations:
```bash
gh api graphql -f query='
query($login: String!) {
  organization(login: $login) {
    sponsors(first: 0) { totalCount }
    sponsorsListing {
      activeGoal { targetValue percentComplete }
    }
  }
}
' -f login=ORGNAME
```

### Get Top Contributors (Bus Factor)

```bash
gh api repos/{owner}/{repo}/stats/contributors --jq '
  sort_by(.total) | reverse | .[0:5] |
  map({author: .author.login, commits: .total})
'
```

### Check README for Burnout Signals

**Cross-platform (use raw URL + WebFetch):**
```
https://raw.githubusercontent.com/{owner}/{repo}/main/README.md
```
Use WebFetch to analyze the README content for burnout signals like "looking for maintainers", "help wanted", "sponsor", etc.

**Unix/macOS only:**
```bash
gh api repos/{owner}/{repo}/readme --jq '.content' | base64 -d | \
  grep -iE "(looking for maintainers|help wanted|sponsor|donate|free time|nights and weekends|need help)"
```

### Get Open Collective Data

If fundingLinks includes Open Collective:
```bash
curl -s "https://opencollective.com/{project}.json" | \
  jq '{backers: .backersCount, balance: .balance, yearlyBudget: .yearlyBudget}'
```

Or use their GraphQL API:
```bash
curl -s "https://api.opencollective.com/graphql/v2" \
  -H "Content-Type: application/json" \
  -d '{"query": "query { collective(slug: \"PROJECT\") { stats { backers { all } yearlyBudget { value } balance { value } } } }"}'
```

**WARNING:** JSON endpoints can return values with incorrect decimal placement. Always:
1. Sanity-check: 58 backers does not equal $250K/year (more like $2.5K/year)
2. Verify by visiting `https://opencollective.com/{slug}` directly
3. Look for "Total raised" figure on the actual page

## Funding Reality Check

| Stars | Sponsors | Assessment |
|-------|----------|------------|
| >10k | <10 | Severely underfunded |
| >5k | <10 | Underfunded |
| >1k | <5 | Underfunded |
| Any | 0-2 | Critically underfunded |
| Any | 30+ | Likely adequate |

## Output Format

### Summary Table (ordered by score, highest first)

| Rank | Repository | Stars | Score | Sponsors | Funding Gap | Key Signals |
|------|------------|-------|-------|----------|-------------|-------------|
| 1 | owner/repo | 17.5k | 10 | 5 | Severe | Solo maintainer, 90% commits |
| 2 | owner/repo | 800 | 8 | 0 | Critical | No funding, burnout message |

### Detailed Report (for top candidates)

```
## {repository}

**Score:** X/15
**Recommendation:** High/Medium/Low priority for funding

### Maintainer Analysis
- Active maintainers: X
- Bus factor: X% commits by top contributor
- Maintainer file: Yes/No

### Funding Status
- Funding platforms: [list from fundingLinks]
- **GitHub Sponsors:** X sponsors
- **Open Collective:** $X/year, Y backers
- **Funding Gap:** None / Moderate / Severe / Critical

### Funding Gap Analysis
Compare popularity vs actual funding:
- Stars: X - Expected sponsors: ~Y
- Actual sponsors: Z
- Gap: [Severe/Moderate/None]

### Health Indicators
- Open issues: X
- Open PRs: X
- Last commit: date
- Archived: Yes/No

### Key Signals
- [List notable findings]
- [Include any README mentions of needing help/funding]

### How to Support
- [Direct links to funding platforms]
```

## Exclusion Criteria

Skip repositories where:
- `isArchived: true`
- `pushedAt` > 6 months ago
- Owner is: microsoft, google, facebook, meta, amazon, aws, apple, dotnet, golang, rust-lang
- `sponsors.totalCount` > 50

## Example Workflow

```bash
# 1. Quick scan of multiple repos
gh api graphql -f query='...' # batch query above

# 2. For repos with hasSponsorsListing=true but low sponsor count, dig deeper
gh api graphql -f query='...' -f login=maintainer

# 3. Check README for distress signals
gh api repos/owner/repo/readme --jq '.content' | base64 -d | grep -i "help\|sponsor\|maintain"

# 4. Get contributor stats for bus factor
gh api repos/owner/repo/stats/contributors --jq 'sort_by(.total) | reverse | .[0:3]'
```

## Data Verification (CRITICAL)

**Always verify financial data from multiple sources.** JSON APIs and automated parsing can misread decimal formatting, leading to 10x-100x errors.

### Sanity Checks

Before reporting financial figures, apply these sanity checks:

| Backers/Sponsors | Plausible Yearly Income |
|------------------|------------------------|
| <10 | <$5,000 |
| 10-50 | $1,000-$25,000 |
| 50-200 | $10,000-$100,000 |
| 200+ | $50,000+ |

**Red flags that indicate bad data:**
- <100 backers but >$100K/year claimed - likely 100x error
- Balance > 10x yearly income - verify decimal placement
- Figures that seem implausibly high for project size

### Verification Steps

1. **Cross-check Open Collective data** by visiting the actual page:
   ```
   https://opencollective.com/{collective-slug}
   ```
   Look for "Total raised" and "Yearly budget" displayed on the page.

2. **Never trust JSON endpoint figures alone** - the `.json` endpoint can have parsing issues with decimal formatting in different locales.

3. **Compare against GitHub Sponsors count** - if a project has 15 GitHub sponsors, they're unlikely to have $250K on Open Collective.

4. **When in doubt, report ranges** - say "$2K-5K/year" rather than an exact figure you're uncertain about.

### Open Collective Verification

Instead of relying solely on the JSON API, verify by fetching the human-readable page:

```bash
# Fetch the actual Open Collective page and extract figures
curl -s "https://opencollective.com/{slug}" | grep -oP '(?<=Total raised).*?\$[\d,.]+'
```

Or manually verify at: `https://opencollective.com/{slug}`

## Notes

- GraphQL rate limit: 5,000 points/hour (most queries cost 1 point)
- Batch queries are much more efficient than individual calls
- Cache results when analyzing the same repos multiple times
- The `sponsors.totalCount` field requires the repo owner to have sponsorship enabled
- **Always sanity-check financial figures before reporting them**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lottepitcher) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
