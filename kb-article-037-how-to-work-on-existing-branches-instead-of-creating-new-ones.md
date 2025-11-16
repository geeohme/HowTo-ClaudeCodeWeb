# How to work on existing branches instead of creating new ones

**Article ID**: KB-037
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10 minutes

## Overview
Claude Code Web automatically creates new branches by default, but you can work on existing branches by instructing Claude to check them out during your session. This guide explains how to continue development on existing feature branches, work with team branches, and avoid creating unnecessary new branches when you need to build upon existing work.

## Prerequisites
- Connected GitHub repository (KB-004)
- Basic understanding of Git branches
- Familiarity with starting sessions (KB-016)
- Understanding of Claude's branch behavior (KB-014)

## Understanding the Default Behavior

### How Claude Normally Works

**Default Branch Creation:**
When you start a new task, Claude Code Web:

1. **Starts from default branch**: Clones your repository at the default branch (typically `main` or `master`)
2. **Creates new branch**: Automatically creates a branch like `claude/task-description-abc123`
3. **Works in isolation**: Makes all changes on this new branch
4. **Pushes when complete**: Pushes the new branch to your repository

**This is ideal for:**
- Starting completely new features
- Bug fixes that branch from main
- Independent work that doesn't depend on other branches
- Following standard Git Flow workflows

### When You Need Existing Branches

**Scenarios requiring existing branch work:**

**Continuing previous work:**
- Adding to a feature branch you or a teammate created
- Completing work that Claude started in a previous session
- Iterating on a pull request that needs changes

**Working with team branches:**
- Contributing to a shared `develop` branch
- Building on top of a feature branch created by another developer
- Making changes to a long-running feature branch

**Complex workflows:**
- GitFlow with `develop`, `release`, or `hotfix` branches
- Trunk-based development with short-lived feature branches
- Multi-stage feature development across several branches

## Steps to Work on Existing Branches

### Step 1: Identify the Target Branch

Before starting your session, determine which existing branch you want to work on.

**Find Available Branches:**

Visit your repository on GitHub:
```
https://github.com/your-username/your-repo/branches
```

**Note the exact branch name:**
```
✓ feature/user-authentication
✓ develop
✓ hotfix/security-patch
✓ release/v2.0
✓ claude/previous-task-abc123
```

**Important**: Copy the exact branch name including any prefixes.

### Step 2: Start Your Session Normally

Navigate to Claude Code Web:
```
https://claude.com/code
```

**Create Your Task:**
1. Select your repository
2. Start a new session
3. Begin with your task description

**Note**: The session will still start from your default branch initially. This is a current limitation of Claude Code Web, but we'll address it in the next step.

### Step 3: Instruct Claude to Check Out the Existing Branch

**In your task prompt, include explicit git instructions:**

**Method 1: Direct Checkout Instruction**

```
First, check out the existing branch 'feature/user-authentication' using:
git checkout feature/user-authentication

Then, add validation for email addresses in the registration form.
```

**Method 2: Checkout and Pull Latest**

```
Please start by:
1. Running: git checkout develop
2. Running: git pull origin develop

Then proceed to add the user profile settings page.
```

**Method 3: Continue Previous Claude Work**

```
Check out the branch 'claude/add-api-endpoints-xyz789' and continue
the work by adding error handling to the endpoints.

First run: git checkout claude/add-api-endpoints-xyz789
```

### Step 4: Verify Branch Checkout

**Watch the activity feed for confirmation:**

```
[12:34:56] Executing git checkout feature/user-authentication
[12:34:57] Switched to branch 'feature/user-authentication'
[12:34:58] ✓ Now working on: feature/user-authentication
[12:35:00] Beginning task execution...
```

**If you see this, the checkout was successful.**

**If you see errors:**
```
[12:34:57] error: pathspec 'feature/user-authentication' did not match any file(s)
```

This means:
- The branch name is incorrect
- The branch doesn't exist in the remote repository
- There may be a typo in your instruction

### Step 5: Confirm Claude is on the Correct Branch

**Ask Claude to verify if needed:**

```
What branch am I currently on? Please run: git branch
```

**Expected response:**
```
You are currently on branch: feature/user-authentication
```

**Or check the session details:**
- Look for "Working branch" in the session information
- Should show your target branch, not a new `claude/` branch
- Verify it matches your intended branch

### Step 6: Complete Your Work on the Existing Branch

**Claude will now:**
- Make changes on the existing branch
- Commit to the existing branch
- Push updates to the existing branch on GitHub

**You avoid:**
- Creating unnecessary new branches
- Needing to merge between Claude branches
- Fragmenting related work across multiple branches

## Working with Different Branch Types

### Continuing Your Own Feature Branches

**Scenario**: You created a feature branch manually and want Claude to continue work on it.

**Approach:**
```
Check out my existing branch 'feature/payment-gateway' and add
Stripe integration to the payment service.

git checkout feature/payment-gateway
git pull origin feature/payment-gateway
```

**Benefits:**
- All payment gateway work stays on one branch
- Clean PR with all related commits
- Easier to track feature progress

### Contributing to Team Branches

**Scenario**: Your team uses a shared `develop` branch for integration.

**Approach:**
```
Start from the develop branch:
git checkout develop
git pull origin develop

Then create comprehensive tests for the authentication module.
```

**Best Practice:**
- Always pull latest changes first
- Ensure you're working with current team code
- Prevents merge conflicts later

### Building on Previous Claude Work

**Scenario**: Claude created a branch previously, and you need additional changes.

**Approach:**
```
Continue work on the existing Claude branch 'claude/api-refactor-abc123':

git checkout claude/api-refactor-abc123
git pull origin claude/api-refactor-abc123

Then add logging to all API endpoints that were refactored.
```

**Why This Works:**
- Keeps all related refactoring in one branch
- Maintains logical commit history
- Single PR for the entire refactoring effort

### Working with Release or Hotfix Branches

**Scenario**: Urgent fix needed on a release branch.

**Approach:**
```
This is an urgent hotfix. Please:
1. git checkout release/v2.0
2. git pull origin release/v2.0
3. Fix the security vulnerability in the authentication module
4. Commit with message: "Security fix: Patch XSS vulnerability"
```

**Critical considerations:**
- Specify exact branch for hotfixes
- Be explicit about urgency
- Include commit message requirements

## Advanced Techniques

### Method 1: Checkout with Verification

**Most reliable approach for critical branches:**

```
Please perform these steps in order:

1. List all available branches: git branch -a
2. Checkout the target branch: git checkout feature/critical-feature
3. Verify current branch: git branch
4. Pull latest changes: git pull origin feature/critical-feature
5. Show recent commits: git log --oneline -5

After verification, proceed with [your task description].
```

**Why use this:**
- Confirms branch exists before checkout
- Ensures you're on correct branch
- Shows recent commit history for context
- Catches issues early

### Method 2: Conditional Branch Creation

**Only create new branch if specific branch doesn't exist:**

```
Check if branch 'feature/new-api' exists.
If it exists: git checkout feature/new-api
If it doesn't exist: git checkout -b feature/new-api

Then implement the REST API endpoints for user management.
```

**When to use:**
- Uncertain if branch exists
- Coordinating with teammates
- Resuming work after time away

### Method 3: Branch with Specific Base

**Work on existing branch but ensure correct parent:**

```
Checkout the feature branch:
git checkout feature/user-profiles

Verify it's based on develop:
git merge-base --is-ancestor develop feature/user-profiles

Then add the profile settings page.
```

**Advanced workflow:**
- Ensures branch has correct base
- Validates branch genealogy
- Prevents integration issues

## Preventing Unwanted Branch Creation

### Explicit "Don't Create New Branch" Instructions

**Make your intention crystal clear:**

```
IMPORTANT: Do not create a new branch.

Instead:
1. git checkout existing-feature-branch
2. Work directly on this branch
3. Commit changes to existing-feature-branch

Task: Add unit tests for the authentication service.
```

### Verify Before Starting

**Pre-flight checklist in your prompt:**

```
Before making any changes:
1. Confirm current branch with: git branch
2. If not on 'develop', switch to it: git checkout develop
3. Verify with: git status

Only after confirmation, proceed with the task.
```

## Expected Results

After successfully working on an existing branch, you should:

- See Claude check out the specified branch at the start of the session
- Have all commits made directly to the existing branch
- Find changes pushed to the existing branch on GitHub
- Avoid creating new `claude/` branches when not needed
- Maintain cleaner repository branch structure
- Have consolidated commit history for related features
- Be able to create PRs from the existing branch

**On GitHub, you'll see:**
- New commits added to the existing branch
- Branch updated timestamp reflects recent work
- No new Claude-created branches (unless specifically requested)

## Troubleshooting

### Common Issue 1
**Problem**: Claude creates a new branch despite instructions to use existing one

**Solution**:
- Make checkout instruction the FIRST item in your prompt
- Use explicit git commands: `git checkout branch-name`
- Start prompt with "IMPORTANT: Do not create a new branch"
- Verify the existing branch name is spelled correctly
- Check that the branch exists on the remote repository
- If issue persists, manually verify the branch after session starts

### Common Issue 2
**Problem**: Branch checkout fails with "pathspec did not match" error

**Solution**:
- Verify branch name exactly matches (case-sensitive)
- Check that branch exists: visit GitHub branches page
- Ensure no typos in branch name
- Try: `git checkout -b branch-name origin/branch-name` to track remote
- Pull all branches first: `git fetch --all`
- If branch is very new, confirm it's been pushed to remote

### Common Issue 3
**Problem**: Claude checks out branch but it's outdated

**Solution**:
- Always include `git pull origin branch-name` after checkout
- Explicitly instruct: "Pull the latest changes before starting"
- Check commit history to verify you're up to date
- If work is based on old commits, rebase or merge as needed
- Consider: `git pull --rebase origin branch-name` for cleaner history

### Common Issue 4
**Problem**: Working on existing branch causes merge conflicts

**Solution**:
- Pull latest changes before Claude starts working
- Ensure base branch is merged into feature branch
- If conflicts occur, have Claude run: `git status`
- Instruct Claude to resolve conflicts or abort changes
- May need to manually resolve complex conflicts
- Prevention: Keep branches up to date with base branch

### Common Issue 5
**Problem**: Not sure which branch Claude is actually working on

**Solution**:
- Ask Claude: "What branch are you on? Run: git branch"
- Check session details in UI for working branch indicator
- Review activity feed for checkout commands
- Look for "Switched to branch X" messages
- Verify on GitHub after first commits appear
- Request branch confirmation before proceeding with work

### Common Issue 6
**Problem**: Need to switch branches mid-session

**Solution**:
- You can instruct Claude to switch at any point
- Example: "Please checkout branch feature/other and continue work there"
- Ensure any uncommitted work is committed first
- Claude can: `git add . && git commit -m "WIP" && git checkout other-branch`
- Switch branches as many times as needed during a session
- Each checkout will be visible in the activity feed

### Common Issue 7
**Problem**: Existing branch is protected and Claude can't push

**Solution**:
- Check branch protection rules on GitHub
- Protected branches may prevent direct pushes
- Claude should create a new branch from the protected branch instead
- Modify instructions: "Create new branch from develop, not main"
- Or work locally and create PR manually
- Consider temporarily adjusting protection rules if appropriate

## Additional Tips

### Clear Communication is Key

**Be explicit in your instructions:**

❌ **Vague:**
```
Work on the feature branch
```

✓ **Explicit:**
```
git checkout feature/user-authentication
git pull origin feature/user-authentication
Then add email validation.
```

### Verify Branch State First

**Good practice for important branches:**

```
Before starting work:
1. Show me the current branch: git branch
2. Show recent commits: git log --oneline -5
3. Show branch status: git status

After confirmation, [proceed with task].
```

### Document Your Branch Strategy

**Include branch context in prompts:**

```
Context: We use GitFlow with 'develop' as integration branch.

Please:
1. Checkout develop: git checkout develop
2. Pull latest: git pull origin develop
3. Add the new feature

All work should be on develop, not main.
```

### Combine with PR Workflow

**Complete workflow for existing branches:**

```
1. Checkout existing branch: git checkout feature/payments
2. Pull latest: git pull origin feature/payments
3. Add Stripe webhook handling
4. Commit with message: "Add Stripe webhook endpoints"
5. Push changes: git push origin feature/payments

Note: This branch already has an open PR #123, so just update it.
```

### Branch Naming Context Helps

**When working on conventionally named branches:**

```
Our team uses:
- feature/* for new features
- bugfix/* for bug fixes
- hotfix/* for urgent production fixes

Please checkout feature/user-dashboard and add the analytics widget.
```

### Consider Branch Lifecycle

**Be aware of branch age:**

```
This branch 'feature/legacy-migration' has been active for 2 months.

Before working:
1. git checkout feature/legacy-migration
2. git merge develop (to include latest changes)
3. Resolve any conflicts
4. Then proceed with migration tasks
```

## Related Articles
- KB-014: How to understand which branch Claude Code is working on
- KB-031: How to create a new branch for a specific task
- KB-015: How to view changes made by Claude Code in your repository
- KB-033: How to create a pull request automatically from completed work
- KB-016: How to start a new Claude Code session
- KB-022: How to intervene and guide Claude during task execution

## Sources
1. Claude Code Web - Feature Request for Branch Selection - https://github.com/anthropics/claude-code/issues/10018 (Accessed: November 16, 2025)
2. Claude Code on the web - Official Support - https://support.claude.com/en/articles/12618689-claude-code-on-the-web (Accessed: November 16, 2025)
3. Git Checkout Documentation - https://git-scm.com/docs/git-checkout (Accessed: November 16, 2025)
4. GitHub Branch Management Best Practices - https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-branches (Accessed: November 16, 2025)

Google Search Suggestion: `"Claude Code Web" existing branch checkout workflow 2025`

---
*This article is part of the Claude Code Web knowledge base*
