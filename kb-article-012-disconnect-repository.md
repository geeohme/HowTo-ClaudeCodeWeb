# How to Disconnect a Repository from Claude Code Web

**Article ID**: KB-012
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 3 minutes

## Overview
If you no longer need Claude Code Web to access a specific repository, you can disconnect it. This guide shows you how to safely remove a repository connection while understanding the implications.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one connected repository
- Understanding of what disconnection means for your workflow

## What Happens When You Disconnect

### Effects of Disconnecting

**✓ Repository is removed from Claude Code Web:**
- No longer appears in your repository list
- Cannot create new tasks for this repository
- Existing sessions for this repo will be terminated

**✓ Your GitHub repository is unchanged:**
- All code remains intact on GitHub
- Branches created by Claude still exist
- Commits made by Claude are preserved
- No data is deleted from GitHub

**✓ You can reconnect anytime:**
- Disconnection is reversible
- Simply reconnect the repository when needed
- No data loss or permanent changes

## Steps to Disconnect

### Step 1: Navigate to Repository Settings

**Option 1: From Repository View**
1. Go to `https://claude.com/code`
2. Select the repository you want to disconnect
3. Look for Settings, Options, or a three-dot menu (⋮)

**Option 2: From Settings/Preferences**
1. Access Claude Code Web settings
2. Navigate to "Connected Repositories" or similar section
3. Find the repository you want to disconnect

### Step 2: Locate the Disconnect Option

Look for one of these controls:

**Common Labels:**
- "Disconnect Repository"
- "Remove Repository"
- "Unlink Repository"
- "Delete Connection"
- Red "Disconnect" or "Remove" button

**Common Locations:**
- Repository settings menu
- Three-dot menu (⋮) next to repository name
- Repository card footer or options
- Settings page with list of repositories

### Step 3: Initiate Disconnection

1. Click the "Disconnect" or "Remove" button
2. A confirmation dialog should appear

**Confirmation Dialog Typically Shows:**
- Repository name (verify it's correct!)
- Warning about active sessions
- Information about what will happen
- Reminder that GitHub repository is unaffected

### Step 4: Confirm Disconnection

**Review the Warning:**
- Ensure you're disconnecting the correct repository
- Note any active sessions that will be terminated
- Understand the implications

**Confirmation Options:**
- **"Disconnect"** or **"Yes, Remove"** - Proceeds with disconnection
- **"Cancel"** or **"Keep Connected"** - Aborts the operation

Click "Disconnect" or "Yes" to proceed.

### Step 5: Verify Disconnection

After confirming, you should see:
- Repository removed from your list
- Confirmation message
- Repository no longer accessible in Claude Code Web
- Any active sessions for that repo terminated

## Expected Results

After disconnecting a repository, you should:
- No longer see the repository in your connected repositories list
- Be unable to create new tasks for that repository
- See any running sessions for that repo terminated
- Still have the repository intact on GitHub
- Be able to reconnect the repository anytime
- Have freed up space in your repository connections (if there's a limit)

## Troubleshooting

### Common Issue 1
**Problem**: Can't find the disconnect option
**Solution**:
- Look for a settings or gear icon near the repository name
- Try right-clicking on the repository (context menu)
- Check the main settings/preferences page
- Look for a three-dot menu (⋮) on the repository card
- Some interfaces may call it "Remove" instead of "Disconnect"

### Common Issue 2
**Problem**: Disconnect button is grayed out or disabled
**Solution**:
- Cancel any running tasks for this repository first
- Complete or cancel all active sessions
- Wait for pending operations to finish
- Refresh the page and try again

### Common Issue 3
**Problem**: Accidentally disconnected the wrong repository
**Solution**:
- Don't panic - no data was deleted
- Immediately reconnect the repository (see KB-004)
- All code and history remain intact on GitHub
- Reconnection is quick and easy

### Common Issue 4
**Problem**: Want to disconnect but have important running tasks
**Solution**:
- Wait for tasks to complete before disconnecting
- Or cancel the tasks if they're no longer needed
- Review and save any important work in progress
- You cannot disconnect while tasks are actively running

## When to Disconnect a Repository

### Good Reasons to Disconnect

**✓ Project Completed:**
- Work on this repository is finished
- No longer need Claude's assistance with it

**✓ Workspace Cleanup:**
- Too many repositories cluttering your list
- Want to focus on active projects only

**✓ Temporary Access:**
- Connected for a one-time task
- Was a test or experimental repository

**✓ Permission Changes:**
- Lost access to the repository on GitHub
- Repository was deleted or archived
- Organization revoked access

**✓ Security Considerations:**
- Repository contains sensitive data
- Want to limit which repos have Claude Code access

### Reasons NOT to Disconnect

**✗ Still actively developing:**
- Regular work continues on this repository
- Will need Claude's help again soon

**✗ Just to free concurrent tasks:**
- Disconnection doesn't affect concurrent task limits
- Cancel specific tasks instead

**✗ Temporary break:**
- Taking a short break from the project
- Easier to leave connected and ignore it

## Additional Tips

### Before Disconnecting
- Complete all active sessions
- Review any branches created by Claude
- Ensure all work is committed and pushed
- Note any important context about Claude's work on this repo

### After Disconnecting
- The repository still exists normally on GitHub
- All branches Claude created remain available
- All commits are preserved
- You can review Claude's work directly on GitHub

### Reconnecting Later
- Simply follow the connection process again (KB-004)
- All previous work by Claude is still visible on GitHub
- No history is lost by disconnecting and reconnecting

### Managing Multiple Repositories
- Keep only actively-used repositories connected
- Disconnect completed projects to reduce clutter
- Reconnect as needed—it's a quick process
- No limit to how many times you can connect/disconnect

### Repository Permissions
```
Note: Disconnecting from Claude Code Web does NOT revoke
Claude's OAuth permissions on GitHub. To fully revoke access:

1. Disconnect from Claude Code Web
2. Go to GitHub Settings → Applications
3. Find "Claude Code" in Authorized OAuth Apps
4. Revoke access or remove specific repositories
```

## Related Articles
- KB-004: How to connect your first GitHub repository to Claude Code Web
- KB-011: How to switch between different connected repositories
- KB-003: How to authenticate your GitHub account with Claude Code Web
- KB-010: How to cancel a running task if needed

## Sources
1. Repository Connection Management - Search: "disconnect repository from cloud IDE" (Accessed: November 16, 2025)
2. Claude Code Web Repository Settings - Search: "claude code web remove disconnect repository" (Accessed: November 16, 2025)
3. GitHub OAuth Application Management - https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/authorizing-oauth-apps (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
