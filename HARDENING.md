<!-- markdownlint-disable -->

# Hardening Report: github--ai-moderator/v1.1.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github--ai-moderator/v1.1.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation inside `run:` blocks. In ci.yml, three shell commands embed `${{ steps.*.outputs.* }}` expressions directly, which are substituted by the template engine before the shell sees them, enabling script injection if the action output contains shell metacharacters.

Offending lines:
- Line 57: `run: echo "${{ steps.test-action.outputs.response }}"`
- Line 91: `echo "Response saved to: ${{ steps.test-action-prompt-file.outputs.response-file }}"`
- Line 92: `cat "${{ steps.test-action-prompt-file.outputs.response-file }}"`

Fix: move the value into an `env:` variable and reference it as a quoted shell variable, e.g. `echo "$RESPONSE_FILE"`.

Locations:

- `.github/workflows/ci.yml:57`
- `.github/workflows/ci.yml:91`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions by mutable tags or version strings instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

Failing references:
- check-dist.yml: `actions/checkout@v5` (line 30), `actions/setup-node@v4` (line 34), `actions/upload-artifact@v4` (line 64)
- ci.yml: `actions/checkout@v5` (lines 23, 52, 68), `actions/setup-node@v4` (line 27)
- licensed.yml: `actions/checkout@v5` (line 28), `actions/setup-node@v4` (line 32), `ruby/setup-ruby@v1` (line 40), `licensee/setup-licensed@v1.3.2` (line 44)
- linter.yml: `actions/checkout@v5` (line 22), `actions/setup-node@v4` (line 27)
- release-new-action-version.yml: `actions/publish-action@v0.2.2` (line 20)

Note: `super-linter/super-linter/slim@5119dcd8011e92182ce8219d9e9efc82f16fddb6` in linter.yml is correctly pinned.

Locations:

- `.github/workflows/check-dist.yml:30`
- `.github/workflows/check-dist.yml:34`
- `.github/workflows/check-dist.yml:64`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:52`
- `.github/workflows/ci.yml:68`
- `.github/workflows/licensed.yml:28`
- `.github/workflows/licensed.yml:32`
- `.github/workflows/licensed.yml:40`
- `.github/workflows/licensed.yml:44`
- `.github/workflows/linter.yml:22`
- `.github/workflows/linter.yml:27`
- `.github/workflows/release-new-action-version.yml:20`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script injection in ci.yml by moving all ${{ steps.*.outputs.* }} expressions into env: blocks (RESPONSE and RESPONSE_FILE variables). Pinned all unpinned action references across check-dist.yml, ci.yml, licensed.yml, linter.yml, and release-new-action-version.yml to their full 40-character commit SHAs, preserving original tags as inline comments.

