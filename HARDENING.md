<!-- markdownlint-disable -->

# Hardening Report: reproducible-containers--buildkit-cache-dance/v3.3.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reproducible-containers--buildkit-cache-dance/v3.3.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files reference external actions using mutable version tags instead of pinned full-length SHA commit hashes. This exposes the workflow to supply-chain attacks if a tag is moved or a repository is compromised.

In .github/workflows/actions-test.yml:
- uses: actions/checkout@v4
- uses: docker/setup-buildx-action@v3
- uses: docker/metadata-action@v5
- uses: actions/cache@v4
- uses: docker/build-push-action@v5

In .github/workflows/unit-tests.yml:
- uses: actions/checkout@v4
- uses: actions/setup-node@v4
- uses: pnpm/action-setup@v4
- uses: actions/upload-artifact@v4

All of these should be pinned to a full 40-character hex commit SHA (e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4).

Locations:

- `.github/workflows/actions-test.yml:11`
- `.github/workflows/actions-test.yml:12`
- `.github/workflows/actions-test.yml:15`
- `.github/workflows/actions-test.yml:21`
- `.github/workflows/actions-test.yml:40`
- `.github/workflows/unit-tests.yml:13`
- `.github/workflows/unit-tests.yml:15`
- `.github/workflows/unit-tests.yml:18`
- `.github/workflows/unit-tests.yml:32`

### missing-permissions (severity: medium)

The workflow file .github/workflows/actions-test.yml has no top-level `permissions:` key and the single job `actions_test` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). A minimal explicit permissions block (e.g. `permissions: contents: read`) should be added.

Locations:

- `.github/workflows/actions-test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed both workflow files:

1. `.github/workflows/actions-test.yml`:
   - Added top-level `permissions: contents: read` block to address missing-permissions finding
   - Pinned all 5 action references to full SHA hashes:
     - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
     - docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f
     - docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051
     - actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830
     - docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25

2. `.github/workflows/unit-tests.yml`:
   - Pinned all 4 action references to full SHA hashes:
     - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
     - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
     - pnpm/action-setup@v4 → @b906affcce14559ad1aafd4ab0e942779e9f58b1
     - actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02
   - The existing `permissions: contents: read` block was preserved

All original version tags are preserved as inline comments (e.g., `# v4`) for readability.

