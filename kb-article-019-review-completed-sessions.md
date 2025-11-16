# How to Review Completed Sessions

**Article ID**: KB-019
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 5 minutes

## Overview
After Claude finishes a coding task, you need to review what was accomplished, examine the changes made, and verify the results. This guide shows you how to effectively review completed sessions and understand what Claude did.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one completed Claude Code session
- Understanding of sessions (KB-016)
- Familiarity with viewing changes (KB-015)

## Understanding Completed Sessions

### What is a Completed Session?

**A completed session is:**
- A task that Claude finished successfully
- All planned work executed
- Changes committed to a branch
- Session marked as "Complete" or "Finished"
- No longer using a concurrent slot

**Completed sessions include:**
- Full activity log of all actions
- List of files created or modified
- Commit messages and branch information
- Pull request details (if created)
- Execution summary
- Total time elapsed

### Why Review Completed Sessions?

**Important reasons:**
- Verify Claude did what you asked
- Understand the approach taken
- Learn implementation patterns
- Identify any issues or oversights
- Prepare for next steps
- Document what was accomplished

## Steps

### Step 1: Access Completed Sessions List

**Navigate to completed sessions:**

**Option 1: From Dashboard**
1. Go to `https://claude.com/code`
2. Look for "Completed Sessions" or "Recent Sessions"
3. Find tabs or sections for completed work
4. Sessions are typically sorted by completion time

**Option 2: From Sessions View**
1. Navigate to Sessions or Tasks area
2. Filter by status: "Completed"
3. Or switch to "Completed" tab
4. Browse chronologically

**Option 3: From Repository View**
1. Select a specific repository
2. View session history for that repository
3. Filter to completed sessions only
4. See all completed work for that codebase

### Step 2: Select a Session to Review

**Identify the session:**

**Session list typically shows:**
- Repository name
- Task description
- Completion time (e.g., "Completed 2 hours ago")
- Status indicator (✓ Complete)
- Duration (e.g., "Took 8 minutes")
- Branch name created

**Click on the session** to open detailed view

### Step 3: Review Session Summary

**High-level overview:**

**Session Header:**
```
✓ Completed Successfully
Repository: myproject
Branch: feature/dark-mode-toggle
Task: Add dark mode toggle to navigation
Started: 2:30 PM
Completed: 2:38 PM
Duration: 8 minutes
```

**Summary Information:**
- What was requested
- What was accomplished
- Key files modified
- Commits made
- PR created (if applicable)

### Step 4: Examine Files Changed

**Review modifications:**

**Files Section shows:**
- List of all files modified
- Number of additions/deletions
- New files created
- Files deleted (if any)

**Example:**
```
Files Changed (5):
  ✎ src/components/Header.js         (+23, -5)
  ✎ src/hooks/useDarkMode.js          (+45, -0)  [new file]
  ✎ src/styles/theme.css              (+67, -2)
  ✎ src/context/ThemeContext.js       (+34, -0)  [new file]
  ✎ README.md                          (+3, -1)
```

**Actions you can take:**
- Click on each file to see diff
- View side-by-side comparison
- See exact changes made
- Understand code additions and removals

### Step 5: Read the Activity Log

**Chronological record:**

**Activity log contains:**
- Every action Claude took
- Files read during analysis
- Planning and decision-making
- Implementation steps
- Testing and verification
- Commit messages

**Example log entries:**
```
[2:30 PM] Session started
[2:30 PM] Reading repository structure
[2:31 PM] Analyzing src/components/Header.js
[2:31 PM] Planning implementation approach
[2:32 PM] Creating src/hooks/useDarkMode.js
[2:33 PM] Editing src/components/Header.js
[2:35 PM] Adding theme CSS variables
[2:36 PM] Creating ThemeContext
[2:37 PM] Running lint checks
[2:38 PM] Committing changes to feature/dark-mode-toggle
[2:38 PM] Session completed successfully
```

### Step 6: Review Commit Details

**Git information:**

**Commits made:**
- View commit messages
- See commit hashes
- Check branch name
- Verify commits pushed to remote

**Example:**
```
Commits (2):
  abc123f - Add dark mode toggle component and hook
  def456g - Update theme CSS and context provider

Branch: feature/dark-mode-toggle
Pushed to: origin/feature/dark-mode-toggle
```

### Step 7: Check Pull Request (If Created)

**PR information:**

**If Claude created a PR:**
- View PR number and title
- Read PR description
- See PR status (Open, Merged, etc.)
- Access direct link to GitHub PR

**Example:**
```
Pull Request Created:
  #42: Add dark mode toggle to navigation
  Status: Open
  Link: https://github.com/username/myproject/pull/42
```

**Actions:**
- Click link to review in GitHub
- Read full PR description
- Review code changes in GitHub UI
- Check CI/CD status
- Merge when ready

### Step 8: Evaluate the Results

**Assessment checklist:**

**Verify:**
- [ ] Task description matches what was delivered
- [ ] All requested features implemented
- [ ] Code quality is acceptable
- [ ] No unexpected changes to unrelated files
- [ ] Commit messages are clear
- [ ] Tests pass (if applicable)
- [ ] Documentation updated (if needed)

**Check for issues:**
- Incomplete implementations
- Bugs or errors introduced
- Missing edge cases
- Style inconsistencies
- Missing tests or documentation

## Expected Results

When reviewing a completed session, you should:
- See clear summary of what was accomplished
- Have access to all files that were changed
- Be able to view diffs and exact modifications
- Understand Claude's implementation approach
- Know which branch contains the changes
- Have PR details if one was created
- See complete activity timeline
- Verify task completion against requirements

## Troubleshooting

### Common Issue 1
**Problem**: Can't find a recently completed session
**Solution**:
- Refresh the page
- Check "Recent" or "All Sessions" view
- Verify correct repository filter is selected
- Look in multiple time ranges (today, this week)
- Search by task description keywords
- Check if session actually completed vs failed/cancelled

### Common Issue 2
**Problem**: Session marked complete but changes not visible
**Solution**:
- Check which branch the changes are on
- Verify changes were committed (check activity log)
- Look at commit details for branch name
- Pull latest changes from remote
- Check if repository view is on correct branch
- Review git history for the commits

### Common Issue 3
**Problem**: Activity log is too long to review effectively
**Solution**:
- Focus on summary section first
- Look at key milestones (file creations, commits)
- Use browser search (Ctrl+F) for specific terms
- Review files changed instead of full log
- Check commit messages for high-level summary
- Focus on any error or warning messages

### Common Issue 4
**Problem**: Completed session has unexpected changes
**Solution**:
- Review activity log to understand Claude's reasoning
- Check if task description was ambiguous
- Examine files changed section carefully
- Compare against your original request
- Use git to revert specific unwanted changes
- Provide feedback for future tasks to be more specific

### Common Issue 5
**Problem**: Session shows completed but task is not actually done
**Solution**:
- Review what was accomplished vs what was requested
- Check for errors in activity log that were bypassed
- Verify Claude understood the full scope
- Task description may have been unclear
- Start a new session to complete remaining work
- Provide more specific instructions next time

## Additional Tips

### Effective Review Process

**Step-by-step approach:**

**1. Quick overview (30 seconds)**
- Status and duration
- Branch name
- Files changed count

**2. Summary review (2 minutes)**
- Read completion summary
- Check commit messages
- Verify high-level approach

**3. Detailed review (5-15 minutes)**
- Examine each file's changes
- Review critical logic
- Check edge cases
- Verify tests (if applicable)

**4. Testing (varies)**
- Pull branch locally
- Test functionality
- Verify everything works
- Check for regressions

### Learning from Sessions

**Extract insights:**
- How did Claude approach the problem?
- What patterns did Claude use?
- How was the code organized?
- What can you apply to future tasks?

**Example learnings:**
```
Session: "Add user authentication"

Observations:
- Claude created separate files for each concern
- Used existing patterns from codebase
- Added comprehensive error handling
- Followed project naming conventions
- Created tests alongside implementation

Apply to future tasks:
- Trust Claude to follow existing patterns
- Don't over-specify implementation details
- Claude naturally includes error handling
```

### Session History as Documentation

**Completed sessions serve as:**
- Implementation history
- Decision record
- Code evolution tracking
- Learning resource
- Audit trail
- Progress documentation

**Use cases:**
- Understand why changes were made
- Review implementation decisions
- Track project evolution
- Share with team members
- Onboard new developers

### Organizing Completed Sessions

**Keep sessions organized:**
- Review and close completed sessions
- Archive old sessions if platform allows
- Tag or categorize important sessions
- Export session summaries for records
- Link sessions to issues/tickets

### Batch Review Strategy

**When reviewing multiple sessions:**

**Priority order:**
1. Critical/production sessions first
2. Sessions affecting core functionality
3. Dependencies for other work
4. Experimental or learning sessions last

**Efficient batch review:**
- Group by repository
- Review related sessions together
- Check for conflicts between sessions
- Verify integration across multiple changes

### Session Retention

**Understand retention policies:**
- How long are completed sessions stored?
- Can you export session data?
- Are activity logs retained indefinitely?
- Should you document important sessions externally?

## Related Articles
- KB-015: How to view changes made by Claude Code in your repository
- KB-017: How to view all your active sessions
- KB-020: How to access session history and logs
- KB-008: How to monitor a running task in real-time
- KB-016: How to start a new Claude Code session

## Sources
1. Code Review Best Practices - Search: "code review checklist best practices" (Accessed: November 16, 2025)
2. Claude Code Web Session History - Search: "claude code web review completed sessions" (Accessed: November 16, 2025)
3. Git Change Review Workflows - Search: "reviewing git commits and changes effectively" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
