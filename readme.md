# github-actions

Reusable GitHub Actions workflows that power [Codweft](https://codweft.com).
The Codweft GitHub App installs a single router workflow
(`.github/workflows/codweft.yml`) in destination repositories; the router
calls these reusables by version tag, e.g.:

```yaml
uses: codweft/github-actions/.github/workflows/codweft-review.yml@v0.2.0
```

You can read every workflow before you ship it. That is the point.

## Versioning

This repository is published as semantic version tags (`v0.2.0`, `v0.3.0`,
…). The Codweft control plane pins a specific tag per destination repo and
opens a pull request when it wants to bump the pinned version. **Do not use
`@main` in production callers**. Pin a tag.

## Workflows

All Codweft workflows try configured models in this order:

1. `kimi-coding / kimi-for-coding` with `KIMI_API_KEY`
2. `zai / glm-5.1` with `ZAI_API_KEY`
3. `minimax / MiniMax-M2.7` with `MINIMAX_API_KEY`

If a secret is not configured, that model is skipped. At least one of these
secrets must be configured in each caller repository or organization.

### `codweft-review`

Reusable pull request review workflow powered by coding agent.
Use `@codweft review` for comment-triggered reviews. The shorter `/review` alias is
intentionally not supported so repositories can avoid command conflicts with
other tools.

Caller workflow:

```yaml
jobs:
  review:
    uses: codweft/github-actions/.github/workflows/codweft-review.yml@main
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

### `codweft-fix`

Reusable workflow that lets trusted repository users request an automated fix
from issue and pull request comments with `@codweft fix`.

Caller workflow:

```yaml
jobs:
  fix:
    uses: codweft/github-actions/.github/workflows/codweft-fix.yml@main
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

For pull request targets, `codweft-fix` also loads unresolved, non-outdated review
threads through GitHub GraphQL. Commands such as `@codweft fix all unresolved
review threads` use that structured thread context as the primary source.

### `codweft-implement`

Reusable workflow that lets trusted repository users ask Codweft to implement a full
issue with `@codweft implement` or by adding the `ready-for-codweft` label.

The workflow asks clarifying questions when the issue is not ready. When the
issue is clear, it creates a draft pull request, implements the issue, marks the
pull request ready, and comments `@codweft review` on the generated pull request.

Caller workflow:

```yaml
jobs:
  implement:
    uses: codweft/github-actions/.github/workflows/codweft-implement.yml@main
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

### `codweft-resolve-conflicts`

Reusable workflow that lets trusted repository users request an automated merge
conflict resolution from pull request comments with `@codweft resolve conflicts`.

The workflow creates a separate stacked pull request against the original pull
request head branch and includes the resolution decisions in the generated pull
request body.

Caller workflow:

```yaml
jobs:
  resolve-conflicts:
    uses: codweft/github-actions/.github/workflows/codweft-resolve-conflicts.yml@main
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
