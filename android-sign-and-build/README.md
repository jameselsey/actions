# android-sign-and-build

Decode a base64-encoded keystore from a secret, build a signed release AAB with `./gradlew bundleRelease`, verify the output, then clean up the keystore file from the runner.

## Usage

```yaml
- name: Build signed release AAB
  uses: jameselsey/actions/android-sign-and-build@main
  with:
    keystore-base64: ${{ secrets.RELEASE_KEYSTORE }}
    keystore-password: ${{ secrets.KEYSTORE_PASSWORD }}
    key-alias: ${{ secrets.KEY_ALIAS }}
    key-password: ${{ secrets.KEY_PASSWORD }}
    version-name: '1.2.0'
    version-code: '42'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `keystore-base64` | **Yes** ⚠️ sensitive | — | Base64-encoded keystore file content. Store as a repository secret. |
| `keystore-password` | **Yes** ⚠️ sensitive | — | Password for the keystore. Store as a repository secret. |
| `key-alias` | **Yes** ⚠️ sensitive | — | Key alias within the keystore. Store as a repository secret. |
| `key-password` | **Yes** ⚠️ sensitive | — | Password for the key. Store as a repository secret. |
| `version-name` | **Yes** | — | Version name string, e.g. `1.2.0` |
| `version-code` | **Yes** | — | Integer version code, e.g. `42` |
| `java-version` | No | `17` | JDK version to use for the build |

## Outputs

| Output | Description |
|--------|-------------|
| `aab-path` | Path to the built signed AAB file |
| `aab-size-mb` | Size of the AAB in MB (2 decimal places) |
| `mapping-path` | Path to the ProGuard mapping directory |

## Setting up signing secrets

1. **Generate a keystore** (if you don't already have one):
   ```bash
   keytool -genkey -v -keystore release.keystore -alias my-key-alias \
     -keyalg RSA -keysize 2048 -validity 10000
   ```

2. **Base64-encode the keystore**:
   ```bash
   base64 -w 0 release.keystore
   ```
   On macOS, omit `-w 0`:
   ```bash
   base64 release.keystore
   ```

3. **Add the following secrets** to your repository (Settings → Secrets and variables → Actions):
   - `RELEASE_KEYSTORE` — the base64-encoded keystore output from step 2
   - `KEYSTORE_PASSWORD` — the keystore password
   - `KEY_ALIAS` — the key alias
   - `KEY_PASSWORD` — the key password

> **Note:** Google recommends using a dedicated upload key rather than your app signing key. If you use Google Play App Signing, upload keys are separate from the signing keys Google uses to distribute your app. See the [Play App Signing documentation](https://support.google.com/googleplay/android-developer/answer/9842756) for details.
