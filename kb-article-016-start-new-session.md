# How to Start a New Claude Code Session

**Article ID**: KB-016
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 3 minutes

## Overview
A session in Claude Code Web represents an independent coding workflow. This guide explains what sessions are and how to start new ones for different tasks or repositories.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one connected repository
- Understanding of basic task creation (KB-006)

## Understanding Sessions

### What is a Session?

**A session is:**
- An instance of Claude working on a specific task
- Isolated in its own sandboxed environment
- Associated with one repository and one task
- Tracked independently with its own history

**One session typically includes:**
- Task description
- All file reads and analyses
- Code changes made
- Commits and branch creation
- Complete activity log

### Sessions vs Tasks

These terms are often used interchangeably:
- **Task**: What you want accomplished
- **Session**: The execution of that task

Creating a new task starts a new session.

## Steps to Start a New Session

### Method 1: From Dashboard

**Step 1: Navigate to Claude Code Web**
```
https://claude.com/code
```

**Step 2: Select Repository**
- Click on the repository you want to work with
- Or select from a repository dropdown

**Step 3: Create New Task**
- Look for "New Task," "New Session," or "Start Coding" button
- Click to open task creation interface

**Step 4: Describe Your Task**
- Enter your task description
- Be specific about what you want Claude to do
- See KB-007 for effective task descriptions

**Step 5: Submit**
- Click "Start Task" or "Create Session"
- Session begins immediately
- You're taken to the session monitoring view

### Method 2: Quick Start Button

**Direct Path:**
1. Look for a prominent "+" or "New" button
2. Often located in the header or navigation
3. Click to immediately open task creation
4. Select repository and describe task
5. Submit to start session

### Method 3: From Repository View

**When viewing a specific repository:**
1. Repository details page shows "New Task" option
2. Task is automatically associated with current repository
3. Enter description and start

## What Happens When You Start a Session

### Immediate Actions

**1. Session Initialization (0-10 seconds)**
```
Creating session...
Allocating sandbox environment...
Connecting to repository...
```

**2. Session ID Assignment**
- Unique identifier for this session
- Used for tracking and logging
- Appears in session list

**3. Concurrent Slot Allocation**
- Takes up one of your concurrent task slots
- Pro: Uses 1 of 3 slots
- Max: Uses 1 of 10+ slots

**4. Environment Setup**
- Isolated VM or container created
- Repository cloned
- Dependencies prepared

### Session Starts Running

**Phase 1: Analysis**
- Claude reads repository structure
- Analyzes relevant files
- Builds context

**Phase 2-N: Execution**
- Implements your request
- Makes code changes
- Runs tests if needed
- Commits and pushes

## Session Information

### What's Tracked in Each Session

**Metadata:**
- Session ID
- Start time
- Repository name
- Branch being worked on
- Current status

**Activity:**
- Every file read
- Every file edited
- All commands executed
- Commit messages
- Error messages

**Outcomes:**
- Commits made
- Branch created
- PR details
- Completion status

## Multiple Sessions

### Running Concurrent Sessions

**You can have multiple sessions running simultaneously:**

**Example with Pro (3 concurrent tasks):**
```
Session 1: Repo A - Adding feature X
Session 2: Repo B - Fixing bug Y
Session 3: Repo A - Writing tests for Z
```

**Example with Max (10+ concurrent tasks):**
```
Sessions 1-5: Various features across Repo A
Sessions 6-8: Bug fixes in Repo B
Session 9: Documentation in Repo C
Session 10: Code review help in Repo D
```

### Same Repository, Multiple Sessions

**You can run multiple sessions in the same repository:**
- Each gets its own branch
- Each works independently
- No conflicts between sessions
- Useful for parallel feature development

## Expected Results

After starting a new session, you should:
- See the session in your "Active Sessions" list
- Have a unique session ID assigned
- Watch Claude begin analyzing your repository
- See real-time progress updates
- Have access to session monitoring view
- Know which concurrent slot is being used

## Troubleshooting

### Common Issue 1
**Problem**: Can't start a new session
**Solution**:
- Check if you've reached your concurrent task limit
- Cancel or wait for other sessions to complete
- Verify your subscription is active
- Ensure the repository is properly connected
- Refresh the page and try again

### Common Issue 2
**Problem**: Session fails to initialize
**Solution**:
- Verify repository exists and is accessible
- Check if the repository is empty (needs at least one commit)
- Ensure you have permissions for the repository
- Try a different repository to isolate the issue
- Check your internet connection

### Common Issue 3
**Problem**: Started session in wrong repository
**Solution**:
- Cancel the session immediately (KB-010)
- Free up the concurrent slot
- Start a new session in the correct repository
- Double-check repository selection before submitting

### Common Issue 4
**Problem**: Multiple sessions for same task accidentally created
**Solution**:
- Cancel the duplicate sessions
- Keep only one session running for the task
- Be careful not to double-click submit buttons
- Check active sessions before creating new ones

## Additional Tips

### Before Starting a Session

**Checklist:**
- [ ] Know which repository to work on
- [ ] Have clear task description ready
- [ ] Verify you have concurrent slots available
- [ ] Confirm repository is up to date
- [ ] Understand expected outcomes

### Session Naming and Organization

**Good Task Descriptions Serve as Session Names:**
```
✓ "Add email validation to contact form"
✓ "Fix calculation bug in checkout total"
✓ "Create user profile component"

✗ "Update code"
✗ "Fix stuff"
✗ "Work on project"
```

Clear descriptions make sessions easy to identify later.

### Managing Your Sessions

**Best Practices:**
- Start sessions intentionally, not impulsively
- Monitor new sessions for first few minutes
- Ensure session is progressing before starting another
- Use descriptive task descriptions for easy identification
- Clean up completed sessions periodically

### Strategic Session Creation

**Parallel Workflows:**
- Start multiple sessions for independent features
- Work on different repositories simultaneously
- Balance quick tasks with long-running ones

**Sequential Workflows:**
- Complete one session before starting related ones
- Ensure dependencies are met before next session
- Build on previous session results

## Related Articles
- KB-006: How to create your first coding task in Claude Code Web
- KB-007: How to describe a task effectively for Claude to understand
- KB-017: How to view all your active sessions
- KB-018: How to pause and resume a session
- KB-010: How to cancel a running task if needed

## Sources
1. Session Management in Cloud IDEs - Search: "cloud IDE session management concurrent tasks" (Accessed: November 16, 2025)
2. Claude Code Web Session Guide - Search: "claude code web start new session workflow" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
