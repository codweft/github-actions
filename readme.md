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

### `pi-fix`

Reusable workflow that lets trusted repository users request an automated fix
from issue and pull request comments with `/pi-fix`.

Caller workflow:

```yaml
jobs:
  fix:
    uses: leynier/github-actions/.github/workflows/pi-fix.yml@main
    permissions:
      contents: write
      pull-requests: write
      issues: write
      actions: read
    with:
      issue_number: ${{ github.event.inputs.issue_number || '' }}
      pr_number: ${{ github.event.inputs.pr_number || '' }}
      instruction: ${{ github.event.inputs.instruction || '' }}
    secrets:
      KIMI_API_KEY: ${{ secrets.KIMI_API_KEY }}
```

The caller repository must define `KIMI_API_KEY`. The workflow only honors
comment-triggered requests from owners, members, or collaborators.

For pull request targets, `pi-fix` also loads unresolved, non-outdated review
threads through GitHub GraphQL. Commands such as `/pi-fix fix all unresolved
review threads` use that structured thread context as the primary source.
