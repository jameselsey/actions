# android-instrumented-test

Run Android instrumented tests on an emulator using [`reactivecircus/android-emulator-runner`](https://github.com/ReactiveCircus/android-emulator-runner). Uploads the HTML test report as an artifact regardless of outcome.

## Usage

```yaml
- name: Checkout
  uses: actions/checkout@v4

- name: Run instrumented tests
  uses: jameselsey/actions/android-instrumented-test@main
  with:
    api-level: '33'
```

> **Note:** This action requires a Linux runner with KVM support. Use `runs-on: ubuntu-latest` — GitHub-hosted Ubuntu runners support KVM.

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `api-level` | | `33` | Android API level for the emulator |
| `target` | | `default` | Emulator target: `default` or `google_apis` |
| `arch` | | `x86_64` | Emulator CPU architecture |
| `java-version` | | `17` | JDK version to use |
| `artifact-retention-days` | | `30` | Days to retain the test report artifact |

## Outputs

| Output | Description |
|--------|-------------|
| `result` | `passed` or `failed` |
| `report-artifact-name` | Name of the uploaded artifact |

## Notes

- The action enables KVM hardware acceleration automatically — this is required for acceptable emulator performance on GitHub-hosted runners.
- Animations are disabled on the emulator to prevent flaky timing-dependent tests.
- The test report is always uploaded even on failure so you can inspect results.
- Emulator startup adds ~3–5 minutes to the job. Consider running instrumented tests only on the release workflow rather than every PR.
