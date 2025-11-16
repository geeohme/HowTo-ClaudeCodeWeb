# How to Authenticate Your GitHub Account with Claude Code Web

**Article ID**: KB-003
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 3-5 minutes

## Overview
Claude Code Web works exclusively with GitHub repositories, so you need to authenticate your GitHub account before you can start coding. This guide walks you through the GitHub OAuth authentication process.

## Prerequisites
- An active Claude Pro or Max subscription
- Access to Claude Code Web at claude.com/code
- A GitHub account (create one at github.com if you don't have one)
- Permission to authorize third-party applications in your GitHub account

## Steps

### Step 1: Access Claude Code Web

1. Navigate to `https://claude.com/code`
2. Ensure you're logged into your Claude account with an active Pro or Max subscription
3. You should see the Claude Code Web interface

### Step 2: Initiate GitHub Authentication

When you first access Claude Code Web or try to connect a repository, you'll see a prompt to connect your GitHub account:

1. Look for a button labeled "Connect GitHub" or "Authenticate with GitHub"
2. Click the authentication button
3. You'll be redirected to GitHub's authorization page

### Step 3: Review GitHub Permissions

On the GitHub authorization page, you'll see:

**Requested Permissions:**
- Read access to your repositories (to view code)
- Write access to repositories (to make commits and create branches)
- Read/write access to pull requests (to create PRs)
- Access to your email address (for git commit attribution)

**What Claude Code Web Can Do:**
- Read repository contents
- Create new branches
- Commit code changes
- Create pull requests
- Access repository metadata

**What Claude Code Web Cannot Do:**
- Delete repositories
- Change repository settings
- Access repositories you don't explicitly connect
- Modify your GitHub account settings

### Step 4: Authorize the Application

1. Review the permissions carefully
2. If you agree, click "Authorize Anthropic" or "Authorize Claude"
3. You may be prompted to enter your GitHub password to confirm
4. If you have two-factor authentication (2FA) enabled, complete the 2FA challenge

### Step 5: Grant Access to Repositories

After authorization, you may see an additional screen to select which repositories Claude Code can access:

**Option 1: All Repositories**
- Grants access to all current and future repositories
- Most convenient for extensive use
- Recommended for personal accounts

**Option 2: Select Repositories**
- Grants access only to specific repositories you choose
- More restrictive and secure
- Recommended for organization accounts or sensitive projects

Select your preference and click "Install" or "Save"

### Step 6: Verify Authentication

You'll be redirected back to Claude Code Web. You should now see:
- A confirmation message that GitHub is connected
- Your GitHub username displayed in the interface
- The ability to browse and select your repositories
- A "Connected" or similar status indicator

## Expected Results

After successful authentication, you should:
- See your GitHub account linked to Claude Code Web
- Have the ability to browse your accessible repositories
- See a list of repositories you can connect to Claude Code
- Be able to proceed with connecting your first repository
- See your GitHub avatar or username in the Claude Code Web interface

## Troubleshooting

### Common Issue 1
**Problem**: GitHub authorization fails or returns an error
**Solution**:
- Ensure you're logged into the correct GitHub account
- Check if your GitHub account has any restrictions on third-party app access
- Verify your internet connection is stable during the OAuth flow
- Try again in a private/incognito browser window
- Clear browser cookies and cache, then retry

### Common Issue 2
**Problem**: Don't see option to connect GitHub
**Solution**:
- Verify you have an active Claude Pro or Max subscription
- Refresh the page at claude.com/code
- Log out of Claude and log back in
- Check if you're already connected (look for a connected status indicator)

### Common Issue 3
**Problem**: Can't see certain repositories after authentication
**Solution**:
- Check if you selected "All repositories" or "Select repositories" during setup
- If you chose selective access, go to GitHub Settings → Applications → Claude Code and add the missing repositories
- Verify you have at least read access to those repositories in GitHub
- For organization repositories, ensure the organization has approved the Claude Code application

### Common Issue 4
**Problem**: Organization repositories aren't showing up
**Solution**:
- Organization owners must approve third-party applications
- Contact your organization administrator to approve Claude Code
- Go to: GitHub Organization Settings → Third-party access → Grant access to Claude Code
- You may need organization owner permissions to connect org repositories

### Common Issue 5
**Problem**: Want to revoke or re-authenticate GitHub access
**Solution**:
- In Claude Code Web, look for account or settings menu
- Find "Disconnect GitHub" or similar option
- Alternatively, go to GitHub Settings → Applications → Authorized OAuth Apps
- Find "Claude Code" and click "Revoke"
- Return to claude.com/code and re-authenticate

## Additional Tips
- You only need to authenticate once; the connection persists across sessions
- You can manage which repositories Claude Code can access anytime from GitHub Settings → Applications
- For added security, regularly review authorized applications in your GitHub settings
- If you work with multiple GitHub accounts, make sure you authenticate with the correct one
- Organization repositories require additional approval from organization admins
- Revoking access is instant and can be done anytime from GitHub settings

## Related Articles
- KB-001: How to access Claude Code Web at claude.com/code
- KB-002: How to sign up for a Claude Pro or Max account
- KB-004: How to connect your first GitHub repository to Claude Code Web

## Sources
1. GitHub OAuth Documentation - https://docs.github.com/en/apps/oauth-apps/using-oauth-apps/authorizing-oauth-apps (Accessed: November 16, 2025)
2. Claude Code Web Setup Guide - Search: "Claude Code Web GitHub authentication setup" (Accessed: November 16, 2025)
3. Anthropic Claude Code Web Documentation - Search: "claude code web connect github repository permissions" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
