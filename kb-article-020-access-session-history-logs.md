# How to Access Session History and Logs

**Article ID**: KB-020
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 4 minutes

## Overview
Claude Code Web maintains detailed logs and history for all your sessions. This guide shows you how to access session history, read activity logs, and use this information for debugging, learning, and auditing purposes.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one completed or active session
- Understanding of sessions (KB-016)
- Familiarity with monitoring tasks (KB-008)

## Understanding Session History and Logs

### What is Session History?

**Session history includes:**
- Complete chronological list of all sessions
- Sessions across all connected repositories
- Active, completed, cancelled, and failed sessions
- Searchable and filterable records
- Date ranges and timestamps

### What are Session Logs?

**Session logs contain:**
- Every action Claude took during a session
- Files read and analyzed
- Planning and reasoning steps
- Code changes made
- Commands executed
- Errors and warnings encountered
- Timestamps for each action

### Why Access History and Logs?

**Use cases:**
- Debugging: Understand what went wrong
- Learning: See how Claude solved problems
- Auditing: Track all changes made
- Documentation: Record development decisions
- Recovery: Find information about past work
- Troubleshooting: Diagnose issues

## Steps to Access Session History

### Step 1: Navigate to Sessions Area

**Access the main sessions view:**

**Option 1: From Dashboard**
1. Go to `https://claude.com/code`
2. Click "Sessions" or "History" in navigation
3. Or find "View All Sessions" link

**Option 2: Direct Navigation**
1. Look for dedicated History or Sessions section
2. May be in sidebar or main navigation
3. Click to see complete session list

### Step 2: Browse Session History

**View all sessions:**

**Default view typically shows:**
- Most recent sessions first
- Session status (Active, Completed, Cancelled, Failed)
- Repository name
- Task description
- Date and time information
- Duration

**Example session history:**
```
Recent Sessions:

[Nov 16, 2:30 PM] ✓ Add dark mode toggle
  myproject • Completed in 8 min • feature/dark-mode-toggle

[Nov 16, 1:15 PM] ✓ Fix checkout calculation bug
  ecommerce-app • Completed in 5 min • bugfix/checkout-calc

[Nov 16, 10:45 AM] ✗ Update dependencies
  myproject • Cancelled after 2 min • N/A

[Nov 15, 4:20 PM] ✓ Create user profile component
  web-app • Completed in 15 min • feature/user-profile
```

### Step 3: Filter and Search Sessions

**Narrow down sessions:**

**Filter by status:**
- Active sessions only
- Completed sessions
- Failed sessions
- Cancelled sessions
- All sessions

**Filter by repository:**
- Select specific repository from dropdown
- See only sessions for that codebase
- Compare work across repositories

**Filter by date:**
- Today
- Last 7 days
- Last 30 days
- Custom date range
- Specific month or year

**Search by keyword:**
- Enter task description keywords
- Search by branch name
- Find sessions by file names
- Search commit messages

**Example:**
```
Filters Applied:
  Repository: myproject
  Status: Completed
  Date: Last 7 days
  Search: "dark mode"

Results: 3 sessions
```

### Step 4: Open a Specific Session

**View session details:**

**Click on any session to open:**
- Full session detail view
- Complete activity log
- All files changed
- Commit information
- Timestamps and duration

## Steps to Access Session Logs

### Step 1: Open Session Detail View

**Navigate to the session:**
1. Find the session in history
2. Click to open detailed view
3. Logs are typically in main content area

### Step 2: Read the Activity Log

**Activity log format:**

**Chronological entries:**
```
[Time] Action or Event

[2:30 PM] Session started
           Repository: myproject
           Branch: main
           Task: Add dark mode toggle to navigation

[2:30 PM] Initializing sandbox environment

[2:30 PM] Cloning repository

[2:31 PM] Reading repository structure

[2:31 PM] Analyzing package.json
           Found React 18.2.0, standard dependencies

[2:31 PM] Reading src/components/Header.js
           Current header implementation reviewed

[2:31 PM] Planning implementation approach
           Will create useDarkMode hook
           Will add toggle button to Header
           Will use Context API for global state

[2:32 PM] Creating src/hooks/useDarkMode.js

[2:33 PM] Editing src/components/Header.js
           Added toggle button
           Integrated useDarkMode hook
```

### Step 3: Understand Log Entry Types

**Different types of entries:**

**File Operations:**
```
[2:31 PM] Reading src/components/Header.js
[2:33 PM] Editing src/components/Header.js
[2:34 PM] Creating src/hooks/useDarkMode.js
[2:35 PM] Deleting src/old-theme.css
```

**Analysis and Planning:**
```
[2:31 PM] Analyzing codebase structure
[2:32 PM] Planning implementation approach
[2:32 PM] Identified approach: Use React Context
```

**Code Changes:**
```
[2:33 PM] Added function toggleDarkMode() to Header.js
[2:34 PM] Imported useDarkMode hook
[2:35 PM] Updated theme CSS variables
```

**Command Execution:**
```
[2:36 PM] Running: npm test
[2:37 PM] Command output: All tests passed (23 tests)
```

**Git Operations:**
```
[2:38 PM] Creating branch: feature/dark-mode-toggle
[2:38 PM] Committing changes
           Commit message: "Add dark mode toggle component and hook"
[2:38 PM] Pushing to origin/feature/dark-mode-toggle
```

**Errors and Warnings:**
```
[2:35 PM] ⚠ Warning: Linting issue in Header.js line 45
[2:36 PM] ✗ Error: Test failed for darkModeToggle.test.js
[2:36 PM] Fixing test implementation
```

**Completion:**
```
[2:38 PM] Session completed successfully
           Duration: 8 minutes
           Files changed: 5
           Commits: 2
           Branch: feature/dark-mode-toggle
```

### Step 4: Navigate Long Logs

**For extensive logs:**

**Navigation features:**
- Scroll through chronologically
- Jump to specific sections (Start, Completion, Errors)
- Search within log (Ctrl+F or Cmd+F)
- Collapse/expand detailed sections
- View only errors or important events

**Filtering log entries:**
- Show only file operations
- Show only errors/warnings
- Hide routine operations
- Focus on specific file

### Step 5: Export or Save Logs

**Preserve log information:**

**If export available:**
- Look for "Export" or "Download" button
- Options may include:
  - Plain text format
  - JSON format
  - PDF report
  - CSV summary

**Manual preservation:**
- Copy relevant log sections
- Take screenshots of important parts
- Document key decisions
- Save to external notes

## Expected Results

When accessing session history and logs, you should:
- See complete list of all past sessions
- Be able to filter by repository, date, and status
- Search for specific sessions by keyword
- Open any session to view detailed logs
- Read chronological activity for each session
- Understand every action Claude took
- Find specific information quickly
- Export or save important information

## Troubleshooting

### Common Issue 1
**Problem**: Session history doesn't show old sessions
**Solution**:
- Check if there's a retention period (e.g., 90 days)
- Try expanding date range filter
- Verify you're looking at correct account
- Check if "All Repositories" is selected
- Some platforms may archive old sessions

### Common Issue 2
**Problem**: Activity log is incomplete or missing details
**Solution**:
- Refresh the page
- Try reopening the session
- Check if log is still loading (for recent sessions)
- Very old sessions may have truncated logs
- Verify internet connection for loading full data

### Common Issue 3
**Problem**: Can't find a specific session
**Solution**:
- Use search function with task keywords
- Filter by repository first
- Expand date range
- Check cancelled/failed sessions tabs
- Session may have failed during initialization
- Try searching by branch name

### Common Issue 4
**Problem**: Logs are too technical or hard to understand
**Solution**:
- Focus on high-level entries (Planning, Completion)
- Look for summary sections
- Ignore detailed technical commands
- Read commit messages for overview
- Check files changed list instead
- Review KB-025 for understanding Claude's actions

### Common Issue 5
**Problem**: Need to share log information with team
**Solution**:
- Use export function if available
- Copy relevant log sections
- Take screenshots of important parts
- Share branch and commit information instead
- Link directly to GitHub commits
- Summarize key points in your own words

## Additional Tips

### Using Logs for Debugging

**When something went wrong:**

**Investigation steps:**
1. Open the session log
2. Search for "error" or "warning"
3. Find the first error occurrence
4. Read preceding entries for context
5. Understand what Claude was attempting
6. Identify the root cause

**Example debugging:**
```
Problem: Dark mode not working after session

Investigation:
[Search log for "error"]
Found: [2:36 PM] ✗ Error: Test failed for darkModeToggle.test.js

Context:
[2:35 PM] Creating darkModeToggle.test.js
[2:36 PM] Running: npm test
[2:36 PM] ✗ Error: Test failed

[Read error details]
"Theme context provider not wrapped around component"

Solution:
Session completed but test was failing. Need new session to fix test setup.
```

### Learning from Session Logs

**Educational value:**

**Study how Claude:**
- Analyzes unfamiliar codebases
- Plans implementation approaches
- Structures new code
- Handles errors and adapts
- Writes commit messages
- Organizes file changes

**Extract patterns:**
```
Observation from multiple sessions:
- Claude always reads package.json first
- Analyzes related files before editing
- Creates plan before implementation
- Tests changes when possible
- Uses clear, descriptive commit messages

Apply to your work:
- Follow similar analysis patterns
- Plan before coding
- Write descriptive commits
```

### Session History for Project Management

**Track development progress:**

**Use session history to:**
- See what was accomplished each day
- Track velocity (sessions per week)
- Identify common issues
- Plan future work
- Report to stakeholders

**Weekly review example:**
```
Week of Nov 10-16:
- 15 sessions completed
- 3 features added
- 8 bugs fixed
- 4 documentation updates
- Average session duration: 12 minutes
- Success rate: 93% (2 cancelled)
```

### Comparing Sessions

**Analyze different approaches:**

**Compare logs to see:**
- How same type of task was handled differently
- Performance across repositories
- Evolving patterns in Claude's approach
- Effectiveness of different task descriptions

### Log Retention Best Practices

**Manage your logs:**
- Review important sessions promptly
- Export critical session data
- Document important decisions externally
- Don't rely on logs being available forever
- Keep local records of significant changes

### Quick Reference Cheat Sheet

**Common log searches:**
- "error" - Find all errors
- "warning" - Find all warnings
- "commit" - Find git commits
- "creating" - Find new files
- "editing" - Find modified files
- "test" - Find testing activities
- "branch" - Find branch operations

## Related Articles
- KB-019: How to review completed sessions
- KB-017: How to view all your active sessions
- KB-008: How to monitor a running task in real-time
- KB-025: How to understand Claude's responses and actions in the interface
- KB-022: How to intervene and guide Claude mid-session when it encounters issues

## Sources
1. Development Activity Logging - Search: "software development activity logging best practices" (Accessed: November 16, 2025)
2. Claude Code Web Session Management - Search: "claude code web session history logs" (Accessed: November 16, 2025)
3. Audit Trail for Code Changes - Search: "code change audit trail tracking" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
