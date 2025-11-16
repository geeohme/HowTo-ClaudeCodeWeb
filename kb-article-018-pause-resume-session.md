# How to Pause and Resume a Session

**Article ID**: KB-018
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 4 minutes

## Overview
While Claude Code Web sessions typically run to completion, you may need to pause work temporarily or resume sessions that were interrupted. This guide explains the pause and resume functionality, workarounds, and best practices for managing session continuity.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one connected repository
- Understanding of active sessions (KB-017)
- Familiarity with session management (KB-016)

## Understanding Pause vs Cancel

### Pause (If Available)
**Pause means:**
- Temporarily stop execution
- Preserve session state
- Free up concurrent slot temporarily
- Ability to resume later from same point
- Maintain context and progress

**Use pause when:**
- You need the concurrent slot for urgent work
- Waiting for external dependency
- Need to review progress before continuing
- Want to come back to this task later

### Cancel (Always Available)
**Cancel means:**
- Permanently stop the session
- Cannot resume from where it stopped
- Frees concurrent slot permanently
- Keeps changes made so far
- Lose the execution context

**Use cancel when:**
- Task is going in wrong direction
- No longer need this work
- Want to start over fresh
- Session is stuck or erroring

## Important Note About Pause Functionality

**Current Status (November 2025):**
As of this writing, Claude Code Web's pause/resume functionality may be limited or in development. The ability to pause and resume sessions depends on:

- Your specific plan tier (Pro vs Max)
- Platform updates and feature rollouts
- Session type and complexity
- Current platform capabilities

**If pause is not available:**
- Use the workarounds described below
- Cancel and restart with context
- Use concurrent slots strategically
- Plan sessions for completion

## Steps to Pause a Session (If Available)

### Step 1: Access the Active Session

**Navigate to the session:**
1. Go to `https://claude.com/code`
2. Click on "Active Sessions"
3. Find the session you want to pause
4. Click to open session details

### Step 2: Locate Pause Control

**Look for pause option:**
- Pause button in session header
- Three-dot menu (⋮) with "Pause" option
- Session controls area
- Right-click context menu

**Common locations:**
```
[Session Header]
  Repository Name | Task Description
  [View] [Pause] [Cancel]
```

### Step 3: Initiate Pause

**Click the pause button:**
1. Click "Pause" or pause icon (⏸)
2. Confirm the pause action if prompted
3. Wait for session to pause gracefully

**What happens during pause:**
```
Pausing session...
Saving current state...
Completing current operation...
Session paused successfully
```

### Step 4: Verify Paused State

**Session should show:**
- Status: "Paused"
- Icon indicating paused state
- Time paused
- Option to resume
- Note that concurrent slot is freed

**Paused session indicator:**
```
Status: ⏸ Paused
Repository: myproject
Task: Add dark mode toggle
Paused: 2 minutes ago
[Resume] [Cancel]
```

## Steps to Resume a Session

### Step 1: Locate Paused Session

**Find your paused work:**
1. Go to Active Sessions or Sessions list
2. Filter by "Paused" status
3. Or find in main session list with pause indicator
4. Click on the paused session

### Step 2: Check Concurrent Slot Availability

**Before resuming:**
- Verify you have an available concurrent slot
- Pro: Must have fewer than 3 active sessions
- Max: Must have fewer than 10+ active sessions
- If at limit, complete or cancel another session first

### Step 3: Resume the Session

**Click resume:**
1. Click "Resume" button
2. Confirm if prompted
3. Session re-allocates a concurrent slot
4. Wait for session to reinitialize

**Resume process:**
```
Resuming session...
Restoring context...
Reconnecting to sandbox...
Session active again
```

### Step 4: Monitor Resumed Session

**After resume:**
- Check session picks up where it left off
- Verify files and context are preserved
- Monitor for any initialization issues
- Confirm work continues appropriately

## Workarounds When Pause is Not Available

### Workaround 1: Cancel and Restart with Context

**Preserve your progress:**

**Step 1: Document session state**
- Note which files were modified
- Copy Claude's progress summary
- Save any important context from activity log
- Note the branch name if created

**Step 2: Cancel the session**
- Click "Cancel" on the running session
- Changes made so far are preserved in the branch

**Step 3: Create new session later**
- Start a new session when ready
- Provide context: "Continue work on [branch-name]. Already completed: [X, Y, Z]. Now need to: [remaining work]"
- Claude can review existing changes and continue

**Example:**
```
New task description:
"Continue the dark mode implementation on branch feature/dark-mode.
Already completed: toggle button added to header, basic state management.
Still needed: CSS theme variables, localStorage persistence, theme application to all components."
```

### Workaround 2: Let Session Complete, Then Iterate

**Alternative approach:**
1. Let the current session finish
2. Review the completed work
3. Start a new session for additional changes
4. Build on the previous session's output

**Benefits:**
- Clean session boundaries
- Clear git history
- Easier to track progress
- No session state complexity

### Workaround 3: Strategic Session Planning

**Avoid need to pause:**
- Break large tasks into smaller sessions
- Plan sessions to complete within available time
- Use concurrent slots intentionally
- Queue tasks instead of pausing

## Expected Results

When pausing and resuming works correctly, you should:
- Successfully pause a running session
- Free up concurrent slot temporarily
- See paused status in session list
- Be able to resume from the same point
- Have context and progress preserved
- Continue work seamlessly

If using workarounds:
- Successfully preserve progress via git branches
- Be able to continue work in new session
- Maintain code continuity
- Not lose significant progress

## Troubleshooting

### Common Issue 1
**Problem**: Cannot find pause button
**Solution**:
- Pause feature may not be available in current platform version
- Check session controls thoroughly (menus, three-dot icons)
- Look for alternative session management options
- Use "Cancel and restart with context" workaround
- Check platform documentation for feature availability

### Common Issue 2
**Problem**: Paused session won't resume
**Solution**:
- Check concurrent slot availability
- Ensure you're under your plan limit
- Try refreshing the page
- Check if session expired after too long paused
- Verify repository is still connected
- If issue persists, start new session with previous context

### Common Issue 3
**Problem**: Session loses context after resuming
**Solution**:
- This may indicate pause/resume limitations
- Check activity log for continuity
- Provide additional context manually
- Consider starting fresh session with full context
- Document expected behavior vs actual

### Common Issue 4
**Problem**: Need to pause but option isn't available
**Solution**:
- Use Workaround 1: Cancel and document state
- Changes are preserved in git branch
- Create comprehensive handoff notes
- Start new session with detailed context
- This is often more reliable than pause

### Common Issue 5
**Problem**: Accidentally cancelled instead of paused
**Solution**:
- Check the branch created by the session
- All committed changes are preserved
- Review session activity log while still available
- Start new session referencing the branch
- Provide context about what was completed

## Additional Tips

### When Pause Would Be Useful

**Scenarios:**
- Long-running task but urgent issue arises
- Need to free concurrent slot temporarily
- Waiting for external feedback or approval
- Dependency blocker discovered
- Need to switch priorities midstream

### Session State Management

**Best practices:**
- Keep sessions focused and completable
- Plan for sessions to finish naturally
- Use git branches to preserve work
- Document progress in commit messages
- Don't rely heavily on pause if unstable

### Alternative Session Management

**Instead of pausing:**

**Strategy 1: Multiple short sessions**
```
❌ One 60-minute session (might need pause)
✅ Three 20-minute sessions (complete naturally)
```

**Strategy 2: Progressive tasks**
```
Task 1: "Implement basic dark mode toggle"
Task 2: "Add dark mode CSS variables"
Task 3: "Apply dark mode to all components"
```

**Strategy 3: Checkpoint commits**
- Request Claude commit at logical points
- Each commit is a resumption point
- Easy to continue in new session

### Documentation for Continuity

**If you must interrupt a session:**

**Capture:**
- Current branch name
- Files modified so far
- What's completed
- What's remaining
- Any decisions made
- Known issues or blockers

**Template:**
```
Session interrupted on branch: feature/dark-mode
Completed:
  ✓ Added toggle button to Header.js
  ✓ Created useDarkMode hook
  ✓ Basic state management working

Remaining:
  ☐ Add CSS custom properties for theming
  ☐ Implement localStorage persistence
  ☐ Apply to all components

Notes:
  - Using React Context for state
  - Following existing theme pattern from UserSettings
```

### Working with Session Limits

**Concurrent slot strategy:**
- Reserve one slot for urgent work
- Plan sessions to complete within time windows
- Stagger session starts
- Use pause only when necessary
- Cancel stuck sessions promptly

## Related Articles
- KB-017: How to view all your active sessions
- KB-016: How to start a new Claude Code session
- KB-010: How to cancel a running task if needed
- KB-019: How to review completed sessions
- KB-020: How to access session history and logs
- KB-021: How to provide additional context to Claude during a task

## Sources
1. Session State Management in Cloud IDEs - Search: "cloud IDE pause resume session state" (Accessed: November 16, 2025)
2. Claude Code Web Session Controls - Search: "claude code web pause session functionality" (Accessed: November 16, 2025)
3. Development Workflow Interruption Handling - Search: "handling interrupted coding sessions best practices" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
