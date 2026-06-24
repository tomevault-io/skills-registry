---
name: seemenot-pii-redaction
description: This skill should be used when the user asks to "redact PII from an image", "remove personal information from a screenshot", "blur sensitive data in an image", "hide emails/IPs in a screenshot", "anonymize an image", "mask usernames in a screenshot", or mentions redacting text, credentials, or private information from images. Use when this capability is needed.
metadata:
  author: waldekmastykarz
---

# seemenot PII Redaction Skill

Redact personally identifiable information (PII) from images using local OCR. No cloud APIs—everything runs locally using Tesseract.

## Prerequisites

Tesseract OCR must be installed:
- **macOS**: `brew install tesseract`
- **Linux (Debian/Ubuntu)**: `sudo apt install tesseract-ocr`
- **Windows**: `winget install UB-Mannheim.TesseractOCR`

Install the CLI from GitHub:
```bash
uv tool install git+https://github.com/waldekmastykarz/seemenot
```

Or run without installing:
```bash
uvx --from git+https://github.com/waldekmastykarz/seemenot seemenot <image> -r <text>
```

## Core Workflow

### Basic Redaction

Redact specific text strings from an image:

```bash
seemenot <image> -r <text-to-redact> [-r <more-text>...]
```

Output is saved as `<image>-nopii.<ext>` by default.

### Common Options

| Option | Short | Description |
|--------|-------|-------------|
| `--output` | `-o` | Custom output path |
| `--redact` | `-r` | Text to redact (repeatable) |
| `--exclude` | `-e` | Text to exclude from detection (repeatable) |
| `--redact-style` | | `box` (default), `blur`, or `pixelate` |
| `--types` | | Auto-detect PII types: `email`, `ip`, `jwt` |
| `--interactive` | `-i` | Confirm each detection before redacting |
| `--dry-run` | | Preview detections without modifying |
| `--json` | | Output as JSON (with `--dry-run`) |
| `--from-manifest` | | Apply redactions from JSON file |
| `--verbose` | `-v` | Show detection details |

## Typical Tasks

### Redact Specific Text

When the user specifies exact strings to redact:

```bash
seemenot screenshot.png -r "waldek" -r "mypassword" -r "secret-hostname"
```

### Auto-Detect PII Patterns

Automatically find and redact emails, IPs, and JWTs:

```bash
# Detect all supported types
seemenot screenshot.png

# Only specific types
seemenot screenshot.png --types email,ip

# Combine with explicit redactions
seemenot screenshot.png -r waldek --types jwt
```

### Exclude False Positives

Prevent specific text from being redacted:

```bash
seemenot screenshot.png -r waldek -e "waldek-public-repo"
```

### Choose Redaction Style

```bash
# Black box (default)
seemenot screenshot.png -r secret --redact-style box

# Gaussian blur
seemenot screenshot.png -r secret --redact-style blur

# Pixelate
seemenot screenshot.png -r secret --redact-style pixelate
```

### Preview Before Redacting

Dry run shows what would be detected without modifying:

```bash
seemenot screenshot.png -r waldek --dry-run -v
```

### Interactive Review

Review each detection before redacting:

```bash
seemenot screenshot.png -r waldek -i
```

### Manifest Workflow (Fine-Grained Control)

For complex images requiring manual review:

```bash
# 1. Generate detection manifest
seemenot screenshot.png -r waldek --dry-run --json > detections.json

# 2. Edit detections.json to remove false positives

# 3. Apply from manifest
seemenot screenshot.png --from-manifest detections.json
```

## Execution Guidelines

1. **Always use absolute paths** for images to avoid path resolution issues
2. **Preview with `--dry-run`** when uncertain about what will be detected
3. **Use `-v` (verbose)** to show detection details for verification
4. **Combine `-r` and `--types`** when both explicit strings and pattern detection needed
5. **Use `--exclude`** for known false positives rather than editing manifests
6. **Prefer `--redact-style blur`** when context around redacted content should be partially visible
7. **Prefer `--redact-style pixelate`** for a less aggressive anonymization

## Supported PII Types

| Type | Examples | Notes |
|------|----------|-------|
| `email` | `john@example.com` | Standard email pattern |
| `ip` | `192.168.1.1`, `10.0.0.1:8080` | Excludes localhost `127.x.x.x` |
| `jwt` | `eyJhbGciOiJ...` | JSON Web Tokens |

For usernames, hostnames, API keys, or other text—use `-r <text>`.

## Error Handling

- **Exit 0**: Success or no PII detected
- **Exit 1**: Error during detection or redaction
- **Exit 2**: Invalid input (e.g., unknown PII type)

Check stderr for error messages. Use `-v` to debug detection issues.

## Example Scenarios

### Clean a screenshot for documentation
```bash
seemenot docs/screenshot.png -r "john.doe@company.com" -r "192.168.1.100" -o docs/screenshot-clean.png
```

### Anonymize terminal output
```bash
seemenot terminal.png --types email,ip -r "$(whoami)" --redact-style blur
```

### Batch processing with manifest
```bash
# Generate, review, apply
seemenot image.png -r sensitive --dry-run --json > manifest.json
# (manually edit manifest.json)
seemenot image.png --from-manifest manifest.json -o image-safe.png
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/waldekmastykarz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
