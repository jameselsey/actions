# static-site-build-test

Install dependencies, run tests, and build a static site. Outputs the absolute path to the build directory for use by downstream steps or actions.

## Usage

```yaml
- name: Build and test
  uses: jameselsey/actions/static-site-build-test@main
  with:
    node-version: '20'
    working-directory: 'src'
    build-command: 'build'
    test-command: 'test'
    build-output-dir: 'dist'
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `node-version` | No | `20` | Node.js version to use |
| `working-directory` | No | `.` | Directory containing the site source (where `package.json` lives) |
| `build-command` | No | `build` | npm script to build the site |
| `test-command` | No | `test` | npm script to run tests. Leave empty to skip tests. |
| `build-output-dir` | No | `dist` | Directory produced by the build command, relative to `working-directory` |

## Outputs

| Output | Description |
|---|---|
| `build-path` | Absolute path to the built output directory |

## Prerequisites

- The repository must contain a `package.json` with the specified build (and optionally test) scripts.
- A `package-lock.json` must be present for `npm ci` to work.
- The runner must have internet access to download npm packages.
