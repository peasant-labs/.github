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

## Epic tracking

`.github/workflows/add-epic-to-project.yml` is a reusable workflow that adds open `epic`-labeled
issues and pull requests to [Peasant Labs Tracker](https://github.com/orgs/peasant-labs/projects/4).

Enable it in a repository by adding a caller workflow at `.github/workflows/add-epic-to-project.yml`:

```yaml
name: Add epic to Peasant Labs Tracker

on:
  issues:
    types: [opened, reopened, labeled, transferred]
  pull_request_target:
    types: [opened, reopened, labeled]

permissions:
  contents: read

jobs:
  add-to-project:
    uses: peasant-labs/.github/.github/workflows/add-epic-to-project.yml@main
    secrets:
      ADD_TO_PROJECT_PAT: ${{ secrets.ADD_TO_PROJECT_PAT }}
```

The caller requires the organization secret `ADD_TO_PROJECT_PAT` to be visible to the repository:
a fine-grained token with *Projects: read & write* for the organization and *Issues* / *Pull
requests: read* on the repositories it reads. `GITHUB_TOKEN` cannot reach projects, so the secret is
required rather than optional. When it is missing the run reports a notice and adds nothing, so the
caller can be installed before the credential exists.

GitHub delivers events only to workflows in the repository where the event happened, so each
repository that wants tracking needs its own caller; the shared workflow keeps the behaviour in one
place.
