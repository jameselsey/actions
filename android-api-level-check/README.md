# android-api-level-check

Checks an Android app's `targetSdk` and `compileSdk` against the latest stable Android SDK level, fetched live from the Android SDK repository. No config file or manual version tracking required — the latest SDK is always current automatically.

## Usage

```yaml
- name: Check API level compliance
  id: compliance
  uses: jameselsey/actions/android-api-level-check@main

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
| `warning-levels-behind` | | `1` | Levels behind latest before a warning fires. Default 1 means warn if not on the latest stable SDK. |

## Outputs

| Output | Description |
|--------|-------------|
| `status` | `compliant`, `warning`, or `critical` |
| `message` | Human-readable status line |
| `color` | Discord embed color for the status |
| `fields` | JSON array of Discord embed fields |
| `target_sdk` | Current `targetSdkVersion` |
| `compile_sdk` | Current `compileSdkVersion` |
| `latest_sdk` | Latest stable SDK level fetched from the Android SDK repository |

## Status logic

| Condition | Status |
|-----------|--------|
| `targetSdk ≠ compileSdk` | `critical` — misconfiguration |
| `compileSdk` is more than `warning-levels-behind` behind latest | `critical` |
| `compileSdk` is exactly `warning-levels-behind` behind latest | `warning` |
| `compileSdk == latest` | `compliant` |

## Notes

- The latest stable SDK is fetched from `https://dl.google.com/android/repository/repository2-3.xml` on every run — no manual config updates needed when Google releases a new Android version.
- If the fetch fails (network issue), the check is skipped gracefully and reports compliant.
