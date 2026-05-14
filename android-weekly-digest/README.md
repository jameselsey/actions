# android-weekly-digest

Orchestrates all Android health checks — dependencies, API level compliance, build tools, and recent build history — and outputs a combined Discord digest with an overall health score.

Internally calls `android-dependency-check`, `android-api-level-check`, and `android-build-tools-check`, then combines their results into a single notification payload.

## Usage

```yaml
- name: Checkout repository
  uses: actions/checkout@v4

- name: Generate weekly digest
  id: digest
  uses: jameselsey/actions/android-weekly-digest@main
  with:
    repository: ${{ github.repository }}
    github-token: ${{ secrets.GITHUB_TOKEN }}

- name: Send to Discord
  uses: jameselsey/actions/discord-notify@main
  with:
    webhook-url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    title: ${{ steps.digest.outputs.title }}
    description: ${{ steps.digest.outputs.description }}
    color: ${{ steps.digest.outputs.color }}
    fields: ${{ steps.digest.outputs.fields }}
    footer: 'Weekly Digest | Next report: Sunday 6 PM AEDT'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `repository` | ✅ | — | Repository in `owner/name` format, used to fetch build history |
| `github-token` | ✅ | — | GitHub token for the Actions API (use `secrets.GITHUB_TOKEN`) |
| `root-build-gradle-path` | | `build.gradle.kts` | Path to the root build file |
| `app-build-gradle-path` | | `app/build.gradle.kts` | Path to the app-level build file |
| `gradle-wrapper-path` | | `gradle/wrapper/gradle-wrapper.properties` | Path to Gradle wrapper properties |
| `monitoring-config-path` | | `.github/workflows/config/monitoring-config.json` | Path to the monitoring config |
| `build-history-days` | | `7` | Number of days of build history to include |

## Outputs

| Output | Description |
|--------|-------------|
| `title` | Digest notification title |
| `description` | Overall score and report period |
| `color` | Discord embed color based on overall health score |
| `fields` | JSON array of Discord embed fields for the full digest |
| `overall_score` | Overall health score 0–100 |

## Monitoring config format

The action reads health score weights and API requirements from a config file in the calling repo:

```json
{
  "api_requirements": {
    "next_target_sdk": 35,
    "next_deadline": "2025-08-31",
    "documentation_url": "https://developer.android.com/google/play/requirements/target-sdk"
  },
  "thresholds": {
    "api_level_warning_days": 90
  },
  "weekly_digest": {
    "health_score_weights": {
      "dependencies": 25,
      "api_compliance": 25,
      "build_health": 25,
      "build_tools": 25
    }
  }
}
```

Weights must sum to 100. If any weight key is missing, it defaults to 25.
