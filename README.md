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

## Epic tracker sync

`.github/workflows/tracker-epic-sync.yml` sweeps every open epic in the
organization into the [Peasant Labs Tracker](https://github.com/orgs/peasant-labs/projects/4)
project. It runs every 15 minutes and can be started manually.

The native "Auto-add to project" workflow cannot cover the organization: GitHub
Free allows one auto-add workflow, each workflow targets one repository, and its
filters cannot match issue titles. The sweeper is the organization-wide
equivalent. It recognizes an epic when any of these is true:

- the issue carries the `epic` label;
- the title starts with `[Epic]`, `Epic`, or `EPIC`;
- the issue has at least one sub-issue.

New epics are added with `Status = Backlog`, a size (`L`, or `XL` above ten
sub-issues), the repository's team, and a priority parsed from a `[P0]`-`[P3]`
title tag or an explicit severity/priority line in the body (P2 otherwise). An
existing Priority set at issue creation is preserved. The sweep is idempotent
and fills a field only when it is empty.

### Required secret

Add a repository secret named `PROJECT_TOKEN` to this repository. A fine-grained
personal access token owned by `peasant-labs` needs:

- **All repositories:** Issues: read.
- **All repositories:** Issues: write, if the token should also set the
  organization-level Priority field.
- **Organization:** Projects: read and write.

Without the secret the job fails closed and writes nothing.

### Manual run

Run the workflow from the Actions tab with `dry_run` enabled to see the
decisions without changing the project.

### Developer access

Editing the project (adding items and setting fields) requires the **Write**
base role. Organization owners are project admins. An organization owner can set
the base role in the project's **Settings → Manage access**. A public project is
viewable by everyone, but only Write access can change it.
