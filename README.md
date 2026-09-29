# peasant-labs organization defaults

Shared GitHub configuration for the `peasant-labs` organization.

## Discord pull request notifications

`.github/workflows/discord-pr-notify.yml` is a reusable workflow that posts pull request events to a Discord channel webhook.

Enable it in a repository by adding a caller workflow at `.github/workflows/discord-pr-notify.yml`:

```yaml
name: Notify Discord of pull requests

on:
  pull_request_target:
    types: [opened, reopened, ready_for_review, closed]

jobs:
  notify:
    if: github.event.action != 'closed' || github.event.pull_request.merged == true
    uses: peasant-labs/.github/.github/workflows/discord-pr-notify.yml@main
    secrets: inherit
```

The caller requires the organization secret `DISCORD_WEBHOOK_URL` (a Discord channel webhook URL)
to be visible to the repository. Notifications post when a pull request is opened, reopened, marked
ready for review, or merged.
