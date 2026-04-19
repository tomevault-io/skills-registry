---
name: dependency-extractor
description: Extract dependencies from projects/SBOMs and map them to GitHub repositories Use when this capability is needed.
metadata:
  author: lottepitcher
---

# Dependency Extractor

Extract dependencies from a project using SBOM (Software Bill of Materials) tools and map them to GitHub repositories.

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

Without `gh`, this skill has severely limited functionality:
- Cannot scan GitHub repos for dependency files
- Cannot fetch file contents from GitHub
- Can still parse local SBOM files using Node.js scripts
- Can still query package registries (npm, PyPI, etc.) via curl

## Platform Compatibility

**Check the platform first** before running commands. Many bash commands fail on Windows.

### Platform Detection
```bash
# Returns: win32, darwin, or linux
node -e "console.log(process.platform)"
```

### Windows Limitations
On Windows, avoid these patterns that will fail:
- `base64 -d` - doesn't exist
- `grep -oP` - Perl regex not available
- Complex jq escaping with `\\.` - escaping differs
- Piping to `while read` loops - breaks

### Recommended Approach (All Platforms)
**Use raw GitHub URLs instead of base64 decoding:**
```
https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{path}
```

This works everywhere and avoids all encoding issues. Use WebFetch or curl to retrieve content directly.

## Usage

```
/dependency-extractor <path-or-sbom>
```

Where `<path-or-sbom>` is either:
- A path to a project directory
- A path to an existing SBOM file (CycloneDX or SPDX JSON format)
- A GitHub repository (e.g., `owner/repo`)

> **Large SBOM files:** SBOM files can exceed 100k lines. Do NOT use the Read tool directly - use Node.js to parse JSON files. See "Parsing SBOM Files" section below.

## Scanning GitHub Repos with gh CLI

### Find All Dependency Files (Single Query)

Use `gh api` with tree endpoint to find all dependency files at once.

**Cross-platform (simple filters):**
```bash
# Get all paths, then filter for specific extensions
gh api repos/{owner}/{repo}/git/trees/HEAD?recursive=1 --jq "[.tree[].path | select(endswith(\".csproj\") or endswith(\"package.json\") or endswith(\".props\") or endswith(\"requirements.txt\") or endswith(\"go.mod\") or endswith(\"Cargo.toml\"))] | .[]"
```

**Unix/macOS only (regex filters):**
```bash
gh api repos/{owner}/{repo}/git/trees/HEAD?recursive=1 --jq '
  .tree[].path | select(
    test("(package\\.json|package-lock\\.json|yarn\\.lock|pnpm-lock\\.yaml)$") or
    test("(Directory\\.Packages\\.props|Directory\\.Build\\.props|\\.csproj|packages\\.config)$") or
    test("(requirements.*\\.txt|pyproject\\.toml|Pipfile|setup\\.py)$") or
    test("(go\\.mod|go\\.sum)$") or
    test("(Cargo\\.toml|Cargo\\.lock)$") or
    test("(Gemfile|Gemfile\\.lock)$") or
    test("(pom\\.xml|build\\.gradle(\\.kts)?)$") or
    test("(composer\\.json|composer\\.lock)$")
  )
'
```

### Fetch Dependency File Contents

**Recommended: Use raw GitHub URLs (works on all platforms):**
```
https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{path}
```

Example: Fetch and analyze with WebFetch or curl:
```bash
curl -s "https://raw.githubusercontent.com/owner/repo/main/package.json"
```

**Unix/macOS only (base64 decoding):**
```bash
# For text files (package.json, requirements.txt, etc.)
gh api repos/{owner}/{repo}/contents/{path} --jq '.content' | base64 -d

# For .csproj files - extract PackageReference items
gh api repos/{owner}/{repo}/contents/{path} --jq '.content' | base64 -d | \
  grep -oP '(?<=Include=")[^"]+'

# For package.json - extract dependencies
gh api repos/{owner}/{repo}/contents/package.json --jq '.content' | base64 -d | \
  jq -r '.dependencies // {} | keys[]'
```

**Windows:** The `base64 -d` and `grep -oP` commands above will fail. Use raw GitHub URLs instead.

### NuGet Central Package Management

**Cross-platform (use raw URL):**
```bash
curl -s "https://raw.githubusercontent.com/{owner}/{repo}/main/Directory.Packages.props"
# Then parse the XML to extract PackageReference Include="" values
```

**Unix/macOS only:**
```bash
gh api repos/{owner}/{repo}/contents/Directory.Packages.props --jq '.content' | base64 -d | \
  grep -oP '(?<=Include=")[^"]+'
```

### Batch Fetch Multiple Files

```bash
# Get all package.json files in repo
gh api repos/{owner}/{repo}/git/trees/HEAD?recursive=1 --jq '
  .tree[] | select(.path | endswith("package.json")) | .path
' | while read path; do
  echo "=== $path ==="
  gh api "repos/{owner}/{repo}/contents/$path" --jq '.content' | base64 -d | jq '.dependencies'
done
```

## Multi-Ecosystem Scanning

**IMPORTANT:** Always scan for ALL dependency files in the repository. Modern projects often use multiple ecosystems:

| Ecosystem | Dependency Files to Find |
|-----------|-------------------------|
| .NET/NuGet | `Directory.Packages.props`, `*.csproj`, `packages.config`, `paket.dependencies` |
| Node.js/npm | `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` |
| Python | `requirements.txt`, `pyproject.toml`, `Pipfile`, `setup.py` |
| Go | `go.mod`, `go.sum` |
| Rust | `Cargo.toml`, `Cargo.lock` |
| Ruby | `Gemfile`, `Gemfile.lock` |
| Java | `pom.xml`, `build.gradle`, `build.gradle.kts` |
| PHP | `composer.json`, `composer.lock` |

Search the entire repository tree for these files. A full-stack application will typically have:
- Backend dependencies (NuGet, pip, Go, etc.)
- Frontend dependencies (npm in `src/`, `client/`, `web/`, `ui/` folders)
- Build tool dependencies
- Test dependencies

## SBOM Generation

If given a project directory without an existing SBOM, suggest generating one:

### Using sbomify
```bash
sbomify scan <project-path> -o sbom.json
```

### Using syft
```bash
syft <project-path> -o cyclonedx-json > sbom.json
```

### Using cyclonedx-cli (for specific ecosystems)
```bash
# Node.js
cyclonedx-npm --output-file sbom.json

# Python
cyclonedx-py requirements -o sbom.json

# Go
cyclonedx-gomod mod -output sbom.json
```

## Parsing SBOM Files

### Handling Large SBOM Files

**SBOM files can be very large (100k+ lines).** Do NOT try to read them directly with the Read tool - it will fail with token limit errors.

**Use the helper script (recommended):**
```bash
node ~/.claude/skills/dependency-extractor/scripts/extract-sbom-repos.js <sbom-file> --filter-corporate
```

Options:
- `--filter-corporate` - Exclude repos owned by Google, Microsoft, Docker, etc.
- `--json` - Output as JSON for further processing

**Or use inline Node.js to parse large JSON files:**

```javascript
// Save as extract-sbom.js and run with: node extract-sbom.js <path-to-sbom>
const fs = require('fs');
const path = process.argv[2];
const sbom = JSON.parse(fs.readFileSync(path));

// CycloneDX format
if (sbom.components) {
  console.log('Format: CycloneDX');
  console.log('Total components:', sbom.components.length);

  // Extract GitHub repos from package names or purls
  const repos = [...new Set(
    sbom.components
      .map(c => {
        // Try to extract from name (Go modules)
        const nameMatch = c.name?.match(/github\.com\/([^\/]+\/[^\/]+)/);
        if (nameMatch) return nameMatch[1];

        // Try to extract from purl
        const purlMatch = c.purl?.match(/github\.com[%2F\/]([^\/]+)[%2F\/]([^@\/]+)/i);
        if (purlMatch) return `${purlMatch[1]}/${purlMatch[2]}`;

        // Try externalReferences
        const extRef = c.externalReferences?.find(r =>
          r.url?.includes('github.com') && (r.type === 'vcs' || r.type === 'website')
        );
        if (extRef) {
          const match = extRef.url.match(/github\.com\/([^\/]+\/[^\/]+)/);
          if (match) return match[1].replace(/\.git$/, '');
        }

        return null;
      })
      .filter(Boolean)
  )];

  console.log('GitHub repos found:', repos.length);
  repos.forEach(r => console.log(r));
}

// SPDX format
if (sbom.packages) {
  console.log('Format: SPDX');
  console.log('Total packages:', sbom.packages.length);
  // Similar extraction logic...
}
```

**Quick one-liner for component count:**
```bash
node -e "console.log(JSON.parse(require('fs').readFileSync('sbom.json')).components.length)"
```

**Extract first N components for preview:**
```bash
node -e "JSON.parse(require('fs').readFileSync('sbom.json')).components.slice(0,20).forEach(c => console.log(c.name, c.version))"
```

### Filter Corporate Repos

```javascript
const corporate = [
  'google', 'golang', 'microsoft', 'docker', 'containerd', 'kubernetes',
  'azure', 'aws', 'amazon', 'hashicorp', 'cloudflare', 'facebook', 'meta',
  'apple', 'ibm', 'oracle', 'vmware', 'redhat', 'grpc', 'prometheus',
  'opencontainers', 'moby'
];

const nonCorporate = repos.filter(r =>
  !corporate.some(c => r.toLowerCase().startsWith(c + '/'))
);
```

### CycloneDX JSON Structure
```json
{
  "components": [
    {
      "name": "lodash",
      "version": "4.17.21",
      "purl": "pkg:npm/lodash@4.17.21",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/lodash/lodash.git"
        }
      ]
    }
  ]
}
```

Extract GitHub repos from:
1. `externalReferences` where `type` is `vcs` or `website`
2. Parse `purl` for package identifier to query registries
3. For Go modules: parse the `name` field directly (e.g., `github.com/spf13/cobra`)

### SPDX JSON Structure
```json
{
  "packages": [
    {
      "name": "lodash",
      "versionInfo": "4.17.21",
      "externalRefs": [
        {
          "referenceType": "purl",
          "referenceLocator": "pkg:npm/lodash@4.17.21"
        }
      ],
      "homepage": "https://github.com/lodash/lodash"
    }
  ]
}
```

## Registry Lookups

When SBOM lacks GitHub URLs, query package registries:

### NuGet (.NET)
```bash
curl -s "https://api.nuget.org/v3/registration5-gz-semver2/{package}/index.json" | \
  gunzip | jq -r '.items[-1].items[-1].catalogEntry.projectUrl'
```

Or scrape from nuget.org:
```bash
curl -s "https://www.nuget.org/packages/{package}" | \
  grep -oP '(?<=href=")[^"]*github\.com/[^"]*(?=")'
```

### npm
```bash
curl -s "https://registry.npmjs.org/{package}" | jq -r '.repository.url'
```

Batch lookup (multiple packages):
```bash
echo "lodash express chalk" | tr ' ' '\n' | while read pkg; do
  url=$(curl -s "https://registry.npmjs.org/$pkg" | jq -r '.repository.url // empty')
  echo "$pkg: $url"
done
```

### PyPI
```bash
curl -s "https://pypi.org/pypi/{package}/json" | \
  jq -r '.info.project_urls.Source // .info.project_urls.Repository // .info.project_urls.Homepage // .info.home_page'
```

### crates.io (Rust)
```bash
curl -s "https://crates.io/api/v1/crates/{package}" | jq -r '.crate.repository'
```

### Go
```bash
# Go modules often use GitHub paths directly
# e.g., github.com/gin-gonic/gin -> gin-gonic/gin
# Just extract the path after github.com/

# For indirect deps, check pkg.go.dev:
curl -s "https://pkg.go.dev/{module}" | grep -oP 'github\.com/[^"<]+'
```

### RubyGems
```bash
curl -s "https://rubygems.org/api/v1/gems/{package}.json" | \
  jq -r '.source_code_uri // .homepage_uri'
```

### Maven (Java)
```bash
# Use search.maven.org API
curl -s "https://search.maven.org/solrsearch/select?q=a:{artifactId}+AND+g:{groupId}&rows=1&wt=json" | \
  jq -r '.response.docs[0].scm // empty'
```

## URL Normalization

Convert various Git URL formats to `owner/repo`:

| Input | Output |
|-------|--------|
| `https://github.com/lodash/lodash.git` | `lodash/lodash` |
| `git+https://github.com/lodash/lodash.git` | `lodash/lodash` |
| `git://github.com/lodash/lodash.git` | `lodash/lodash` |
| `git@github.com:lodash/lodash.git` | `lodash/lodash` |
| `github.com/lodash/lodash` | `lodash/lodash` |

Discard non-GitHub URLs (GitLab, Bitbucket, etc.) with a note in output.

## Output Format

### Primary Output (for funding-analysis)
```
lodash/lodash, expressjs/express, mochajs/mocha, chalk/chalk
```

### Detailed Output
```
## Extracted Dependencies

**Total packages:** 47
**With GitHub repos:** 42
**Non-GitHub/Unknown:** 5

### GitHub Repositories
| Package | Ecosystem | GitHub Repo |
|---------|-----------|-------------|
| lodash | npm | lodash/lodash |
| express | npm | expressjs/express |
| requests | pypi | psf/requests |

### Non-GitHub Sources
| Package | Ecosystem | Source |
|---------|-----------|--------|
| internal-lib | npm | https://gitlab.company.com/... |

### No Source Found
- some-abandoned-package (npm)
- legacy-util (pypi)

### Ready for Funding Analysis
Copy this for `/dependency-funding-analysis`:
\`\`\`
lodash/lodash, expressjs/express, psf/requests, ...
\`\`\`
```

## Filtering Options

When running, consider filtering:
- **Exclude dev dependencies** - Focus on production deps (check SBOM scope/dev flags)
- **Exclude known corporate repos** - Microsoft, Google, Meta, Amazon owned
- **Direct deps only** - Skip transitive dependencies for smaller lists
- **Minimum popularity** - Only packages with >1000 GitHub stars

## deps.dev API (Google Open Source Insights)

The fastest way to get package to GitHub repo mapping with extra metadata:

```bash
# Get package info including repo URL
curl -s "https://api.deps.dev/v3/systems/{ecosystem}/packages/{package}" | \
  jq '{name: .package.name, repo: .package.projectUrl, versions: .versions | length}'

# Ecosystems: npm, go, maven, pypi, cargo, nuget
```

Example:
```bash
curl -s "https://api.deps.dev/v3/systems/npm/packages/express" | \
  jq -r '.package.projectUrl'
# Returns: https://github.com/expressjs/express
```

Batch lookup with deps.dev:
```bash
for pkg in lodash express chalk; do
  repo=$(curl -s "https://api.deps.dev/v3/systems/npm/packages/$pkg" | jq -r '.package.projectUrl // empty')
  echo "$pkg -> $repo"
done
```

## Notes

- deps.dev is often the fastest option - has repo URLs pre-resolved
- Rate limit registry API calls (1 request/second recommended)
- Cache registry lookups to avoid redundant calls
- Some packages genuinely have no public repo (closed source, vendored)
- SBOM quality varies - some generators capture more metadata than others
- **On Windows:** Always use raw GitHub URLs (`raw.githubusercontent.com`) instead of base64 decoding. Use simple `endswith()` jq filters instead of regex `test()` patterns.
- **Large files:** Never use the Read tool on SBOM files - they often exceed token limits. Always use `node -e` or a script file to parse JSON.
- **PowerShell escaping:** Avoid `$variable` in PowerShell commands from bash - the `$` gets eaten. Write a `.js` file instead.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lottepitcher) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
