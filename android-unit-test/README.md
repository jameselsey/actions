# android-unit-test

Run Android unit tests with `./gradlew test` and upload the HTML report as a GitHub Actions artifact.

## Usage

```yaml
- name: Run unit tests
  uses: jameselsey/actions/android-unit-test@main
  with:
    java-version: '17'
    artifact-retention-days: '30'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `java-version` | No | `17` | JDK version to use for the build |
| `artifact-retention-days` | No | `30` | Number of days to retain the uploaded test report artifact |

## Outputs

| Output | Description |
|--------|-------------|
| `result` | Test outcome: `passed` or `failed` |
| `report-artifact-name` | Name of the uploaded test report artifact |

## Notes

- The test report is always uploaded (even on failure) so you can inspect results regardless of outcome.
- If no test report files are found under `app/build/reports/tests/`, the upload step will warn but not fail — this is intentional so the action works on projects that don't have any unit tests yet.
- The artifact name includes a timestamp (e.g. `unit-test-report-20240115-143022`) to avoid collisions across runs.
- The action exits with code 1 if tests fail, after the report has been uploaded.
