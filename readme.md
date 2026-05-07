# github-actions

Reusable GitHub Actions workflows and automations.

## Workflows

All Pi workflows try configured models in this order:

1. `kimi-coding / kimi-for-coding` with `KIMI_API_KEY`
2. `zai / glm-5.1` with `ZAI_API_KEY`
3. `minimax / MiniMax-M2.7` with `MINIMAX_API_KEY`

If a secret is not configured, that model is skipped. At least one of these
secrets must be configured in each caller repository or organization.

### `pi-review`

Reusable pull request review workflow powered by Pi Coding Agent.
Use `/pi-review` for comment-triggered reviews. The shorter `/review` alias is
intentionally not supported so repositories can avoid command conflicts with
other tools.

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
      ZAI_API_KEY: ${{ secrets.ZAI_API_KEY }}
      MINIMAX_API_KEY: ${{ secrets.MINIMAX_API_KEY }}
```

The caller repository must define at least one supported model API key.

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
      ZAI_API_KEY: ${{ secrets.ZAI_API_KEY }}
      MINIMAX_API_KEY: ${{ secrets.MINIMAX_API_KEY }}
```

The caller repository must define at least one supported model API key. The
workflow only honors comment-triggered requests from owners, members, or
collaborators.

For pull request targets, `pi-fix` also loads unresolved, non-outdated review
threads through GitHub GraphQL. Commands such as `/pi-fix fix all unresolved
review threads` use that structured thread context as the primary source.

### `pi-implement`

Reusable workflow that lets trusted repository users ask Pi to implement a full
issue with `/pi-implement` or by adding the `ready-for-pi` label.

The workflow asks clarifying questions when the issue is not ready. When the
issue is clear, it creates a draft pull request, implements the issue, marks the
pull request ready, and comments `/pi-review` on the generated pull request.

Caller workflow:

```yaml
jobs:
  implement:
    uses: leynier/github-actions/.github/workflows/pi-implement.yml@main
    permissions:
      contents: write
      pull-requests: write
      issues: write
      actions: write
    with:
      issue_number: ${{ github.event.inputs.issue_number || '' }}
      instruction: ${{ github.event.inputs.instruction || '' }}
    secrets:
      KIMI_API_KEY: ${{ secrets.KIMI_API_KEY }}
      ZAI_API_KEY: ${{ secrets.ZAI_API_KEY }}
      MINIMAX_API_KEY: ${{ secrets.MINIMAX_API_KEY }}
```

### `pi-resolve-conflicts`

Reusable workflow that lets trusted repository users request an automated merge
conflict resolution from pull request comments with `/pi-resolve-conflicts`.

The workflow creates a separate stacked pull request against the original pull
request head branch and includes the resolution decisions in the generated pull
request body.

Caller workflow:

```yaml
jobs:
  resolve-conflicts:
    uses: leynier/github-actions/.github/workflows/pi-resolve-conflicts.yml@main
    permissions:
      contents: write
      pull-requests: write
      issues: write
      actions: read
    with:
      pr_number: ${{ github.event.inputs.pr_number || '' }}
      instruction: ${{ github.event.inputs.instruction || '' }}
    secrets:
      KIMI_API_KEY: ${{ secrets.KIMI_API_KEY }}
      ZAI_API_KEY: ${{ secrets.ZAI_API_KEY }}
      MINIMAX_API_KEY: ${{ secrets.MINIMAX_API_KEY }}
```
