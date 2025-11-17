# How to integrate Claude Code with CI/CD pipelines

**Article ID**: KB-095
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 30-45 minutes

## Overview
This guide demonstrates how to integrate Claude Code into your CI/CD pipelines for automated code reviews, testing, documentation generation, and issue triage. By embedding Claude Code into your continuous integration workflows, you can leverage AI-powered assistance for routine development tasks, improve code quality, and accelerate your development cycle across platforms like GitHub Actions, GitLab CI/CD, Azure DevOps, and others.

## Prerequisites
- Active Claude Pro or Max account with API access
- Repository admin access for the target repositories
- Basic understanding of CI/CD concepts and YAML configuration
- Familiarity with your chosen CI/CD platform (GitHub Actions, GitLab CI, etc.)
- Anthropic API key (obtain from console.anthropic.com)
- Related article: KB-001 (Access Claude Code Web)

## Steps

### Step 1: Choose Your CI/CD Integration Platform

Claude Code supports multiple CI/CD platforms with varying levels of official support:

**Official Integrations:**
- **GitHub Actions**: Fully supported via `anthropics/claude-code-action` (recommended)
- **GitLab CI/CD**: Currently in beta with official support

**Community/Third-Party Support:**
- **Azure DevOps**: Available via marketplace extension
- **CircleCI**: MCP server integration available
- **Jenkins, Travis CI, Other platforms**: Use headless mode

For this guide, we'll focus on GitHub Actions as the primary example, with notes on adapting to other platforms.

### Step 2: Set Up Authentication

Before integrating Claude Code, configure your authentication credentials as secrets in your CI/CD platform.

**For GitHub Actions:**

Navigate to your repository's Settings → Secrets and variables → Actions → New repository secret.

Add one of the following authentication methods:

**Option A: Direct API Key (Recommended for most users)**
```yaml
Secret name: ANTHROPIC_API_KEY
Secret value: sk-ant-api03-[your-key-here]
```

**Option B: OAuth Token (Pro/Max users)**

First, generate your token locally:
```bash
claude setup-token
```

Then add it as a secret:
```yaml
Secret name: CLAUDE_CODE_OAUTH_TOKEN
Secret value: [token-generated-from-command]
```

**For GitLab CI/CD:**

Navigate to Settings → CI/CD → Variables → Add Variable:
```yaml
Key: ANTHROPIC_API_KEY
Value: sk-ant-api03-[your-key-here]
Flags: ✓ Protect variable, ✓ Mask variable
```

**Security Warning:** Never commit API keys directly to your repository. Always use platform-provided secrets management.

### Step 3: Install the GitHub App (GitHub Actions Only)

The easiest method is using Claude Code's terminal command:

```bash
claude /install-github-app
```

This interactive command will:
1. Guide you through installing the Claude GitHub App
2. Configure required repository permissions
3. Set up necessary secrets automatically

Alternatively, manually install the app at: https://github.com/apps/claude

Grant the app these minimum permissions:
- Repository Contents: Read & Write
- Issues: Read & Write
- Pull Requests: Read & Write

### Step 4: Create Your First Workflow File

**For GitHub Actions:**

Create `.github/workflows/claude.yml` in your repository:

```yaml
name: Claude Code CI/CD

on:
  pull_request:
    types: [opened, synchronize, reopened]
  issue_comment:
    types: [created]
  issues:
    types: [opened, labeled]

permissions:
  contents: write
  pull-requests: write
  issues: write

jobs:
  claude-assistant:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run Claude Code
        uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

This basic workflow activates Claude when:
- Pull requests are opened or updated
- Comments mention @claude
- Issues are opened or labeled

**For GitLab CI/CD:**

Create or update `.gitlab-ci.yml`:

```yaml
claude-review:
  image: anthropic/claude-code:latest
  stage: test
  script:
    - export ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY
    - claude -p "Review this merge request for code quality, security issues, and best practices"
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
  artifacts:
    reports:
      codequality: claude-review.json
```

### Step 5: Configure Advanced Automation Use Cases

Claude Code supports multiple automation patterns. Here are common use cases:

**Automated PR Code Review:**

```yaml
- name: Code Review
  uses: anthropics/claude-code-action@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      Review this pull request for:
      - Code quality and maintainability
      - Security vulnerabilities
      - Performance issues
      - Best practices compliance
      - Test coverage gaps
```

**Automated Documentation Generation:**

```yaml
- name: Update Documentation
  uses: anthropics/claude-code-action@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      Review changed files and update documentation:
      - Add JSDoc comments to new functions
      - Update README if APIs changed
      - Generate changelog entries
    claude_args: --allow-tools Write,Edit
```

**Issue Triage and Labeling:**

```yaml
on:
  issues:
    types: [opened]

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Analyze this issue and:
            - Assign appropriate labels (bug, feature, documentation, etc.)
            - Suggest priority level
            - Identify related components
```

**CI Failure Diagnosis:**

```yaml
on:
  workflow_run:
    workflows: ["Tests"]
    types: [completed]

jobs:
  diagnose-failure:
    if: ${{ github.event.workflow_run.conclusion == 'failure' }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Analyze the test failure logs and:
            - Identify the root cause
            - Suggest potential fixes
            - Create a comment on the PR with findings
```

### Step 6: Use Headless Mode for Custom CI/CD Platforms

For platforms without official integrations (Jenkins, CircleCI, Travis CI, etc.), use Claude Code's headless mode:

**Basic Headless Command:**

```bash
claude -p "Your prompt here" --output-format stream-json
```

**Jenkins Pipeline Example:**

```groovy
pipeline {
    agent any

    environment {
        ANTHROPIC_API_KEY = credentials('anthropic-api-key')
    }

    stages {
        stage('Code Review') {
            steps {
                sh '''
                    npm install -g @anthropic/claude-code
                    claude -p "Review the code changes in this commit for quality and security" \
                        --output-format stream-json > review-results.json
                '''
                archiveArtifacts artifacts: 'review-results.json'
            }
        }
    }
}
```

**CircleCI Configuration (using MCP Server):**

```yaml
version: 2.1

orbs:
  claude: anthropic/claude-code@1.0

jobs:
  review:
    docker:
      - image: cimg/node:lts
    steps:
      - checkout
      - run:
          name: Install Claude Code
          command: npm install -g @anthropic/claude-code
      - run:
          name: Run Code Review
          command: |
            export ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
            claude -p "Review this PR for code quality" --json
```

**Azure DevOps Pipeline:**

```yaml
trigger:
  - main
  - develop

pool:
  vmImage: 'ubuntu-latest'

steps:
  - task: ClaudeCodeBaseTask@1
    inputs:
      anthropicApiKey: $(ANTHROPIC_API_KEY)
      prompt: 'Review code changes and suggest improvements'
      outputFormat: 'json'
    displayName: 'Claude Code Review'
```

### Step 7: Implement Advanced Patterns

**Fanning Out (Batch Processing):**

Use this pattern for large-scale migrations or batch analyses:

```yaml
- name: Batch File Analysis
  run: |
    # Generate list of files to analyze
    git diff --name-only main...HEAD > files.txt

    # Process each file with Claude
    while IFS= read -r file; do
      claude -p "Analyze $file for migration to React 18" \
        --json >> migration-report.json
    done < files.txt
```

**Pipelining (Data Processing):**

Integrate Claude into existing data workflows:

```bash
# Extract data, process with Claude, format output
git log --oneline -10 | \
  claude -p "Generate release notes from these commits" --json | \
  jq '.summary' > RELEASE_NOTES.md
```

### Step 8: Configure Progress Tracking and Notifications

Enable visual progress tracking in GitHub Actions:

```yaml
- name: Multi-Step Task
  uses: anthropics/claude-code-action@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: |
      Complete these tasks:
      - [ ] Review security vulnerabilities
      - [ ] Update dependencies
      - [ ] Run test suite
      - [ ] Generate documentation
```

Claude will update the checkboxes as each task completes, providing real-time visibility.

### Step 9: Set Up Custom GitHub App (Optional)

For organizations with strict security policies or using AWS Bedrock/Google Vertex AI:

1. **Use the quick setup tool:**
   - Download `create-app.html` from the claude-code-action repository
   - Open in browser
   - Click "Create App for Organization"
   - Enter your organization name

2. **Configure custom authentication:**

```yaml
steps:
  - name: Generate App Token
    id: app-token
    uses: actions/create-github-app-token@v1
    with:
      app-id: ${{ secrets.APP_ID }}
      private-key: ${{ secrets.APP_PRIVATE_KEY }}

  - name: Run Claude with Custom App
    uses: anthropics/claude-code-action@v1
    with:
      github_token: ${{ steps.app-token.outputs.token }}
      anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

3. **Store credentials securely:**
   - `APP_ID`: Your GitHub App's numeric ID
   - `APP_PRIVATE_KEY`: Contents of the downloaded `.pem` file

### Step 10: Test and Monitor Your Integration

**Test the workflow:**

1. Create a test pull request
2. Verify Claude responds to @claude mentions
3. Check workflow logs for errors
4. Review Claude's output quality

**Monitor performance:**

```yaml
- name: Run with Verbose Logging
  uses: anthropics/claude-code-action@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: --verbose
    prompt: "Test prompt"
```

**Set up failure notifications:**

```yaml
- name: Notify on Failure
  if: failure()
  uses: actions/github-script@v7
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: '⚠️ Claude Code workflow failed. Check the [logs](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }})'
      })
```

## Expected Results

After successful integration, you should observe:

1. **Automated PR Reviews**: Claude automatically comments on pull requests with code quality feedback, security concerns, and improvement suggestions
2. **Issue Triage**: New issues receive automatic labels and priority assessments within minutes of creation
3. **Documentation Updates**: Documentation stays synchronized with code changes automatically
4. **CI Failure Analysis**: Failed builds receive diagnostic comments identifying root causes
5. **Visual Progress Tracking**: Multi-step tasks show real-time checkbox updates as Claude completes each item
6. **Faster Development Cycle**: Routine tasks complete without manual intervention, freeing developer time for complex problem-solving

Example Claude comment on a PR:
```
@claude reviewed this pull request:

✅ Code Quality: Well-structured, follows project conventions
⚠️ Security: Potential SQL injection on line 47 - use parameterized queries
✅ Performance: No obvious bottlenecks
⚠️ Testing: Missing test coverage for the new getUserData() function

Suggested improvements:
1. Add input validation to the authentication endpoint
2. Include unit tests for edge cases
3. Update API documentation for the new /users route
```

## Troubleshooting

### Common Issue 1: Workflow Not Triggering
**Problem**: Claude Code action doesn't run when PRs are created or @claude is mentioned
**Solution**:
- Verify the workflow file is in `.github/workflows/` directory
- Check that `permissions` block includes necessary scopes
- Ensure the GitHub App is installed on the repository
- Review GitHub Actions logs for permission errors
- Confirm the trigger events match your use case (check `on:` section)

### Common Issue 2: Authentication Failures
**Problem**: "Invalid API key" or "Unauthorized" errors in workflow logs
**Solution**:
- Verify `ANTHROPIC_API_KEY` secret is set correctly in repository settings
- Check that the API key starts with `sk-ant-api03-`
- Ensure the key hasn't expired or been revoked
- For OAuth: regenerate token with `claude setup-token`
- Confirm secrets are accessible to the workflow (check organization/repository secret scope)

### Common Issue 3: Rate Limiting or Timeout Errors
**Problem**: Workflows fail with "Rate limit exceeded" or timeout messages
**Solution**:
- Implement conditional triggering to reduce API calls:
  ```yaml
  if: github.event.action == 'opened' || contains(github.event.comment.body, '@claude')
  ```
- Add timeout configuration:
  ```yaml
  timeout-minutes: 10
  ```
- Use caching for repetitive analyses
- Consider upgrading API tier for higher rate limits
- Space out batch operations with delays

### Common Issue 4: Insufficient Permissions
**Problem**: Claude cannot write comments, create PRs, or modify files
**Solution**:
- Add required permissions to workflow:
  ```yaml
  permissions:
    contents: write
    pull-requests: write
    issues: write
  ```
- For custom GitHub Apps, verify the app has sufficient repository permissions
- Check organization security policies aren't blocking the app
- Review GitHub App installation settings

### Common Issue 5: Headless Mode Session Persistence
**Problem**: Context lost between headless mode invocations
**Solution**:
- Headless mode doesn't persist sessions - this is by design
- Pass all necessary context in each invocation:
  ```bash
  claude -p "Context: This is a React project. Task: Review file.js" --json
  ```
- Use artifacts or cache to maintain state across workflow runs:
  ```yaml
  - uses: actions/cache@v3
    with:
      path: .claude-cache
      key: claude-${{ hashFiles('**/*.js') }}
  ```

### Common Issue 6: Large Repository Performance
**Problem**: Claude takes too long analyzing large codebases or complex PRs
**Solution**:
- Limit analysis scope to changed files only:
  ```yaml
  prompt: "Review only the files changed in this PR"
  ```
- Use path filters in workflow triggers:
  ```yaml
  on:
    pull_request:
      paths:
        - 'src/**'
        - '!docs/**'
  ```
- Break large tasks into smaller, focused prompts
- Configure file exclusion patterns in CLAUDE.md

## Additional Tips

- **Start Small**: Begin with simple PR reviews before implementing complex automation workflows
- **Iterate Prompts**: Refine your prompts based on Claude's output quality - more specific prompts yield better results
- **Use CLAUDE.md**: Create a CLAUDE.md file in your repository root to provide project-specific context Claude will use in all CI runs
- **Combine with Existing Tools**: Claude augments, not replaces, traditional CI tools - use it alongside linters, test runners, and security scanners
- **Monitor Costs**: Track API usage through the Anthropic console to manage costs, especially for high-frequency workflows
- **Version Pin Actions**: Use specific versions (`@v1.2.3`) instead of floating tags (`@v1`) for production workflows to ensure stability
- **Conditional Execution**: Use `if:` conditions to run Claude only when needed, reducing unnecessary API calls
- **Security Scanning**: Leverage Claude for security reviews but also use dedicated security tools (Snyk, Dependabot) for comprehensive coverage
- **Documentation First**: Update CLAUDE.md with coding standards, architecture decisions, and project conventions for consistent CI behavior
- **Feedback Loop**: Review Claude's CI comments regularly and adjust prompts to improve accuracy and relevance
- **Team Coordination**: Document your CI/CD integration patterns in your team wiki so all developers understand when and how Claude activates
- **Gradual Rollout**: Test workflows on a single repository before deploying organization-wide
- **Custom Slash Commands**: Create project-specific slash commands for common CI tasks and reference them in workflows

## Related Articles
- KB-001: How to access Claude Code Web
- KB-003: How to authenticate your GitHub account
- KB-033: How to create a pull request automatically from completed work
- KB-034: How to review PR change summaries generated by Claude
- KB-050: How to audit Claude actions
- KB-056: How to perform code reviews
- KB-061: How to create SessionStart hook for repository initialization
- KB-068: How to create your first custom slash command
- KB-075: How to install and configure an MCP server

## Sources
- Claude Code GitHub Actions - https://docs.claude.com/en/docs/claude-code/github-actions (Accessed: November 16, 2025)
- Claude Code Action Repository - https://github.com/anthropics/claude-code-action (Accessed: November 16, 2025)
- Claude Code Action Setup Guide - https://github.com/anthropics/claude-code-action/blob/main/docs/setup.md (Accessed: November 16, 2025)
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code GitLab CI/CD - https://docs.claude.com/en/docs/claude-code/gitlab-ci-cd (Accessed: November 16, 2025)
- Claude Code Base Task for Azure DevOps - https://marketplace.visualstudio.com/items?itemName=claswen.claude-code-base-task (Accessed: November 16, 2025)
- CircleCI MCP Server - https://circleci.com/mcp/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
