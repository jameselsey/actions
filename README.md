# actions

Shared GitHub Actions for personal projects.

## Actions

### [`discord-notify`](./discord-notify)

Send a formatted Discord embed notification with retry logic. Generic — works for any event (builds, releases, monitoring alerts, etc.).

### [`android-api-level-check`](./android-api-level-check)

Check an Android app's `targetSdkVersion` against Google Play compliance requirements. Outputs status, color, and pre-built Discord fields.

### [`android-build-tools-check`](./android-build-tools-check)

Check current Gradle, AGP, and Kotlin versions against the latest releases. Outputs status, color, and pre-built Discord fields.

### [`android-dependency-check`](./android-dependency-check)

Scan `build.gradle.kts` files for outdated Maven dependencies, classified by severity (major/minor/patch). Outputs counts, color, and pre-built Discord fields.

### [`android-weekly-digest`](./android-weekly-digest)

Orchestrates all Android health checks and produces a combined weekly digest with an overall health score. Designed to be paired with `discord-notify`.

### [`android-unit-test`](./android-unit-test)

Run Android unit tests and upload the report as an artifact.

### [`android-sign-and-build`](./android-sign-and-build)

Decode a keystore, build a signed release AAB, and verify it.

### [`android-publish-to-play`](./android-publish-to-play)

Publish a signed AAB to a Google Play track using a service account.

### [`android-instrumented-test`](./android-instrumented-test)

Run Android instrumented tests on an emulator (requires Linux runner with KVM). Uploads the HTML report as an artifact.

## Usage

Reference actions from this repo using:

```yaml
uses: jameselsey/actions/discord-notify@main
uses: jameselsey/actions/android-api-level-check@main
uses: jameselsey/actions/android-build-tools-check@main
uses: jameselsey/actions/android-dependency-check@main
uses: jameselsey/actions/android-weekly-digest@main
uses: jameselsey/actions/android-unit-test@main
uses: jameselsey/actions/android-sign-and-build@main
uses: jameselsey/actions/android-publish-to-play@main
uses: jameselsey/actions/android-instrumented-test@main
```

Each calling repository controls its own schedule and configuration (e.g. `monitoring-config.json`).
