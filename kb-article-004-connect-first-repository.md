# How to Connect Your First GitHub Repository to Claude Code Web

**Article ID**: KB-004
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 3-5 minutes

## Overview
Once you've authenticated with GitHub, you can connect repositories to Claude Code Web. This guide shows you how to select and connect your first repository so Claude can start helping with your code.

## Prerequisites
- An active Claude Pro or Max subscription
- Access to Claude Code Web at claude.com/code
- GitHub account authenticated with Claude Code Web (see KB-003)
- At least one repository in your GitHub account
- Appropriate permissions for the repository you want to connect

## Steps

### Step 1: Access the Repository Selection Interface

1. Navigate to `https://claude.com/code`
2. Ensure you're logged in with your authenticated GitHub account
3. Look for one of these options:
   - "Connect Repository" button
   - "Add Repository" option
   - A repository browser/selector interface
   - "Get Started" prompt if this is your first time

### Step 2: Browse Your Available Repositories

You'll see a list or search interface showing your GitHub repositories:

**What You'll See:**
- Repository names
- Repository owners (your username or organization names)
- Privacy status (public or private)
- Last updated information
- Brief repository descriptions

**Repository Visibility:**
- Personal repositories you own
- Organization repositories (if your org approved Claude Code)
- Repositories you have at least read access to

### Step 3: Select a Repository

Choose your first repository wisely:

**Recommended for First-Time Users:**
- Start with a test or non-critical repository
- Choose a smaller project to familiarize yourself with the workflow
- Avoid production or mission-critical repositories initially
- Select a repository you have write permissions for

**How to Select:**
1. Browse or search for the repository name
2. Click on the repository to select it
3. Review the repository information displayed
4. Confirm this is the correct repository

### Step 4: Review Repository Connection Settings

Before connecting, you may see options or confirmations:

**Branch Information:**
- The default branch (usually `main` or `master`) will be shown
- Claude Code will be able to create new branches from this base

**Permissions Confirmation:**
- Read access to view code
- Write access to create branches and commits
- Ability to create pull requests

**Security Notice:**
- The repository will be accessible in isolated sandbox environments
- Sensitive credentials are never directly exposed to Claude
- Each session runs in a separate, secure container

### Step 5: Connect the Repository

1. Click "Connect," "Add Repository," or similar confirmation button
2. Wait for the connection to establish (usually takes a few seconds)
3. You may see a loading indicator or progress message

### Step 6: Verify Successful Connection

After connection, you should see:
- A confirmation message that the repository is connected
- The repository appears in your "Connected Repositories" list
- Access to the repository interface showing:
  - Current branch information
  - Recent commits
  - File structure preview
- The ability to create a new coding task for this repository

## Expected Results

After successfully connecting your repository, you should:
- See the repository in your connected repositories list
- Have the ability to start a new Claude Code session for this repository
- View the repository's file structure and branch information
- See options to create coding tasks
- Be able to describe what you want Claude to do with this repository
- Have the repository remain connected for future sessions (no need to reconnect each time)

## Troubleshooting

### Common Issue 1
**Problem**: Can't find a specific repository in the list
**Solution**:
- Use the search function to find the repository by name
- Verify the repository exists in your GitHub account
- Check if you selected "All repositories" or "Selected repositories" during GitHub authentication
- If using selective access, add the repository in GitHub Settings → Applications → Claude Code
- Ensure the repository isn't archived in GitHub

### Common Issue 2
**Problem**: Repository shows but can't connect it
**Solution**:
- Verify you have at least read access to the repository
- For organization repositories, ensure your org admin approved Claude Code
- Check if the repository is empty (Claude Code may require at least one commit)
- Try refreshing the page and attempting to connect again
- Verify your GitHub authentication is still valid

### Common Issue 3
**Problem**: Connection fails with an error message
**Solution**:
- Check your internet connection
- Verify the repository isn't too large (there may be size limits)
- Ensure the repository has a valid default branch
- Log out and log back in to refresh your session
- Re-authenticate your GitHub connection if needed

### Common Issue 4
**Problem**: Connected the wrong repository by mistake
**Solution**:
- Look for a "Disconnect" or "Remove" option next to the repository
- You can safely disconnect and connect a different repository
- Disconnecting doesn't affect your GitHub repository in any way
- See KB-012 for detailed disconnection instructions

### Common Issue 5
**Problem**: Organization repository requires additional approval
**Solution**:
- Contact your GitHub organization administrator
- Ask them to approve the Claude Code application for your organization
- They can do this in: Organization Settings → Third-party access → Claude Code
- Once approved, refresh your repository list

## Additional Tips
- You can connect multiple repositories to Claude Code Web (work on different projects)
- Connected repositories remain accessible across sessions—no need to reconnect
- Start with simpler repositories to learn the workflow before connecting critical projects
- You can disconnect and reconnect repositories anytime without losing data
- Claude Code doesn't modify your repository until you explicitly run coding tasks
- Consider creating a dedicated test repository to experiment with Claude Code features
- Connection is instantaneous once set up—no waiting for syncs or clones

## Related Articles
- KB-003: How to authenticate your GitHub account with Claude Code Web
- KB-006: How to create your first coding task in Claude Code Web
- KB-011: How to switch between different connected repositories
- KB-012: How to disconnect a repository from Claude Code Web
- KB-013: How to view the file structure of your connected repository

## Sources
1. Claude Code Web Repository Connection Guide - Search: "Claude Code Web connect repository tutorial" (Accessed: November 16, 2025)
2. GitHub Repository Permissions Documentation - https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features (Accessed: November 16, 2025)
3. Claude Code Web Getting Started - Search: "claude code web first repository setup" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
