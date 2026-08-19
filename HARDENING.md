<!-- markdownlint-disable -->

# Hardening Report: github--ai-moderator/v1.1.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **github--ai-moderator/v1.1.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are directly interpolated inside run: shell command strings. In ci.yml, `${{ steps.test-action.outputs.response }}` and `${{ steps.test-action-prompt-file.outputs.response-file }}` are embedded directly in shell commands (`echo` and `cat`). The `steps.*.outputs.*` context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling command injection if the action output contains shell metacharacters.

Locations:

- `.github/workflows/ci.yml:62`
- `.github/workflows/ci.yml:91`
- `.github/workflows/ci.yml:92`

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Unpinned references found:

- check-dist.yml: `actions/checkout@v5` (line 29), `actions/setup-node@v4` (line 34), `actions/upload-artifact@v4` (line 64)
- ci.yml: `actions/checkout@v5` (lines 22, 49, 68), `actions/setup-node@v4` (line 27)
- licensed.yml: `actions/checkout@v5` (line 28), `actions/setup-node@v4` (line 33), `ruby/setup-ruby@v1` (line 40), `licensee/setup-licensed@v1.3.2` (line 44)
- linter.yml: `actions/checkout@v5` (line 22), `actions/setup-node@v4` (line 28)
- release-new-action-version.yml: `actions/publish-action@v0.2.2` (line 20)

Locations:

- `.github/workflows/check-dist.yml:29`
- `.github/workflows/check-dist.yml:34`
- `.github/workflows/check-dist.yml:64`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:49`
- `.github/workflows/ci.yml:68`
- `.github/workflows/licensed.yml:28`
- `.github/workflows/licensed.yml:33`
- `.github/workflows/licensed.yml:40`
- `.github/workflows/licensed.yml:44`
- `.github/workflows/linter.yml:22`
- `.github/workflows/linter.yml:28`
- `.github/workflows/release-new-action-version.yml:20`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in ci.yml by moving ${{ steps.test-action.outputs.response }} and ${{ steps.test-action-prompt-file.outputs.response-file }} expressions from run: shell strings into env: blocks (RESPONSE and RESPONSE_FILE variables). Fixed all unpinned-uses across 5 workflow files: pinned actions/checkout@v5, actions/setup-node@v4, actions/upload-artifact@v4, ruby/setup-ruby@v1, licensee/setup-licensed@v1.3.2, and actions/publish-action@v0.2.2 to their full 40-character commit SHAs.

