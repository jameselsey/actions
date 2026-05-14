# android-publish-to-play

Publish a signed AAB and store listing to Google Play using the [Gradle Play Publisher (GPP)](https://github.com/Triple-T/gradle-play-publisher) plugin. Runs `./gradlew publishReleaseBundle`, which uploads the AAB **and** syncs your store listing content (descriptions, screenshots, release notes) from `app/src/main/play/` in a single step.

## Usage

```yaml
- name: Publish to Google Play
  uses: jameselsey/actions/android-publish-to-play@main
  with:
    service-account-json: ${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}
    track: alpha
    release-notes: 'Bug fixes and performance improvements.'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `service-account-json` | **Yes** ⚠️ sensitive | — | Google Play service account JSON (plain text). Store as a repository secret. |
| `track` | No | `alpha` | Informational — the actual track is configured in the `play {}` block in `app/build.gradle.kts`. |
| `release-notes` | No | `''` | Release notes written to `app/src/main/play/release-notes/en-US/default.txt` before publishing. If empty, the existing file content is used. |
| `java-version` | No | `17` | JDK version to use |

## Outputs

| Output | Description |
|--------|-------------|
| `track` | The track value passed as input (informational) |

## Prerequisites

### 1. Add GPP to your project

In your root `build.gradle.kts`:
```kotlin
plugins {
    id("com.github.triplet.play") version "3.12.2" apply false
}
```

In `app/build.gradle.kts`:
```kotlin
plugins {
    id("com.github.triplet.play")
}

play {
    serviceAccountCredentials.set(
        System.getenv("PLAY_SERVICE_ACCOUNT_JSON")
            ?.let { json ->
                val tmp = File.createTempFile("play-credentials", ".json")
                tmp.writeText(json)
                tmp.deleteOnExit()
                tmp
            }
            ?: file("play-credentials.json")
    )
    defaultToAppBundles.set(true)
    track.set("alpha")
    releaseStatus.set(com.github.triplet.gradle.androidpublisher.ReleaseStatus.COMPLETED)
}
```

### 2. Create the store listing folder structure

GPP reads store listing content from `app/src/main/play/`:

```
app/src/main/play/
├── listings/
│   └── en-US/
│       ├── title.txt               # max 50 chars
│       ├── short-description.txt   # max 80 chars
│       ├── full-description.txt    # max 4000 chars
│       ├── video-url.txt           # optional YouTube URL
│       └── graphics/
│           ├── icon/               # 512×512 PNG
│           ├── feature-graphic/    # 1024×500 PNG
│           └── phone-screenshots/  # 2–8 PNGs
└── release-notes/
    └── en-US/
        └── default.txt             # shown for the current release
```

### 3. Set up Google Play API access

1. **Create a service account** in [Google Cloud Console](https://console.cloud.google.com/):
   - IAM & Admin → Service Accounts → Create
   - Create a JSON key and download it

2. **Grant access in Play Console**:
   - Setup → API access → link your Google Cloud project
   - Find the service account → grant **Release Manager** role

3. **Store the secret**:
   - Copy the full JSON key file contents
   - Add as `PLAY_SERVICE_ACCOUNT_JSON` in repository secrets

## Track strategy

| Track | Audience | Review required | Recommended use |
|-------|----------|-----------------|-----------------|
| `internal` | Up to 100 named testers | No | Smoke testing immediately after build |
| `alpha` | Opt-in testers | No | Default for CI — broader testing before production |
| `beta` | Opt-in testers (open) | No | Wider pre-release testing |
| `production` | All users | Yes | Promote manually from Play Console |

The recommended workflow: CI publishes to `alpha` automatically → you test on a device → promote to `production` manually in Play Console. This keeps production releases under your control.
