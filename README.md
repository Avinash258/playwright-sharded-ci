# playwright-sharded-ci

Reusable **GitHub Actions** workflow: run Playwright in **N parallel shards**, then merge blob/HTML reports into one artefact.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

> Built from patterns used in [PlaywrightTSFrameWork](https://github.com/Avinash258/PlaywrightTSFrameWork) · [Portfolio](https://avinash258.github.io/portfolio/)

## Why

Sharding cuts wall-clock time for large suites. Merging reports keeps one HTML report for the PR. This repo packages that as a **callable workflow** you can drop into any Playwright project.

## Quick use (caller workflow)

In your app repo, create `.github/workflows/playwright-sharded.yml`:

```yaml
name: Playwright sharded

on:
  push:
    branches: [main]
  pull_request:

jobs:
  playwright:
    uses: Avinash258/playwright-sharded-ci/.github/workflows/reusable-playwright-sharded.yml@v0.1.0
    with:
      node-version: "20"
      shard-total: 4
      browsers: chromium
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `node-version` | `20` | Node.js version |
| `shard-total` | `4` | Number of parallel shards |
| `browsers` | `chromium` | Passed to `playwright install --with-deps` |
| `test-args` | `''` | Extra args for `playwright test` (e.g. `--grep @smoke`) |
| `working-directory` | `.` | Subfolder if the Playwright project is not at repo root |

## What the reusable workflow does

1. Matrix job: `npx playwright test --shard=i/N` with `CI=true`
2. Uploads each shard’s `blob-report`
3. Merge job: `npx playwright merge-reports` → single HTML report artefact

## Local equivalent

```bash
npx playwright test --shard=1/4
npx playwright test --shard=2/4
# ...
npx playwright merge-reports ./all-blob-reports
```

## Example project layout expected

```text
package.json          # must expose playwright via npm ci
playwright.config.*   # reporter should include blob on CI
tests/
```

Suggested `playwright.config` reporter snippet for CI:

```ts
reporter: process.env.CI
  ? [['blob'], ['html', { open: 'never' }]]
  : [['list'], ['html']]
```

## License

MIT — see [LICENSE](LICENSE).

## Author

**Avinash Sharma** — QA Automation Architect / Lead SDET  
[GitHub](https://github.com/Avinash258) · [Portfolio](https://avinash258.github.io/portfolio/)
