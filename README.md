# Sync Upstream

Sync changes from an upstream repository branch into a current repository branch. For example, updating changes from the repository that was forked from.

Current limitations:
- only merge only selected branch
- only work with public upstream Github repository
- command is `git rebase upstream/branch`

To sync multiple branches, create multiple jobs.

## Usage

### Set up for scheduled trigger

copy and commit this to `.github/workflows/sync-upstream.yml` in your default branch of your repository.

```yaml
name: Sync Upstream

permissions: write-all              # grant write permission

on: 
  schedule:
    - cron: '0 0 * * 1'             # scheduled for 00:00 every Monday
  workflow_dispatch:                # trigger manually

jobs:
  sync-upstream:
    runs-on: ubuntu-latest
    steps: 
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: upstream             # set the branch to merge to
          fetch-depth: 0 
      - name: Sync Upstream
        uses: zhangnew/sync-upstream@v1
        with:
          upstream: owner/repo      # set the upstream repo
          upstream-branch: master   # set the upstream branch to merge from
          branch: upstream          # set the branch to merge to

  # set up another job to merge another branch
  sync-upstream-another-branch:
    runs-on: ubuntu-latest
    steps: 
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: another-branch       # set the branch to merge to
          fetch-depth: 0 
      - name: Sync Upstream
        uses: zhangnew/sync-upstream@v1
        with:
          upstream: owner/repo              # set the upstream repo
          upstream-branch: another-branch   # set the upstream branch to merge from
          branch: another-branch            # set the branch to merge to

```

Reference: 
- [Triggering a workflow with events](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#triggering-a-workflow-with-events)
- [Creating a composite run steps action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-run-steps-action)

