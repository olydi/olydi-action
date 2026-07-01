# OLYDI Scan

GitHub Action for [OLYDI](https://olydi.com) — an exploit-gated code-security scan. Runs on push or pull request, uploads results to the repository's Security tab as SARIF, and can gate the job on findings at or above a chosen severity.

This action is a thin wrapper around [`@olydi/cli`](https://www.npmjs.com/package/@olydi/cli) — it does not implement scanning itself; it triggers a scan via the OLYDI API, waits for completion, converts findings to SARIF, and uploads them.

## Usage

```yaml
name: olydi
on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: read
  security-events: write

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: olydi/olydi-action@v2
        with:
          token: ${{ secrets.OLYDI_TOKEN }}
          fail-on: high
```

Generate a token at [app.olydi.com/settings/api-keys](https://app.olydi.com/settings/api-keys) and store it as a repository secret named `OLYDI_TOKEN`.

## Inputs

| Input | Description | Default |
|---|---|---|
| `token` | OLYDI API token (required). | — |
| `repo` | Target repository as `owner/repo`. | `github.repository` (the calling repo) |
| `fail-on` | Fail the job if findings at/above this severity exist: `critical`, `high`, `medium`, `low`. Set to an empty string to never fail the job on findings. | `high` |
| `upload-sarif` | Upload SARIF results to the Security tab. | `true` |
| `sarif-path` | Path to write the SARIF output. | `olydi-results.sarif` |

## Outputs

| Output | Description |
|---|---|
| `sarif-path` | Path to the generated SARIF file. |
| `run-id` | The OLYDI scan run ID. |

## Permissions

The action requires `security-events: write` to upload SARIF to the Security tab, and `contents: read` to check out the repository. It does not request write access to repository contents — remediation pull requests are opened by the [OLYDI GitHub App](https://olydi.com/get-started), not this action.

## License

Apache-2.0
