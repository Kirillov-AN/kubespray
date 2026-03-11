# Releases Automation

This document describes a PR-based release process where release notes are reviewed as code and a GitHub workflow publishes the release.

## Scope

- Release notes source of truth: `changelogs/vX.Y.Z.md`
- Release trigger: pushing tag `vX.Y.Z`
- Release publisher: `comnoco/create-release`

## Required Repository Setup

1. Create a `changelogs/` directory in the repository.
2. Add `OWNERS` rules so changes under `changelogs/*` require both:
   - `/lgtm`
   - `/approve`
3. Add a GitHub Actions workflow that runs on tag push (`v*`) and has:
   - `permissions: contents: write`
   - a step that reads release notes from `changelogs/vX.Y.Z.md`
   - a step that creates the GitHub Release with `comnoco/create-release`

## Release Process

1. Maintainer creates a PR with release notes file `changelogs/vX.Y.Z.md`.
2. Reviewers approve it (`/lgtm` and `/approve`), then PR is merged.
3. Maintainer creates tag `vX.Y.Z`.
4. Maintainer pushes the tag to origin.
5. GitHub Actions is triggered by the tag push and runs `comnoco/create-release`.
6. Action creates GitHub Release using `changelogs/vX.Y.Z.md` as the release body.

## Conventions

- Tag name and changelog filename must match exactly:
  - Tag: `v2.30.0`
  - File: `changelogs/v2.30.0.md`
- If changelog file does not exist for the pushed tag, the workflow must fail.

## Minimal Workflow Example

```yaml
name: Create Release From Changelog

on:
  push:
    tags:
      - "v*"

permissions:
  contents: write

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Resolve changelog path
        id: changelog
        run: |
          TAG="${GITHUB_REF_NAME}"
          FILE="changelogs/${TAG}.md"
          test -f "${FILE}" || { echo "Missing ${FILE}"; exit 1; }
          echo "file=${FILE}" >> "$GITHUB_OUTPUT"

      - name: Create release
        uses: comnoco/create-release@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref_name }}
          release_name: ${{ github.ref_name }}
          body_path: ${{ steps.changelog.outputs.file }}
          draft: false
          prerelease: false
```
