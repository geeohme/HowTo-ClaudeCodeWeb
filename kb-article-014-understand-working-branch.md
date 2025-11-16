# How to Understand Which Branch Claude Code is Working On

**Article ID**: KB-014
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 5 minutes

## Overview
Claude Code Web creates new branches for your tasks and needs to know which branch to base its work on. Understanding branch management in Claude Code Web ensures changes are made in the right place and helps prevent conflicts.

## Prerequisites
- Basic understanding of Git branches
- Connected repository in Claude Code Web
- Knowledge of your repository's branching strategy

## How Claude Code Uses Branches

### Default Behavior

**When You Start a Task:**
1. Claude identifies your repository's default branch (usually `main` or `master`)
2. Claude creates a new branch from that default branch
3. All changes are made on the new branch
4. The new branch is pushed to your repository
5. You can create a pull request to merge changes back

**Branch Naming:**
Claude typically creates branches with names like:
- `claude/feature-name-abc123`
- `claude/fix-bug-xyz789`
- Names that describe the task

## Finding the Current Branch

### Step 1: Check the Repository View

When viewing a repository in Claude Code Web:

**Look for Branch Indicator:**
- Usually near the repository name
- Shows current branch: "Branch: main" or similar
- Dropdown or button to switch branches
- Git icon or branch icon (often ⎋ or similar)

### Step 2: During Task Creation

When creating a task:

**Base Branch Information:**
- Interface may show "Base branch: main"
- Indicates which branch will be the starting point
- New work will branch off from this point

### Step 3: During Task Execution

While a task runs:

**Activity Feed Shows:**
```
Creating new branch from main...
Branch created: claude/add-validation-x7k2m
Switching to branch claude/add-validation-x7k2m
```

**Session Details Show:**
- Source branch (what you branched from)
- Working branch (where changes are being made)
- Commit information

### Step 4: After Task Completion

Once a task completes:

**Completion Message Shows:**
```
Committed changes to branch: claude/add-validation-x7k2m
Pushed to origin/claude/add-validation-x7k2m
```

**GitHub Confirmation:**
- Visit your repository on GitHub
- See the new branch in the branches list
- Option to create pull request

## Branch Workflow Patterns

### Pattern 1: Default Branch → Feature Branch

**Most Common:**
```
main (default)
  ↓
claude/new-feature (created by Claude)
  ↓
Changes committed here
  ↓
Pull request to main
```

### Pattern 2: Working on Existing Branch

If you need Claude to work on an existing branch:
1. Some interfaces allow selecting the base branch
2. Specify in your task description
3. Claude branches from the selected branch

### Pattern 3: Multiple Sessions

**Parallel Work:**
```
main
  ├─→ claude/feature-a (Session 1)
  ├─→ claude/feature-b (Session 2)
  └─→ claude/bug-fix (Session 3)
```

Each session gets its own branch.

## Understanding Branch Information

### Branch Indicators in the UI

**Repository Page:**
- Current branch dropdown: "📋 main"
- Branch selector
- Recently used branches

**Session Page:**
- "Working on branch: claude/feature-name"
- "Based on: main"
- "Branch status: pushed"

### Reading Branch Names

**Claude-Created Branches:**
```
claude/add-user-authentication-a1b2c3

claude/ → Prefix indicating Claude created it
add-user-authentication → Description of work
a1b2c3 → Unique identifier
```

**Why Unique Identifiers:**
- Prevents naming conflicts
- Allows multiple similar tasks
- Easy to track specific sessions

## Verifying Branch on GitHub

### Step 1: Visit Your Repository
```
https://github.com/your-username/your-repo
```

### Step 2: View Branches

1. Click the branch dropdown (usually shows "main" or current branch)
2. See list of all branches
3. Look for branches starting with "claude/"

### Step 3: Check Branch Details

For each Claude branch:
- See when it was created
- View commits made
- Compare with base branch
- Create pull request if desired

## Expected Results

After understanding branch management, you should:
- Know which branch Claude is using for each task
- Understand where Claude creates new branches from
- Be able to find Claude's branches on GitHub
- Know how to check the base branch before starting a task
- Understand the naming convention for Claude's branches
- Be confident that your main/master branch remains unchanged until you merge

## Troubleshooting

### Common Issue 1
**Problem**: Don't know which branch a task is working on
**Solution**:
- Check the task's activity feed
- Look for "Creating branch" or "Switched to branch" messages
- Visit the task details or session page
- Check GitHub for recently created branches starting with "claude/"

### Common Issue 2
**Problem**: Claude created a branch from the wrong base branch
**Solution**:
- Cancel the task
- Verify the repository's default branch setting
- Create a new task and specify the correct base branch if possible
- Or let the task complete and manually rebase the branch

### Common Issue 3
**Problem**: Can't find Claude's branch on GitHub
**Solution**:
- Wait a few moments and refresh GitHub
- Branch is created locally first, then pushed
- Check the task completion status
- Look in the full branch list (not just recently viewed)
- Verify the task completed successfully

### Common Issue 4
**Problem**: Want Claude to work on an existing feature branch, not main
**Solution**:
- Check if your interface allows selecting a base branch
- Specify in the task description: "Start from branch develop"
- Some workflows may require manual branch setup
- Consult Claude Code Web documentation for advanced branch options

### Common Issue 5
**Problem**: Too many Claude branches accumulating
**Solution**:
- Delete merged branches on GitHub
- Use GitHub's automatic branch deletion after PR merge
- Periodically clean up old branches
- This is normal—one branch per task is expected

## Branch Best Practices

### Before Starting Tasks

**✓ Verify Default Branch:**
- Check repository settings on GitHub
- Ensure default branch is correct (main, master, develop, etc.)
- Claude will use this as the base

**✓ Update Default Branch:**
- Keep your default branch up to date
- Pull latest changes regularly
- Prevents Claude from working on outdated code

### During Tasks

**✓ Monitor Branch Creation:**
- Watch the activity feed for branch name
- Note the base branch being used
- Verify it matches your expectations

### After Tasks

**✓ Create Pull Requests:**
- Review Claude's changes
- Create PR from Claude's branch to base branch
- Merge when satisfied

**✓ Clean Up:**
- Delete merged branches
- Keep repository tidy
- Archive or document important branches

## Related Articles
- KB-013: How to view the file structure of your connected repository
- KB-015: How to view changes made by Claude Code in your repository
- KB-006: How to create your first coding task in Claude Code Web
- KB-004: How to connect your first GitHub repository to Claude Code Web

## Sources
1. Git Branching Basics - https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging (Accessed: November 16, 2025)
2. GitHub Branch Management - https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-branches (Accessed: November 16, 2025)
3. Claude Code Web Branching Strategy - Search: "claude code web git branch management" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
