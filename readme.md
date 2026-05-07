# github-actions

Reusable GitHub Actions workflows and automations.

## Workflows

### `pi-review`

Reusable pull request review workflow powered by Pi Coding Agent.

Caller workflow:

```yaml
jobs:
  review:
    uses: leynier/github-actions/.github/workflows/pi-review.yml@main
    permissions:
      contents: read
      pull-requests: write
      issues: write
    with:
      pr_number: ${{ inputs.pr_number || '' }}
    secrets:
      KIMI_API_KEY: ${{ secrets.KIMI_API_KEY }}
```

The caller repository must define `KIMI_API_KEY`.
