# How to use the secure proxy for authentication

**Article ID**: KB-046
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Claude Code Web uses a secure proxy architecture to manage all GitHub authentication and git operations. This proxy validates credentials and operations before connecting to GitHub, ensuring your sensitive authentication tokens never enter the sandbox environment. Understanding how to work with this secure proxy enables you to safely perform git operations, connect to repositories, and configure advanced GitHub integrations.

## Prerequisites
- Active Claude Pro, Max, Team, or Enterprise account (with premium seat)
- GitHub account with repositories you want to connect
- Admin access to GitHub organizations (if connecting organization repositories)
- Basic understanding of git and GitHub operations

## Steps

### Step 1: Connect to GitHub via the Claude for GitHub App

Claude Code Web requires the Claude for GitHub app to authenticate securely through the proxy.

1. Visit **claude.com/code** in your browser
2. Click **"Connect GitHub"** or **"Connect your first repository"**
3. You'll be redirected to GitHub to authorize the Claude for GitHub app
4. Select the GitHub account or organization you want to connect
5. Choose repository access:
   - **All repositories** - Grants access to all current and future repos
   - **Only select repositories** - Choose specific repos to authorize

The secure proxy now has permission to validate and forward your git operations to GitHub.

**How it works behind the scenes**: Inside the sandbox, git operations use a custom-built scoped credential. The external proxy verifies this credential and the contents of each git interaction (ensuring it only pushes to configured branches), then attaches the real authentication token before sending the request to GitHub.

### Step 2: Verify Repository Access

After connecting, verify your repositories are accessible:

1. Return to **claude.com/code**
2. Create a new task or session
3. You should see your connected repositories available for selection
4. Select a repository to work with

**Troubleshooting missing repositories**: If you don't see expected repositories, visit `https://github.com/apps/claude-for-github/installations/select_target` and verify:
- The correct GitHub account/organization is selected
- Repository permissions are properly configured
- You have admin access (or have requested access from admins)

### Step 3: Understand Proxy Security Restrictions

The secure proxy enforces several protective measures you should be aware of:

**Branch protection**: Git push operations are restricted to the current working branch only. You cannot push to arbitrary branches for safety.

**Credential isolation**: Your actual GitHub authentication tokens remain outside the sandbox. Even if code execution is compromised, attackers cannot access real credentials.

**Operation validation**: The proxy inspects each git operation to ensure:
- Valid scoped credentials are presented
- Operations match authorized actions
- Target branches are permitted
- Request contents are safe

### Step 4: Configure Advanced Authentication (GitHub CLI)

For operations beyond basic git commands (like creating issues, PRs, or repository management), you'll need to configure GitHub CLI authentication.

**Important**: The web version cannot use interactive authentication (`gh auth login`). You must use environment variables.

1. Create a GitHub Personal Access Token:
   - Go to GitHub **Settings > Developer settings > Personal access tokens > Tokens (classic)**
   - Click **"Generate new token (classic)"**
   - Select required scopes:
     - `repo` - Full control of private repositories
     - `write:org` - For organization operations (if needed)
     - `workflow` - For GitHub Actions (if needed)
   - Generate and copy the token

2. Add the token to your Claude Code Web task:
   - When creating a new task, click **"Environment Variables"** or **"Advanced Settings"**
   - Add a new environment variable:
     - **Name**: `GITHUB_PERSONAL_ACCESS_TOKEN`
     - **Value**: Your token (paste the token you copied)

3. In your task initialization or session start hook, export the variable:
```bash
export GITHUB_TOKEN=$GITHUB_PERSONAL_ACCESS_TOKEN
```

4. Verify authentication:
```bash
gh auth status
```

### Step 5: Work with the Proxy in Your Workflow

Once configured, the secure proxy works transparently for standard git operations:

**Standard git commands work automatically:**
```bash
# These commands automatically use the secure proxy
git status
git add .
git commit -m "Your commit message"
git push origin main
git pull
git fetch
```

**GitHub CLI commands use your configured token:**
```bash
# Create a pull request
gh pr create --title "Feature X" --body "Description"

# List issues
gh issue list

# Add a comment to an issue
gh issue comment 123 --body "Comment text"
```

**What the proxy does for you:**
1. Intercepts all git/GitHub operations
2. Validates your scoped credential
3. Checks operation permissions
4. Attaches the real authentication token
5. Forwards the request to GitHub
6. Returns the response to your sandbox

### Step 6: Handle Private Repository Access

For private repositories or organization repositories:

1. **Personal private repos**: Automatically accessible once you authorize the Claude for GitHub app

2. **Organization private repos**:
   - If you're an admin: Go to the organization settings and approve the Claude for GitHub app
   - If you're not an admin: Request access from your organization administrators
   - Admins can grant access at: `https://github.com/organizations/[org-name]/settings/installations`

3. **Verify access**:
```bash
# Check if you can access the repository
git remote -v
git fetch
```

## Expected Results

When the secure proxy is properly configured, you should experience:

- **Seamless git operations**: All standard git commands work without manual authentication
- **Automatic credential management**: No need to manually input passwords or tokens for git operations
- **Branch-restricted pushes**: Attempts to push to unauthorized branches are blocked by the proxy
- **GitHub CLI functionality**: Advanced GitHub operations work using your personal access token
- **Secure isolation**: Authentication tokens never appear in sandbox logs or environment
- **Repository visibility**: Connected repositories appear in your task creation interface

**Success indicators:**
- `git push` completes without prompting for credentials
- `gh auth status` shows you're logged in
- No authentication errors in git operations
- Repository files are accessible and editable

## Troubleshooting

### Common Issue 1
**Problem**: Repositories don't appear when creating a new task
**Solution**:
- Visit `https://github.com/apps/claude-for-github/installations/select_target`
- Select your GitHub account/organization
- Ensure repositories are selected (either "All repositories" or specific repos)
- If it's an organization, verify you have admin permissions or request access from admins
- Refresh the Claude Code Web page and try again

### Common Issue 2
**Problem**: Git push fails with authentication error
**Solution**:
- Verify the Claude for GitHub app still has repository access
- Check you're pushing to the current working branch (cross-branch pushes are restricted)
- Ensure you haven't revoked the app's permissions in GitHub
- Try reconnecting: Disconnect and reconnect the GitHub integration

### Common Issue 3
**Problem**: GitHub CLI commands fail with "Not authenticated"
**Solution**:
- Verify `GITHUB_PERSONAL_ACCESS_TOKEN` environment variable is set correctly
- Check the token has required scopes (repo, workflow, etc.)
- Ensure the token hasn't expired
- Export the token: `export GITHUB_TOKEN=$GITHUB_PERSONAL_ACCESS_TOKEN`
- Test with: `gh auth status`

### Common Issue 4
**Problem**: "Permission denied" when accessing organization repositories
**Solution**:
- Contact your organization administrator
- Request they approve the Claude for GitHub app for your organization
- Provide them the app URL: `https://github.com/apps/claude-for-github`
- After approval, refresh repository connections in Claude Code Web

## Additional Tips

- **Token security**: Never commit your `GITHUB_PERSONAL_ACCESS_TOKEN` to code. Always use environment variables.
- **Minimum permissions**: Grant your personal access token only the scopes you need for your specific tasks.
- **Token rotation**: Regularly regenerate personal access tokens for security (every 30-90 days recommended).
- **Branch awareness**: Remember that the secure proxy restricts pushes to your current branch. Plan your workflow accordingly.
- **Audit access**: Regularly review which repositories the Claude for GitHub app can access at `https://github.com/settings/installations`.
- **Organization policies**: Check with your organization about policies for third-party app integrations before connecting work repositories.
- **Session hooks**: For GitHub CLI operations, consider adding token export to your session start hook for automatic configuration.
- **Network restrictions**: The proxy handles all external GitHub connections - your sandbox is network-isolated except through this proxy.

## Related Articles
- KB-001: Getting started with Claude Code Web
- KB-015: Working with repositories
- KB-020: Understanding the sandbox environment
- KB-025: Session management and environment configuration

## Sources
- Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Claude Code on the web - https://www.anthropic.com/news/claude-code-on-the-web (Accessed: November 16, 2025)
- Corporate proxy configuration - https://docs.claude.com/en/docs/claude-code/corporate-proxy (Accessed: November 16, 2025)
- Enterprise network configuration - https://docs.claude.com/en/docs/claude-code/network-config (Accessed: November 16, 2025)
- Using the GitHub Integration - https://support.claude.com/en/articles/10167454-using-the-github-integration (Accessed: November 16, 2025)
- Google Search Query: "Claude Code Web secure proxy authentication 2025"

---
*This article is part of the Claude Code Web knowledge base*
