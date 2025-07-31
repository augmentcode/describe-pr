# Example Workflows

This directory contains example GitHub Actions workflows for the Auggie Describe PR action. Copy any of these files to your `.github/workflows/` directory and customize them for your needs.

## Available Examples

### 📝 [Basic PR Description](./basic-pr-description.yml)

The simplest setup - automatically generates descriptions for all new pull requests.

**Triggers:** When a PR is opened  
**Use case:** Standard setup for most repositories

### 🚧 [Draft PR Description](./draft-pr-description.yml)

Only generates descriptions for draft pull requests, allowing you to iterate on your changes before the final description.

**Triggers:** When a PR is opened as draft or converted to draft  
**Use case:** Teams that use draft PRs for work-in-progress reviews

### 🌿 [Feature Branch Description](./feature-branch-description.yml)

Targets specific branch patterns and adds automatic labeling for feature branches.

**Triggers:** PRs from `feature/` or `feat/` branches to main/develop/release branches  
**Use case:** Teams with structured branching strategies

### 🛡️ [Robust PR Description](./robust-pr-description.yml)

Includes error handling, retry logic, and fallback notifications when description generation fails.

**Triggers:** When a PR is opened
**Use case:** Production environments where reliability is critical

### 🏷️ [On-Demand Description](./on-demand-description.yml)

Generates descriptions only when the `auggie_describe` label is manually added to a PR.

**Triggers:** When the `auggie_describe` label is added
**Use case:** Manual control over when descriptions are generated, useful for selective usage

### 📝 [Simple Custom Guidelines](./simple-custom-guidelines.yml)

Shows basic usage of custom guidelines with simple, common requirements.

**Triggers:** When a PR is opened
**Use case:** Teams that want to add a few specific requirements without complex formatting

## Required Permissions

All workflows require these permissions:

```yaml
permissions:
  contents: read # To checkout the repository
  pull-requests: write # To update PR descriptions
```

## Customization Tips

### Trigger Events

You can modify when the workflow runs by changing the `on` section:

```yaml
# Run on multiple events
on:
  pull_request:
    types: [opened, synchronize, reopened]

# Run only for specific paths
on:
  pull_request:
    types: [opened]
    paths:
      - 'src/**'
      - '!docs/**'
```

### Conditional Execution

Add conditions to control when the action runs:

```yaml
# Only for external contributors
if: github.event.pull_request.head.repo.full_name != github.repository

# Only for large PRs
if: github.event.pull_request.changed_files > 5

# Skip for certain labels
if: "!contains(github.event.pull_request.labels.*.name, 'skip-description')"
```

## Troubleshooting

### Common Issues

1. **Missing permissions**: Ensure your workflow has `pull-requests: write` permission
2. **API rate limits**: Consider adding delays between API calls for high-traffic repositories
3. **Large PRs**: The action handles pagination automatically, but very large PRs may take longer

### Debugging

Enable debug logging by adding this to your workflow:

```yaml
env:
  ACTIONS_STEP_DEBUG: true
```
