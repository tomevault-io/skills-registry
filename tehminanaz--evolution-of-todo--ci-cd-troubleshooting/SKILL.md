---
name: ci-cd-troubleshooting
description: Troubleshooting and stabilizing CI/CD pipelines, frontend builds, and backend linting. Use when this capability is needed.
metadata:
  author: tehminanaz
---

# CI/CD & Build Stabilization Skill

This skill provides a systematic approach to resolving common CI/CD failures, frontend build conflicts, and linting blockers in modern full-stack projects.

## 1. Frontend Build & ESM Compatibility
When a Next.js project uses `"type": "module"` in `package.json`, generic `.js` config files are treated as ES modules.

- **The Problem**: `module.exports` in `next.config.js` throws `ReferenceError: module is not defined`.
- **The Fix**:
    - Rename `next.config.js` to `next.config.mjs`.
    - Change `module.exports = nextConfig;` to `export default nextConfig;`.

## 2. ESLint 9 Flat Config Migration
ESLint 9 uses `eslint.config.js` by default. Legacy `.eslintrc.json` files or misplaced configs can cause directory resolution errors (e.g., `Failed to fill directory` in Next.js).

- **Best Practice**:
    - Use a single `eslint.config.js` in the project root.
    - Explicitly ignore `next-env.d.ts` and build artifacts.
    - If rules are too strict for production blockers, use `rules: { 'rule-name': 'off' }` sparingly.

## 3. React Hooks: setState in Effect
React hooks flagging `setState` inside `useEffect` can block builds if strict linting is enabled.

- **The Fix**: Wrap synchronous state updates in a `setTimeout(() => { ... }, 0)` or `window.requestAnimationFrame` to ensure the update happens after the current render cycle.
- **Example**:
  ```typescript
  useEffect(() => {
    const timeoutId = setTimeout(() => {
      setData(initialData);
    }, 0);
    return () => clearTimeout(timeoutId);
  }, []);
  ```

## 4. GitHub Actions Infrastructure
Common workflow failures often stem from missing setup steps or invalid versions.

- **Docker Caching**: To use `type=gha` cache, you **must** add `docker/setup-buildx-action@v3`.
- **Trivy Versioning**: Always use valid semver tags like `0.33.1` instead of `v0.20.0` if the action registry requires it.
- **Permissions**: SARIF uploads for security scans require `security-events: write` and `actions: read`.

## 5. Backend Linting (Python/uv/ruff)
When using `uv` for Python management:
- **Installation**: Use `uv sync --extra dev` in CI to ensure testing/linting tools are available.
- **Automatic Fixes**: Run `uv run ruff check --fix --unsafe-fixes .` to clear bulk issues.
- **Stabilization**: If non-critical linting errors block the build, use `--exit-zero` in the CI command to report warnings without failing the job.

## 6. Docker Consistency
Always ensure the `Dockerfile` uses the same package manager as the local environment (e.g., `npm` vs `yarn`) to avoid lockfile mismatches.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
