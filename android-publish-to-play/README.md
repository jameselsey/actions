# android-publish-to-play

Publish a signed AAB to a Google Play track using a service account. Wraps [`r0adkll/upload-google-play`](https://github.com/r0adkll/upload-google-play) — no Gradle plugin required, works with any pre-built AAB.

## Usage

```yaml
- name: Publish to Google Play
  uses: jameselsey/actions/android-publish-to-play@main
  with:
    service-account-json: ${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}
    package-name: 'com.example.myapp'
    aab-path: 'app/build/outputs/bundle/release/app-release.aab'
    track: 'internal'
    whats-new: 'Bug fixes and improvements.'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `service-account-json` | **Yes** ⚠️ sensitive | — | Google Play service account JSON (plain text). Store as a repository secret. |
| `package-name` | **Yes** | — | Android app package name, e.g. `com.example.myapp` |
| `aab-path` | **Yes** | — | Path to the signed AAB file to publish |
| `track` | | `internal` | Google Play track: `internal`, `alpha`, `beta`, or `production` |
| `release-status` | | `completed` | Release status: `completed`, `draft`, `halted`, or `inProgress` |
| `whats-new` | | `''` | Release notes as plain text. Leave empty to publish without release notes. |

## Outputs

| Output | Description |
|--------|-------------|
| `track` | The Google Play track that was published to |

## Setting up Google Play API access

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
| `internal` | Up to 100 named testers | No | Default for CI — immediate, no review |
| `alpha` | Opt-in testers | No | Broader pre-release testing |
| `beta` | Open opt-in | No | Wider pre-release |
| `production` | All users | Yes | Promote manually from Play Console |
