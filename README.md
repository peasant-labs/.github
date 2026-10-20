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

## Epic issue form

`.github/ISSUE_TEMPLATE/epic.yml` is an organization-default issue form. The epic chooser appears
in every repository that does not define its own `.github/ISSUE_TEMPLATE` folder, and issues opened
through it are labeled `epic` and added to the
[Peasant Labs Tracker](https://github.com/orgs/peasant-labs/projects/4) project.

The form adds the project on behalf of the person opening the issue, so that person must have write
access to the project. Organization owners have it; everyone else needs an explicit project write
grant. When that access is missing the issue is still created, just without the project item.

A repository that defines its own `ISSUE_TEMPLATE` folder overrides the defaults entirely —
`peasant`, `village`, and `fairtrade-design-system` today. Each needs its own copy of `epic.yml`
for the form to appear there.
