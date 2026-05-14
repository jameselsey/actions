# android-api-level-check

Checks an Android app's `targetSdkVersion` against Google Play compliance requirements and outputs notification-ready data. Designed to be paired with `discord-notify` or any other notification action.

## Usage

```yaml
- name: Check API level compliance
  id: compliance
  uses: jameselsey/actions/android-api-level-check@main
  # uses defaults: app/build.gradle.kts + .github/workflows/config/monitoring-config.json

- name: Notify if non-compliant
  if: steps.compliance.outputs.status != 'compliant'
  uses: jameselsey/actions/discord-notify@main
  with:
    webhook-url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    title: ${{ steps.compliance.outputs.message }}
    color: ${{ steps.compliance.outputs.color }}
    fields: ${{ steps.compliance.outputs.fields }}
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `build-gradle-path` | | `app/build.gradle.kts` | Path to the app-level build file |
| `monitoring-config-path` | | `.github/workflows/config/monitoring-config.json` | Path to the monitoring config |

## Outputs

| Output | Description |
|--------|-------------|
| `status` | `compliant`, `warning`, or `critical` |
| `message` | Human-readable status line |
| `color` | Discord embed color for the status |
| `fields` | JSON array of Discord embed fields |
| `target_sdk` | Current `targetSdkVersion` |
| `compile_sdk` | Current `compileSdkVersion` |
| `required_sdk` | Required `targetSdkVersion` from config |
| `days_remaining` | Days until the compliance deadline |

## Monitoring config format

The action reads from a JSON config file with this structure:

```json
{
  "api_requirements": {
    "next_target_sdk": 35,
    "next_deadline": "2025-08-31",
    "documentation_url": "https://developer.android.com/google/play/requirements/target-sdk"
  },
  "thresholds": {
    "api_level_warning_days": 90
  }
}
```

This config lives in the calling repository, so each app controls its own deadline and warning threshold independently.
