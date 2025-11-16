# How to View All Your Active Sessions

**Article ID**: KB-017
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 3 minutes

## Overview
Claude Code Web allows you to run multiple coding tasks concurrently. This guide shows you how to view all your active sessions, monitor their status, and understand what each session is doing at a glance.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one connected repository
- Understanding of sessions (KB-016)

## Understanding Active Sessions

### What Counts as an Active Session?

**Active sessions include:**
- Currently running tasks
- Tasks in progress (analyzing, implementing, testing)
- Sessions waiting for your input or approval
- Recently paused sessions (if pause feature is available)

**Not included in active sessions:**
- Completed sessions
- Cancelled sessions
- Failed sessions
- Sessions from disconnected repositories

### Concurrent Session Limits

Your plan determines how many sessions can run simultaneously:

**Claude Pro:**
- Maximum 3 concurrent sessions
- Attempts to start a 4th session will be blocked
- Must wait for or cancel existing sessions

**Claude Max:**
- Maximum 10+ concurrent sessions
- Higher limits for power users
- More flexibility for parallel workflows

## Steps

### Step 1: Navigate to Active Sessions View

**Method 1: From Dashboard**
1. Go to `https://claude.com/code`
2. The main dashboard displays active sessions prominently
3. Look for sections labeled:
   - "Active Sessions"
   - "Running Tasks"
   - "In Progress"
   - "Current Work"

**Method 2: From Navigation Menu**
1. Open Claude Code Web
2. Look for a "Sessions" or "Tasks" link in the navigation
3. Click to view all sessions
4. Filter by "Active" or "Running" status

**Method 3: Quick View Panel**
- Some interfaces have a persistent sidebar
- Shows active session count
- Click to expand for full details

### Step 2: Understand the Active Sessions List

Each active session typically displays:

**Session Card/Row Information:**

**Primary Details:**
- Repository name and icon
- Task description/title
- Current status (Running, Analyzing, Implementing)
- Elapsed time or start time

**Status Indicators:**
- Visual indicator (spinner, progress bar, colored dot)
- Current phase (Reading files, Writing code, Testing)
- Progress percentage (if available)

**Quick Actions:**
- View/Open button
- Cancel button
- Pause button (if available)

### Step 3: Monitor Session Status

**Common Status Labels:**

**Initializing:**
```
Status: Initializing
Details: Setting up sandbox environment...
Time: Just started
```

**Analyzing:**
```
Status: Analyzing
Details: Reading codebase and understanding context
Time: 1-5 minutes typically
```

**Planning:**
```
Status: Planning
Details: Creating implementation plan
Time: 30 seconds - 2 minutes
```

**Implementing:**
```
Status: Implementing
Details: Writing code and making changes
Time: Varies by task complexity
```

**Testing:**
```
Status: Testing
Details: Running tests and verifying changes
Time: 1-10 minutes depending on test suite
```

**Waiting for Input:**
```
Status: Awaiting Response
Details: Claude needs your input to proceed
Action Required: Review and respond
```

### Step 4: View Session Details

Click on any active session to see:

**Detailed View Includes:**

**Header Information:**
- Full task description
- Repository and branch name
- Session ID
- Start time and elapsed duration
- Current status

**Activity Timeline:**
- Chronological log of all actions
- Files read and modified
- Commands executed
- Decisions made by Claude

**Recent Activity:**
- Last 5-10 actions
- Most recent file changes
- Latest status update

**Context:**
- Which files are being worked on
- What phase of the task is current
- Any blockers or issues encountered

### Step 5: Organize and Filter Sessions

**Sorting Options:**

**By Time:**
- Newest first (most recently started)
- Oldest first (longest running)
- Useful for identifying stuck sessions

**By Repository:**
- Group sessions by repository
- See all work happening in one codebase
- Identify repository-specific bottlenecks

**By Status:**
- All running
- Waiting for input
- Completing
- Helpful for finding sessions needing attention

**Search and Filter:**
- Search by task description
- Filter by repository name
- Filter by time range
- Find specific sessions quickly

## Expected Results

When viewing active sessions, you should:
- See all currently running sessions at a glance
- Know how many concurrent slots are in use (e.g., "3 of 3 active")
- Identify which sessions need your attention
- Understand the current status of each session
- Have quick access to view details or cancel sessions
- See elapsed time for long-running sessions
- Identify which repositories are being worked on

## Troubleshooting

### Common Issue 1
**Problem**: No active sessions showing but you started a task
**Solution**:
- Refresh the page
- Check if the task failed during initialization
- Verify you're viewing the correct account
- Check "Completed" or "Failed" tabs for the session
- Ensure your internet connection is stable

### Common Issue 2
**Problem**: Session count doesn't match what you remember starting
**Solution**:
- Some sessions may have completed quickly
- Check completed sessions for finished tasks
- Verify cancelled sessions weren't accidentally stopped
- Sessions can fail during initialization without being obvious
- Check all repository filters are cleared

### Common Issue 3
**Problem**: Can't see details of a specific active session
**Solution**:
- Click directly on the session card/row
- Try refreshing the page
- Check browser console for JavaScript errors
- Try a different browser if issue persists
- Session may be in transition - wait 30 seconds and retry

### Common Issue 4
**Problem**: Active sessions view shows "0 of 3" but can't start new session
**Solution**:
- There may be sessions in cleanup/teardown phase
- Wait 1-2 minutes for full cleanup
- Refresh the page to get updated status
- Check for ghost sessions in different repository views
- Try logging out and back in if issue persists

### Common Issue 5
**Problem**: Session appears active but not making progress
**Solution**:
- Click into the session to see detailed activity log
- Check if Claude is waiting for your input
- Look for error messages in the activity feed
- Session may be running long-duration tests
- If stuck for 10+ minutes with no activity, consider cancelling

## Additional Tips

### Dashboard Organization

**Keep Track of Your Work:**
- Regularly check active sessions
- Close/review completed sessions
- Don't let sessions accumulate
- Maintain awareness of concurrent slot usage

**Session Hygiene:**
- Cancel sessions you no longer need
- Don't start more sessions than you can monitor
- Review stuck sessions promptly
- Clear completed sessions periodically

### Maximizing Concurrent Sessions

**With Pro (3 concurrent):**
```
Strategy 1: Quick Tasks
- Slot 1: Bug fix (5 min)
- Slot 2: Documentation (10 min)
- Slot 3: Small feature (15 min)

Strategy 2: Mixed Duration
- Slot 1: Major feature (60 min)
- Slot 2: Quick fix (5 min) - rotates frequently
- Slot 3: Quick fix (5 min) - rotates frequently
```

**With Max (10+ concurrent):**
```
Example Allocation:
- Slots 1-3: Major features (long-running)
- Slots 4-7: Bug fixes (medium duration)
- Slots 8-10: Quick tasks (fast rotation)
```

### Monitoring Best Practices

**Regular Check-ins:**
- Review active sessions every 15-30 minutes
- Respond promptly to "Waiting for Input" sessions
- Identify and cancel stuck sessions
- Keep at least one slot free for urgent tasks

**Prioritization:**
- Put important tasks in first slots
- Use later slots for experimental or low-priority work
- Keep production fixes in monitored slots
- Leave room for urgent requests

### Visual Indicators

**Learn to Read at a Glance:**
- Green/spinning = actively working
- Yellow/pause = waiting for input
- Red/error = encountered problem
- Completion icon = finishing up
- Time indicators = duration awareness

### Multi-Repository Management

**When working across repositories:**
- Use repository filter to focus
- Group related work together
- Stagger starts to avoid resource contention
- Monitor repository-specific sessions separately

## Related Articles
- KB-016: How to start a new Claude Code session
- KB-018: How to pause and resume a session
- KB-019: How to review completed sessions
- KB-008: How to monitor a running task in real-time
- KB-010: How to cancel a running task if needed
- KB-005: How to understand the difference between Pro and Max concurrent limits

## Sources
1. Session Management in Cloud Development Environments - Search: "cloud IDE session management concurrent tasks view" (Accessed: November 16, 2025)
2. Claude Code Web Dashboard Guide - Search: "claude code web active sessions dashboard" (Accessed: November 16, 2025)
3. Multi-Task Management Best Practices - Search: "managing multiple concurrent coding tasks" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
