# android-dependency-check

Scans `build.gradle.kts` files for outdated Maven dependencies by querying Maven Central, classifies updates by severity (major/minor/patch), and outputs notification-ready data.

## Usage

```yaml
- name: Check dependencies
  id: deps
  uses: jameselsey/actions/android-dependency-check@main

- name: Notify if updates available
  if: steps.deps.outputs.has_updates == 'true'
  uses: jameselsey/actions/discord-notify@main
  with:
    webhook-url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    title: '📦 Dependency Updates Available'
    description: ${{ steps.deps.outputs.description }}
    color: ${{ steps.deps.outputs.color }}
    fields: ${{ steps.deps.outputs.fields }}
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `gradle-files` | | `build.gradle.kts app/build.gradle.kts` | Space-separated list of build files to scan |

## Outputs

| Output | Description |
|--------|-------------|
| `has_updates` | `true` if any dependency updates are available |
| `color` | Discord embed color (red-orange for major, yellow for minor, blue for patch) |
| `description` | Short summary string for the notification description |
| `fields` | JSON array of Discord embed fields grouped by severity |
| `total` | Total number of dependencies scanned |
| `outdated` | Number of outdated dependencies |
| `major` | Number of major version updates |
| `minor` | Number of minor version updates |
| `patch` | Number of patch version updates |
| `health_score` | Score 0–100 (penalised: major=−10, minor=−5, patch=−2) |

## Notes

- Only scans `implementation`, `api`, `compileOnly`, and `runtimeOnly` dependencies declared with string literals (e.g. `implementation("group:artifact:version")`).
- Dependencies using version catalogs or variable references are not detected.
