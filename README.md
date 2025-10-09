# Auggie Describe PR GitHub Action

AI-powered pull request descriptions using Auggie. This action automatically analyzes your PR changes and generates comprehensive, informative descriptions.

## Quick Start

### 1. Get Your Augment API Credentials

First, you'll need to obtain your Augment Authentication information from your local Auggie session:

Example session JSON:

```json
{
  "accessToken": "your-api-token-here",
  "tenantURL": "https://your-tenant.api.augmentcode.com"
}
```

There are 2 ways to get the credentials:

- Run `auggie tokens print`
  - Copy the JSON after `TOKEN=`
- Copy the credentials stored in your Augment cache directory, defaulting to `~/.augment/session.json`

> **⚠️ Security Warning**: These tokens are OAuth tokens tied to your personal Augment account and provide access to your Augment services. They are not tied to a team or enterprise. Treat them as sensitive credentials:
>
> - Never commit them to version control
> - Only store them in secure locations (like GitHub secrets)
> - Don't share them in plain text or expose them in logs
> - If a token is compromised, immediately revoke it using `auggie tokens revoke`

### 2. Set Up the GitHub Repository Secret

You need to add your Augment credentials to your GitHub repository:

#### Adding Secret

1. **Navigate to your repository** on GitHub
2. **Go to Settings** → **Secrets and variables** → **Actions**
3. **Add the following**:
   - **Secret**: Click "New repository secret"
     - Name: `AUGMENT_SESSION_AUTH`
     - Value: The json value from step 1

> **Need more help?** For detailed instructions on managing GitHub secrets, see GitHub's official documentation:
>
> - [Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

### 3. Create Your Workflow File

Add a new workflow file to your repository's `.github/workflows/` directory and merge it.

### Example Workflows

For complete workflow examples, see the [`example-workflows/`](./example-workflows/) directory which contains:

- **[Basic PR Description](./example-workflows/basic-pr-description.yml)** - Simple setup for all new PRs
- **[Draft PR Description](./example-workflows/draft-pr-description.yml)** - Only describe draft PRs
- **[Feature Branch Description](./example-workflows/feature-branch-description.yml)** - Target specific branch patterns with labeling
- **[Robust PR Description](./example-workflows/robust-pr-description.yml)** - Includes error handling and fallback notifications
- **[On-Demand Description](./example-workflows/on-demand-description.yml)** - Triggered by adding the `auggie_describe` label
- **[Simple Custom Guidelines](./example-workflows/simple-custom-guidelines.yml)** - Basic custom guidelines usage

Each example includes a complete workflow file that you can copy to your `.github/workflows/` directory and customize for your needs.

## Features

- **Automatic PR Analysis**: Analyzes code changes, file modifications, and diff content
- **Intelligent Descriptions**: Generates comprehensive PR descriptions using AI
- **Context-Aware**: Understands your codebase structure and change patterns
- **GitHub Integration**: Seamlessly updates PR descriptions via GitHub API

## Inputs

| Input                  | Description                                           | Required | Example                                             |
| ---------------------- | ----------------------------------------------------- | -------- | --------------------------------------------------- |
| `augment_session_auth` | Augment session authentication JSON (store as secret) | Yes      | `${{ secrets.AUGMENT_SESSION_AUTH }}`               |
| `github_token`         | GitHub token with `repo` scopes                       | Yes      | `${{ secrets.GITHUB_TOKEN }}`                       |
| `pull_number`          | The number of the pull request being described        | Yes      | `${{ github.event.pull_request.number }}`           |
| `repo_name`            | The full name (owner/repo) of the repository          | Yes      | `${{ github.repository }}`                          |
| `custom_guidelines`    | Custom guidelines for PR descriptions (optional)      | No       | See [Custom Guidelines](#custom-guidelines) section |
| `model`                | Optional model to use for generation                  | No       | e.g. `sonnet4`, from `auggie --list-models`         |
| `rules`                | JSON array of rules file paths forwarded to agent     | No       | `[".augment/rules.md"]`                             |
| `mcp_configs`          | JSON array of MCP config paths forwarded to agent     | No       | `[".augment/mcp.md"]`                               |
| `fetch_depth`          | Optional fetch depth for git checkout (default: 0)    | No       | `10` for shallow clones, `0` for full history       |

## How It Works

1. **Checkout**: Checks out the PR head to access the complete codebase
2. **Data Gathering**: Fetches PR metadata, changed files, and diff content from GitHub API
3. **Template Rendering**: Uses Nunjucks templates to create a structured instruction for the AI
4. **AI Processing**: Calls the Augment Agent to analyze the changes and generate a description
5. **PR Update**: The Augment Agent updates the PR description with the generated content

## Performance Optimization

For large repositories with long history, you can use the `fetch_depth` parameter to speed up checkouts by limiting the git history fetched:

```yaml
- name: Generate PR Description
  uses: augmentcode/describe-pr@v0
  with:
    # ... other inputs ...
    fetch_depth: 50 # Only fetch last 50 commits
```

This can significantly reduce checkout time for repositories with extensive history while still providing enough context for PR description generation.

## Custom Guidelines

You can add project-specific guidelines to any workflow by including the `custom_guidelines` input:

```yaml
- name: Generate PR Description
  uses: augmentcode/describe-pr@v0
  with:
    # ... other inputs ...
    custom_guidelines: |
      - Always mention if database migrations are included
      - Highlight any breaking API changes
      - Reference the Jira ticket number if applicable
      - Include performance impact for large changes
```

## Permissions

The action requires the following GitHub token permissions:

```yaml
permissions:
  contents: read # To checkout the repository and read files
  pull-requests: write # To update PR descriptions
```
