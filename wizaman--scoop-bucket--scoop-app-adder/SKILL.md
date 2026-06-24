---
name: scoop-app-adder
description: Adds a new application to this Scoop bucket from a GitHub repository. Use when asked to add a tool or repository to the bucket, which involves creating a manifest and updating README.md. Use when this capability is needed.
metadata:
  author: wizaman
---

# Scoop App Adder

This skill automates adding a GitHub-hosted application to this Scoop bucket.

## Workflow

1. **Verify `gh` CLI**: Check if `gh` is installed and authenticated. If not, inform the user and stop.
2. **Gather Info**:
    - Use `gh release view --repo <owner>/<repo> --json tagName,assets` to get metadata.
    - For descriptive content (README, LICENSE), prefer `web-fetch` for raw files from GitHub (e.g., `https://raw.githubusercontent.com/<owner>/<repo>/<branch>/LICENSE`) to avoid repetitive `gh api` permission prompts.
3. **Determine Manifest Values**:
    - **Versioning**: Compare the tag name and the version string in the binary. If they are inconsistent (e.g., tag `1.23-4` vs binary `1.2.3`), decide whether to prioritize the tag or binary version. Note that significant mismatches may make `autoupdate` impossible or unreliable; in such cases, consider omitting `autoupdate` or fixing the version.
    - **App Name**: Determine a suitable name. If the name is common or likely to conflict, propose a name (e.g., adding a suffix like `-rust`) and **ask the user for confirmation**.
    - **Architecture**: Identify Windows binaries for `64bit`, `32bit`, and `arm64`.
    - **Hashes**: Extract SHA256 hashes from the output or download/calculate them.
4. **Create Manifest**:
    - Use `references/manifest-template.json` as a base.
    - **Important**: When writing regular expressions in JSON (e.g., `checkver.regex`), ensure backslashes are double-escaped (e.g., `\\d`, `\\.`).
    - Save to `bucket/<app-name>.json`.
5. **Update README.md**:
    - Add the app to the `## Applications` list in alphabetical order.
    - Format: `- **[app-name](homepage)**: Description.`
6. **Apply Formatting**:
    - Ensure all touched files use **UTF-8 without BOM** and **CRLF**.
    - Ensure one final newline at the end of each file.
7. **Verify**:
    - Run `pwsh -ExecutionPolicy Bypass -File .\bin\test.ps1`.

## Resource References

- See [references/conventions.md](references/conventions.md) for project standards.
- Use [references/manifest-template.json.template](references/manifest-template.json.template) for the manifest structure.

---
> Source: [wizaman/scoop-bucket](https://github.com/wizaman/scoop-bucket) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
