# android-build-tools-check

Checks current Gradle, Android Gradle Plugin, and Kotlin versions against the latest releases and outputs notification-ready data. Fetches latest versions from the Gradle services API and Maven Central.

## Usage

```yaml
- name: Check build tools
  id: tools
  uses: jameselsey/actions/android-build-tools-check@main

- name: Notify if outdated
  if: steps.tools.outputs.needs_notification == 'true'
  uses: jameselsey/actions/discord-notify@main
  with:
    webhook-url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    title: '🔧 Build Tool Version Check'
    color: ${{ steps.tools.outputs.color }}
    fields: ${{ steps.tools.outputs.fields }}
    priority: ${{ steps.tools.outputs.priority }}
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `root-build-gradle-path` | | `build.gradle.kts` | Path to the root build file (AGP and Kotlin versions are read from here) |
| `gradle-wrapper-path` | | `gradle/wrapper/gradle-wrapper.properties` | Path to the Gradle wrapper properties file |
| `age-warning-months` | | `6` | Flag a version as old if the latest release is this many months newer, even if not strictly outdated |

## Outputs

| Output | Description |
|--------|-------------|
| `needs_notification` | `true` if any tool is outdated or old |
| `color` | Discord embed color |
| `priority` | `medium` (updates available) or `low` (all current) |
| `fields` | JSON array of Discord embed fields |
| `gradle_current` | Current Gradle version |
| `gradle_latest` | Latest Gradle version |
| `gradle_status` | `current` or `outdated` |
| `agp_current` | Current AGP version |
| `agp_latest` | Latest AGP version |
| `agp_status` | `current` or `outdated` |
| `kotlin_current` | Current Kotlin version |
| `kotlin_latest` | Latest Kotlin version |
| `kotlin_status` | `current` or `outdated` |
| `outdated_count` | Number of outdated tools (0–3) |
| `health_score` | Score 0–100 (100 = all current, −20 per outdated tool) |
