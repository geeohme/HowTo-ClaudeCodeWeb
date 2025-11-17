# How to automate PR comments through GitHub Actions

**Article ID**: KB-096
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 25-35 minutes

## Overview
GitHub Actions can automatically comment on pull requests to provide feedback, status updates, test results, or deployment information. This automation streamlines your development workflow by keeping team members informed without manual intervention. This guide covers multiple approaches to automated PR commenting, from simple status updates to intelligent code review using Claude Code's official GitHub Action.

## Prerequisites
- GitHub repository with Actions enabled
- Write permissions on the repository
- Basic understanding of YAML syntax
- Familiarity with GitHub Actions workflows
- Understanding of pull request workflow
- Knowledge of GitHub API tokens and permissions (KB-047)
- Optional: Claude Code subscription for AI-powered automation

## Understanding PR Comment Automation

### What You Can Automate

**Common use cases:**
- Deployment status and preview URLs
- Test coverage reports and results
- Build success/failure notifications
- Security scan findings
- Code quality metrics
- Breaking change warnings
- AI-powered code reviews
- Dependency update summaries
- Performance benchmark results

### Available Approaches

**Method 1: Official GitHub Script Action** (Recommended for most cases)
- Uses `actions/github-script@v8`
- Direct access to GitHub REST API
- Full JavaScript flexibility
- Official GitHub support

**Method 2: Third-Party Comment Actions** (Simplest setup)
- Pre-built actions like `thollander/actions-comment-pull-request@v3`
- Minimal configuration required
- Built-in features like comment updating

**Method 3: Claude Code Action** (Advanced AI integration)
- Uses `anthropics/claude-code-action`
- AI-powered code review and implementation
- Responds to @claude mentions
- Automated security reviews

### Permission Requirements

All PR comment automation requires:
```yaml
permissions:
  pull-requests: write
```

For fork PRs, use the `pull_request_target` event instead of `pull_request` to grant write permissions.

## Steps

### Step 1: Create Basic Workflow with GitHub Script

The official `actions/github-script` action provides the most flexible approach for commenting on PRs.

**Create workflow file:**

Navigate to your repository and create `.github/workflows/pr-comment.yml`:

```yaml
name: PR Comment Automation

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  pull-requests: write

jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Add comment to PR
        uses: actions/github-script@v8
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '👋 Thanks for your pull request! Our CI checks are now running.'
            })
```

**Key points:**
- `pull_request` event triggers on PR open or update
- `context.issue.number` refers to the PR number (issues and PRs share the same API)
- `github.rest.issues.createComment` is the API method for commenting
- `${{ secrets.GITHUB_TOKEN }}` is automatically available in workflows

### Step 2: Add Dynamic Content to Comments

Enhance your comments with workflow information and context:

```yaml
- name: Create detailed PR comment
  uses: actions/github-script@v8
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    script: |
      const comment = `
      ## 🚀 PR Summary

      **Author**: @${{ github.actor }}
      **Branch**: \`${{ github.head_ref }}\` → \`${{ github.base_ref }}\`
      **Commit**: ${{ github.sha }}
      **Workflow Run**: [${{ github.run_id }}](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }})

      ### ✅ Next Steps
      - CI checks are running
      - Review will be requested after checks pass
      - Ensure all tests pass before merging

      ---
      *Automated comment by GitHub Actions*
      `;

      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: comment
      });
```

**Available context variables:**
- `github.actor` - User who triggered the workflow
- `github.head_ref` - Source branch name
- `github.base_ref` - Target branch name
- `github.sha` - Commit SHA
- `github.run_id` - Workflow run identifier

### Step 3: Update Existing Comments Instead of Creating New Ones

To avoid cluttering PRs with multiple comments, update an existing comment:

```yaml
- name: Find or create comment
  uses: actions/github-script@v8
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    script: |
      const marker = '<!-- PR_AUTOMATION_MARKER -->';
      const body = `${marker}
      ## 🔄 Build Status

      **Status**: In Progress ⏳
      **Started**: ${new Date().toISOString()}

      This comment will be updated when the build completes.
      `;

      // Find existing comment
      const { data: comments } = await github.rest.issues.listComments({
        owner: context.repo.owner,
        repo: context.repo.repo,
        issue_number: context.issue.number,
      });

      const existingComment = comments.find(c => c.body.includes(marker));

      if (existingComment) {
        // Update existing comment
        await github.rest.issues.updateComment({
          owner: context.repo.owner,
          repo: context.repo.repo,
          comment_id: existingComment.id,
          body: body
        });
      } else {
        // Create new comment
        await github.rest.issues.createComment({
          owner: context.repo.owner,
          repo: context.repo.repo,
          issue_number: context.issue.number,
          body: body
        });
      }
```

**How it works:**
- HTML comment `<!-- PR_AUTOMATION_MARKER -->` identifies your automated comment
- Search for existing comment with this marker
- Update if found, create if not
- Results in single, updated comment instead of multiple comments

### Step 4: Use Third-Party Action for Simpler Setup

The `thollander/actions-comment-pull-request` action simplifies comment management:

```yaml
name: Simple PR Comment

on:
  pull_request:
    types: [opened]

permissions:
  pull-requests: write

jobs:
  welcome:
    runs-on: ubuntu-latest
    steps:
      - name: Comment PR
        uses: thollander/actions-comment-pull-request@v3
        with:
          message: |
            ## Welcome! 👋

            Thank you for contributing to this project!

            ### Before merging:
            - ✅ Ensure all tests pass
            - ✅ Update documentation if needed
            - ✅ Add changelog entry

            A maintainer will review your PR soon.
          comment-tag: 'welcome-message'
```

**Configuration options:**
- `message` - Comment content (supports markdown)
- `comment-tag` - Unique identifier for updating
- `mode` - Behavior: `upsert` (default), `recreate`, `delete`
- `reactions` - Add emoji reactions: `eyes, rocket, heart`
- `pr-number` - Specify PR number (for manual workflows)

**Advanced example with file content:**

```yaml
- name: Comment with file contents
  uses: thollander/actions-comment-pull-request@v3
  with:
    file-path: './test-results/summary.md'
    comment-tag: 'test-results'
    reactions: 'eyes, rocket'
```

### Step 5: Add Test Results to PR Comments

Integrate test output into PR comments:

```yaml
name: Test and Report

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  pull-requests: write
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        id: tests
        run: |
          npm test -- --coverage --json > test-results.json || true
          echo "test_status=$?" >> $GITHUB_OUTPUT

      - name: Generate test summary
        id: summary
        run: |
          PASSED=$(jq '.numPassedTests' test-results.json)
          FAILED=$(jq '.numFailedTests' test-results.json)
          TOTAL=$(jq '.numTotalTests' test-results.json)
          COVERAGE=$(jq '.coverageMap.total.lines.pct' test-results.json)

          echo "passed=$PASSED" >> $GITHUB_OUTPUT
          echo "failed=$FAILED" >> $GITHUB_OUTPUT
          echo "total=$TOTAL" >> $GITHUB_OUTPUT
          echo "coverage=$COVERAGE" >> $GITHUB_OUTPUT

      - name: Comment test results
        uses: actions/github-script@v8
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const passed = ${{ steps.summary.outputs.passed }};
            const failed = ${{ steps.summary.outputs.failed }};
            const total = ${{ steps.summary.outputs.total }};
            const coverage = ${{ steps.summary.outputs.coverage }};
            const status = failed === 0 ? '✅ PASSED' : '❌ FAILED';
            const emoji = failed === 0 ? '🎉' : '⚠️';

            const body = `
            ## ${emoji} Test Results

            **Status**: ${status}
            **Tests Passed**: ${passed}/${total}
            **Tests Failed**: ${failed}
            **Coverage**: ${coverage}%

            ${failed > 0 ? '### ⚠️ Failed Tests\nPlease review the workflow logs for details.' : '### 🎉 All tests passed!'}

            [View Full Report](${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }})
            `;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: body
            });
```

### Step 6: Implement Conditional Comments Based on File Changes

Comment only when specific files are modified:

```yaml
name: Conditional PR Comments

on:
  pull_request:
    paths:
      - 'src/**/*.js'
      - 'package.json'

permissions:
  pull-requests: write

jobs:
  check-changes:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Get changed files
        id: changed-files
        uses: tj-actions/changed-files@v45
        with:
          files: |
            src/**/*.js
            package.json

      - name: Comment on package.json changes
        if: contains(steps.changed-files.outputs.all_changed_files, 'package.json')
        uses: thollander/actions-comment-pull-request@v3
        with:
          message: |
            ## 📦 Dependencies Changed

            This PR modifies `package.json`. Please ensure:
            - Lock file is updated (`package-lock.json` or `yarn.lock`)
            - No unnecessary dependencies added
            - Security vulnerabilities checked
            - Changes documented in changelog
          comment-tag: 'dependency-check'

      - name: Comment on JavaScript changes
        if: contains(steps.changed-files.outputs.all_changed_files, '.js')
        uses: actions/github-script@v8
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const changedFiles = '${{ steps.changed-files.outputs.all_changed_files }}'.split(' ');
            const jsFiles = changedFiles.filter(f => f.endsWith('.js'));

            const body = `
            ## 🔍 Code Review Checklist

            This PR modifies ${jsFiles.length} JavaScript file(s):
            ${jsFiles.map(f => `- \`${f}\``).join('\n')}

            **Review Points**:
            - [ ] ESLint passes
            - [ ] Tests updated/added
            - [ ] No console.log statements
            - [ ] Error handling included
            - [ ] Performance considered
            `;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: body
            });
```

### Step 7: Set Up Claude Code Action for AI-Powered PR Comments

Integrate Claude AI for intelligent code review and automated responses:

**Prerequisites:**
- Anthropic API key (stored in repository secrets as `ANTHROPIC_API_KEY`)
- OR AWS Bedrock / Google Vertex AI configured

**Create `.github/workflows/claude-pr-review.yml`:**

```yaml
name: Claude Code PR Review

on:
  pull_request:
    types: [opened, synchronize]
  issue_comment:
    types: [created]

permissions:
  contents: write
  pull-requests: write
  issues: write

jobs:
  claude-review:
    runs-on: ubuntu-latest
    # Only run when PR is opened or @claude is mentioned
    if: |
      github.event_name == 'pull_request' ||
      (github.event_name == 'issue_comment' &&
       contains(github.event.comment.body, '@claude'))

    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Review this pull request and provide:
            1. Summary of changes
            2. Potential issues or bugs
            3. Security concerns
            4. Performance considerations
            5. Code quality suggestions
          claude_args: |
            --model claude-sonnet-4-5-20250929
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Advanced Claude integration with security review:**

```yaml
name: Claude Security Review

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  contents: write
  pull-requests: write

jobs:
  security-review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          prompt: |
            Perform a security review of this pull request using OWASP guidelines.
            Focus on:
            - SQL injection vulnerabilities
            - XSS attack vectors
            - Authentication/authorization issues
            - Sensitive data exposure
            - Insecure dependencies

            Provide findings as a commented list with severity ratings.
          claude_args: |
            --model claude-sonnet-4-5-20250929
            --timeout 300
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**How Claude Code Action works:**
1. Analyzes PR context (files changed, commits, description)
2. Processes your prompt against the codebase
3. Generates intelligent, context-aware comments
4. Posts findings directly to the PR
5. Can make automated fixes if prompted

### Step 8: Add Deployment Preview Comments

Automatically comment with deployment URLs:

```yaml
name: Deploy and Comment

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  pull-requests: write

jobs:
  deploy-preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to preview environment
        id: deploy
        run: |
          # Your deployment script here
          PREVIEW_URL="https://pr-${{ github.event.number }}.preview.yourapp.com"
          echo "url=$PREVIEW_URL" >> $GITHUB_OUTPUT

      - name: Comment deployment URL
        uses: thollander/actions-comment-pull-request@v3
        with:
          message: |
            ## 🚀 Preview Deployment Ready!

            Your changes have been deployed to a preview environment:

            **Preview URL**: ${{ steps.deploy.outputs.url }}

            **Test Credentials**:
            - Username: `demo`
            - Password: `demo123`

            ### What to test:
            - [ ] Visual changes render correctly
            - [ ] Functionality works as expected
            - [ ] No console errors
            - [ ] Mobile responsiveness

            Preview will be updated automatically with new commits.

            ---
            *Deployed from commit ${{ github.sha }}*
          comment-tag: 'preview-deployment'
          reactions: 'rocket'
```

### Step 9: Handle Comments from Forked PRs

Fork PRs require special handling due to security restrictions:

```yaml
name: Fork PR Comments

on:
  pull_request_target:  # Note: pull_request_target, not pull_request
    types: [opened, synchronize]

permissions:
  pull-requests: write

jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      # DO NOT checkout fork code with pull_request_target!
      # Only use for commenting, not running untrusted code

      - name: Comment on fork PR
        uses: actions/github-script@v8
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const isFork = context.payload.pull_request.head.repo.fork;

            if (isFork) {
              const body = `
              ## 🔒 Fork PR Detected

              Thank you for your contribution from a forked repository!

              **Security Notice**: Some automated checks are limited for fork PRs.
              A maintainer will review and approve workflow runs.

              **For contributors**:
              - Ensure your fork is up to date with main
              - Follow contribution guidelines
              - Tests should pass in your fork before submitting

              [Contribution Guidelines](https://github.com/${{ github.repository }}/blob/main/CONTRIBUTING.md)
              `;

              await github.rest.issues.createComment({
                issue_number: context.issue.number,
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: body
              });
            }
```

**Security considerations:**
- Use `pull_request_target` to grant write permissions
- Never checkout or run code from the PR with `pull_request_target`
- Only use for safe operations like commenting
- Validate all inputs from PR content

### Step 10: Create Reusable Comment Workflow

Build a composite action for reuse across repositories:

**Create `.github/actions/pr-comment/action.yml`:**

```yaml
name: 'PR Comment'
description: 'Post or update a comment on a pull request'
inputs:
  message:
    description: 'Comment message'
    required: true
  tag:
    description: 'Unique tag for updating comments'
    required: false
    default: 'auto-comment'
  github-token:
    description: 'GitHub token'
    required: true

runs:
  using: 'composite'
  steps:
    - name: Post or update comment
      uses: actions/github-script@v8
      with:
        github-token: ${{ inputs.github-token }}
        script: |
          const marker = `<!-- ${{ inputs.tag }} -->`;
          const body = `${marker}\n${{ inputs.message }}`;

          const { data: comments } = await github.rest.issues.listComments({
            owner: context.repo.owner,
            repo: context.repo.repo,
            issue_number: context.issue.number,
          });

          const existing = comments.find(c => c.body.includes(marker));

          if (existing) {
            await github.rest.issues.updateComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              comment_id: existing.id,
              body: body
            });
          } else {
            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: body
            });
          }
```

**Use the composite action:**

```yaml
name: Use Custom PR Comment

on:
  pull_request:
    types: [opened]

permissions:
  pull-requests: write

jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Add PR comment
        uses: ./.github/actions/pr-comment
        with:
          message: |
            ## Custom Comment
            This uses our reusable comment action!
          tag: 'custom-tag'
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Expected Results

After implementing PR comment automation, you should observe:

### Automatic Comments Appear
- Comments posted immediately when PR events trigger
- Consistent formatting and messaging
- Relevant information included (test results, deployment URLs, etc.)
- Proper markdown rendering

### Comment Updates Work
- Single comment updated instead of multiple comments
- Previous comment found and replaced when using tags
- No spam or duplicate comments
- Clean PR conversation thread

### Workflow Integration
- Comments reflect current build/test status
- Links to workflow runs work correctly
- Deployment preview URLs are accessible
- All permissions properly configured

### For Claude Code Integration
- AI-powered reviews appear as comments
- Security findings properly formatted
- Code suggestions are actionable
- @claude mentions trigger responses

### Example Comment Output

```markdown
## 🚀 PR Summary

**Author**: @developer
**Branch**: `feature/add-login` → `main`
**Commit**: abc123def456

### ✅ CI Status
- Tests: 45/45 passed
- Coverage: 87%
- Lint: Passed
- Build: Successful

**Preview**: https://pr-123.preview.myapp.com

---
*Automated comment by GitHub Actions*
```

## Troubleshooting

### Common Issue 1
**Problem**: Workflow runs but no comment appears on PR
**Solution**:
- Verify `pull-requests: write` permission is set in workflow
- Check that workflow completed successfully in Actions tab
- Ensure PR number is correctly referenced (`context.issue.number`)
- Verify `GITHUB_TOKEN` has necessary permissions
- Check if repository requires approval for Actions (Settings → Actions)
- Review workflow logs for API errors
- For fork PRs, use `pull_request_target` event instead

### Common Issue 2
**Problem**: Multiple comments created instead of updating existing one
**Solution**:
- Ensure comment marker/tag is unique and consistent
- Check that marker HTML comment is included in comment body
- Verify search logic correctly finds existing comment
- Use `comment-tag` parameter with third-party actions
- Test marker with: `comments.find(c => c.body.includes(marker))`
- Ensure workflow uses same tag across runs
- Check for typos in tag names between create and update

### Common Issue 3
**Problem**: Comments work for direct PRs but not forks
**Solution**:
- Use `pull_request_target` event for fork PRs
- Never checkout fork code with `pull_request_target` (security risk)
- Limit fork workflows to safe operations only
- Consider requiring approval for fork PR workflows
- Check "Require approval for all outside collaborators" setting
- Document fork PR limitations for contributors
- Use separate workflows for fork vs. non-fork PRs

### Common Issue 4
**Problem**: Claude Code Action fails or doesn't respond
**Solution**:
- Verify `ANTHROPIC_API_KEY` secret is set correctly
- Check API key has sufficient credits/quota
- Ensure workflow has `contents: write` and `pull-requests: write` permissions
- Verify model name is correct: `claude-sonnet-4-5-20250929`
- Check Claude Code Action version is up to date
- Review action logs for specific error messages
- Test API key manually: `curl https://api.anthropic.com/v1/models`
- Consider timeout settings if PRs are very large

### Common Issue 5
**Problem**: Comment formatting looks broken or markdown doesn't render
**Solution**:
- Ensure message is properly quoted in YAML
- Use pipe `|` or `>` for multi-line strings
- Escape special characters in dynamic content
- Test markdown locally before deploying
- Check for unclosed code blocks or lists
- Verify table syntax is correct
- Use backticks for inline code: \`code\`
- Preview comment in GitHub markdown preview tool

### Common Issue 6
**Problem**: Workflow triggers too often and creates spam
**Solution**:
- Limit trigger events: Use specific `types: [opened, synchronize]`
- Add conditional checks: `if: github.event_name == 'pull_request'`
- Use path filters to trigger only on relevant file changes
- Implement rate limiting or debouncing
- Use comment updates instead of new comments
- Add `concurrency` group to cancel redundant runs:
  ```yaml
  concurrency:
    group: pr-comment-${{ github.event.pull_request.number }}
    cancel-in-progress: true
  ```

### Common Issue 7
**Problem**: Dynamic content (test results, coverage) not appearing
**Solution**:
- Ensure previous steps output values correctly
- Use `echo "key=value" >> $GITHUB_OUTPUT` syntax
- Reference outputs as `${{ steps.step-id.outputs.key }}`
- Check that step IDs match in reference
- Verify JSON parsing commands (jq, etc.) work
- Test data extraction locally before workflow
- Add debugging: Print values before using them
- Check file paths are correct for reading results

## Additional Tips

### Performance Optimization

**Cache Dependencies**:
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

**Parallel Jobs**:
```yaml
jobs:
  test:
    # ... run tests

  comment:
    needs: test
    # ... post results
```

**Minimize API Calls**:
- Batch multiple comments into one
- Update existing comments instead of creating new
- Use conditional logic to skip unnecessary comments

### Security Best Practices

**Never Log Secrets**:
```yaml
# BAD
- run: echo "Token: ${{ secrets.GITHUB_TOKEN }}"

# GOOD
- run: echo "Token is set: ${{ secrets.GITHUB_TOKEN != '' }}"
```

**Validate External Input**:
```yaml
- name: Validate PR title
  run: |
    if [[ ! "${{ github.event.pull_request.title }}" =~ ^[a-zA-Z0-9\ \-]+$ ]]; then
      echo "Invalid PR title format"
      exit 1
    fi
```

**Use Least Privilege**:
```yaml
permissions:
  pull-requests: write  # Only what you need
  # Don't use: write-all
```

### Advanced Comment Formatting

**Collapsible Sections**:
```markdown
<details>
<summary>📊 Detailed Test Results</summary>

\`\`\`
Test Suite: Authentication
✓ Login successful
✓ Logout successful
✓ Token refresh works
\`\`\`

</details>
```

**Tables**:
```markdown
| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Bundle Size | 234 KB | 189 KB | -19% ⬇️ |
| Load Time | 2.3s | 1.8s | -22% ⬇️ |
```

**Progress Indicators**:
```markdown
### Deployment Progress
- [x] Build completed
- [x] Tests passed
- [ ] Deploying to staging
- [ ] Smoke tests
- [ ] Production ready
```

### Integration Patterns

**Link to External Tools**:
```markdown
📊 **Code Quality Reports**
- [SonarQube Analysis](https://sonarqube.example.com/project/pr-${{ github.event.number }})
- [Lighthouse Score](https://lighthouse.example.com/pr-${{ github.event.number }})
- [Bundle Analyzer](https://bundle.example.com/pr-${{ github.event.number }})
```

**Conditional Warnings**:
```yaml
- name: Check breaking changes
  run: |
    if grep -r "BREAKING CHANGE" CHANGELOG.md; then
      echo "has_breaking=true" >> $GITHUB_OUTPUT
    fi

- name: Comment if breaking
  if: steps.check.outputs.has_breaking == 'true'
  uses: actions/github-script@v8
  # ... post breaking change warning
```

**Team Mentions**:
```markdown
⚠️ **Database Schema Change Detected**

This PR modifies database migrations. Please review carefully.

CC: @database-team
```

### Workflow Templates

**Complete test and deploy workflow**:
```yaml
name: Test, Deploy, and Report

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  pull-requests: write
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    outputs:
      test-status: ${{ steps.test.outcome }}
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - id: test
        run: npm test

  deploy:
    needs: test
    if: success()
    runs-on: ubuntu-latest
    outputs:
      preview-url: ${{ steps.deploy.outputs.url }}
    steps:
      - id: deploy
        run: echo "url=https://preview.example.com" >> $GITHUB_OUTPUT

  comment:
    needs: [test, deploy]
    if: always()
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v8
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const testStatus = '${{ needs.test.outputs.test-status }}';
            const previewUrl = '${{ needs.deploy.outputs.preview-url }}';

            const body = testStatus === 'success'
              ? `✅ Tests passed! Preview: ${previewUrl}`
              : `❌ Tests failed. Check logs for details.`;

            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: body
            });
```

## Related Articles
- KB-033: How to create a pull request automatically from completed work
- KB-034: How to review PR change summaries generated by Claude
- KB-035: How to customize PR descriptions and titles
- KB-047: How to understand credential access in Claude Code Web
- KB-056: How to perform code reviews with Claude Code

## Sources
- GitHub Script Action - https://github.com/actions/github-script (Accessed: November 16, 2025)
- Comment Pull Request Action - https://github.com/marketplace/actions/comment-pull-request (Accessed: November 16, 2025)
- Claude Code Action - https://github.com/anthropics/claude-code-action (Accessed: November 16, 2025)
- GitHub REST API - Issues - https://docs.github.com/en/rest/issues/comments (Accessed: November 16, 2025)
- Working with Comments - https://docs.github.com/en/rest/guides/working-with-comments (Accessed: November 16, 2025)
- Automate Security Reviews with Claude Code - https://www.anthropic.com/news/automate-security-reviews-with-claude-code (Accessed: November 16, 2025)
- Scaling Claude Code with GitHub Actions - https://medium.com/@waprin/scaling-claude-code-with-github-actions-and-pull-requests-1dd8ce46e465 (Accessed: November 16, 2025)
- GitHub Actions Context - https://docs.github.com/en/actions/learn-github-actions/contexts (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
