# Setup Guide

When using this codebase to migrate repos in your own organization, here are a few things that will need to be created/modified:

## Variables & Secrets

Create these [variables](https://docs.github.com/en/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository) and [secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository) on the repository that is hosting this migration utility according to the table above.

See [Variable and Secret Automation](#variable-secret-script) for a script to automate the creation of variables and secrets.

There are several migration workflows that can be used to migrate repositories from various sources to GitHub.com. Each workflow is configured to migrate repositories from a specific source to a specific target. The following table lists the available workflows and their configurations.

| Issue Template Name | Workflow Name | Source | Target | Vars | Secrets | Notes |
|---------------|---------------|--------|--------|-------|-------|-------|
| GHEC repos to GitHub migration [GEI] | `.github/workflows/migration-github-repos-gei.yml` | GitHub.com | GitHub.com | TARGET_ORGANIZATION | TARGET_ADMIN_TOKEN SOURCE_ADMIN_TOKEN | Uses [GEI](https://github.com/github/gh-gei) |

> [!NOTE]
> - `SOURCE_ADMIN_TOKEN` and `TARGET_ADMIN_TOKEN` must have the `admin:org`, `repo`, and `workflow` scope set.

### Variable Secret Script

Review the following files:

- [.env.variables](.env.variables) - For Variables
- [.env.example](.env.example) - For Secrets Example

Once you have decided based on the chart above which variable and secrets you need to create, copy the `.env.example` and create your own `.env`. Then you can use the [setup-vars-and-secrets.sh](setup-vars-and-secrets.sh) script to automate the process.

To learn how to use the script run:

```bash
./setup-vars-and-secrets.sh -h
```

Alternatively, you can create the secrets and variables manually inside of the GitHub repository.

## Issue Labels

Verify that the [bootstrap actions](.github/workflows/bootstrap.yml) ran successfully as it creates the necessary issue labels. If not, create the following [issue labels](https://docs.github.com/en/issues/using-labels-and-milestones-to-track-work/managing-labels#creating-a-label):

1. `migration` (for all)
2. `gei` (for GEI)

### Runner Setup

If necessary, update the self-hosted runner label in your workflow so that it picks up the designated runner - the runner label otherwise defaults to `self-hosted`. Runners need to the following software installed:

- curl, wget, unzip, ssh, jq, git, [github cli](https://github.com/cli/cli#installation), node 20

### Workflow Modifications

**For GEI**:

1. If not running on a Ubuntu runner, or if you don't want to automatically install the pre-requisites, switch the `env.INSTALL_PREREQS` to `'false'` in
[.github/workflows/shared-github-enterprise-cloud-gei.yml#L26](/.github/workflows/shared-github-enterprise-cloud-gei.yml#L26)
2. Ensure that the `SOURCE_ADMIN_TOKEN` has the [appropriate PAT scopes](https://docs.github.com/en/early-access/enterprise-importer/preparing-to-migrate-with-github-enterprise-importer/managing-access-for-github-enterprise-importer#required-scopes-for-personal-access-tokens) for running a migration (source organization) or has been [granted the migrator role](https://docs.github.com/en/early-access/enterprise-importer/preparing-to-migrate-with-github-enterprise-importer/granting-the-migrator-role)
