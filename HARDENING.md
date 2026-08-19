<!-- markdownlint-disable -->

# Hardening Report: github--ai-moderator/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github--ai-moderator/v1.1.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions from the `steps.*` context are directly interpolated inside `run:` shell command strings in ci.yml. The values flow through YAML template substitution before the shell processes them, enabling script injection if the action output contains shell metacharacters. Offending lines:
- Line 62: `run: echo "${{ steps.test-action.outputs.response }}"`
- Line 88: `echo "Response saved to: ${{ steps.test-action-prompt-file.outputs.response-file }}"`
- Line 89: `cat "${{ steps.test-action-prompt-file.outputs.response-file }}"`
Fix: assign the output to an env var and reference it as `"$ENV_VAR"` in the shell.

Locations:

- `.github/workflows/ci.yml:62`
- `.github/workflows/ci.yml:88`
- `.github/workflows/ci.yml:89`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions by mutable tags or version strings instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

check-dist.yml: `actions/checkout@v4`, `actions/setup-node@v4`, `actions/upload-artifact@v4`
ci.yml: `actions/checkout@v4` (×3), `actions/setup-node@v4`
codeql-analysis.yml: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/autobuild@v3`, `github/codeql-action/analyze@v3`
licensed.yml: `actions/checkout@v4`, `actions/setup-node@v4`, `ruby/setup-ruby@v1`, `licensee/setup-licensed@v1.3.2`
linter.yml: `actions/checkout@v4`, `actions/setup-node@v4`
release-new-action-version.yml: `actions/publish-action@v0.2.2`

Note: `super-linter/super-linter/slim@5119dcd8011e92182ce8219d9e9efc82f16fddb6` in linter.yml is correctly pinned.

Locations:

- `.github/workflows/check-dist.yml:27`
- `.github/workflows/check-dist.yml:33`
- `.github/workflows/check-dist.yml:57`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:48`
- `.github/workflows/ci.yml:68`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:32`
- `.github/workflows/codeql-analysis.yml:37`
- `.github/workflows/codeql-analysis.yml:41`
- `.github/workflows/licensed.yml:27`
- `.github/workflows/licensed.yml:33`
- `.github/workflows/licensed.yml:43`
- `.github/workflows/licensed.yml:46`
- `.github/workflows/linter.yml:24`
- `.github/workflows/linter.yml:30`
- `.github/workflows/release-new-action-version.yml:17`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in ci.yml by moving steps.*.outputs.* expressions into env: blocks and referencing them as plain shell variables. Fixed unpinned-uses across all 6 workflow files (check-dist.yml, ci.yml, codeql-analysis.yml, licensed.yml, linter.yml, release-new-action-version.yml) by replacing mutable tags with full 40-character commit SHAs resolved via lookup_action_sha. The already-pinned super-linter reference in linter.yml was left unchanged.

