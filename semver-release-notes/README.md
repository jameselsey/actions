# semver-release-notes

Calculates the next semantic version from the latest git tag, generates release notes from commits since that tag, and outputs an Android-compatible version code. Works for any repo that uses `vX.Y.Z` git tags.

## Requirements

The calling job must check out with full git history:
```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
```

## Usage

```yaml
- name: Calculate version and release notes
  id: version
  uses: jameselsey/actions/semver-release-notes@main
  with:
    bump: 'patch'          # patch (default), minor, or major
    release-notes: ''      # leave blank to auto-generate from commits

- name: Use outputs
  run: |
    echo "Version: ${{ steps.version.outputs.version-name }}"
    echo "Code:    ${{ steps.version.outputs.version-code }}"
    echo "Notes:   ${{ steps.version.outputs.release-notes }}"
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `bump` | | `patch` | Version component to increment: `patch`, `minor`, or `major` |
| `release-notes` | | `''` | Manual release notes. If provided, skips auto-generation. |
| `exclude-patterns` | | dependabot/chore filters | Newline-separated grep patterns to exclude from auto-generated notes |

## Outputs

| Output | Description |
|--------|-------------|
| `version-name` | Next version string, e.g. `1.2.3` |
| `version-code` | Android version code: `(major × 10000) + (minor × 100) + patch` |
| `previous-tag` | The git tag this release is based on, e.g. `v1.2.2` |
| `release-notes` | Release notes — manual input or auto-generated bullet points |

## Auto-generated notes

When `release-notes` is blank, the action runs `git log <previous-tag>..HEAD` and formats each commit subject as a bullet point. Merge commits are excluded. By default, dependabot and `chore(deps)` commits are also filtered out to keep notes user-readable.

If all commits since the last tag are filtered out (e.g. a release containing only dependency bumps), the notes fall back to `• Dependency updates`.

## Version code formula

```
versionCode = (major × 10000) + (minor × 100) + patch
```

Examples: `1.0.0` → `10000`, `1.2.3` → `10203`, `2.5.10` → `20510`

This produces monotonically increasing codes required by Google Play.
