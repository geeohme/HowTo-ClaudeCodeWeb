# How to create a new branch for a specific task

**Article ID**: KB-031
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10 minutes

## Overview
Claude Code Web automatically creates new Git branches when you start tasks, isolating changes from your main codebase. This guide explains how branch creation works, how to influence branch naming and base selection, and best practices for managing task-specific branches across multiple concurrent workflows.

## Prerequisites
- Connected GitHub repository (KB-004)
- Understanding of basic Git branching concepts
- Familiarity with starting sessions (KB-016)
- Knowledge of how Claude uses branches (KB-014)

## Understanding Automatic Branch Creation

### How Claude Creates Branches

**When You Start a Task:**

1. **Base Branch Selection**: Claude identifies your repository's default branch (typically `main` or `master`)
2. **Branch Creation**: A new branch is created with an auto-generated name
3. **Environment Initialization**: The branch is checked out in an isolated environment
4. **Task Execution**: All changes are made on this new branch
5. **Push to Remote**: The branch is automatically pushed to GitHub
6. **PR Readiness**: Changes are ready for pull request creation

**This happens automatically**—no manual git commands required.

### Default Branch Naming Pattern

Claude creates branches following this convention:

```
claude/[task-description]-[unique-id]
```

**Examples:**
```
claude/add-user-authentication-a1b2c3
claude/fix-calculation-bug-x7y8z9
claude/refactor-api-endpoints-m4n5p6
claude/update-documentation-q1w2e3
```

**Name Components:**
- `claude/` - Prefix identifying Claude-created branches
- `task-description` - Derived from your task description
- `unique-id` - Random identifier preventing naming conflicts

## Steps to Create a Branch for a Task

### Step 1: Prepare Your Task Description

Before starting a task, prepare a clear description that will influence the branch name:

**Good Task Descriptions (create clear branch names):**
```
✓ "Add email validation to registration form"
   → claude/add-email-validation-registration-form-abc123

✓ "Fix null pointer exception in checkout"
   → claude/fix-null-pointer-exception-checkout-xyz789

✓ "Refactor user service for better performance"
   → claude/refactor-user-service-better-performance-def456
```

**Poor Task Descriptions (create unclear branch names):**
```
✗ "Update code"
   → claude/update-code-rst901

✗ "Fix stuff"
   → claude/fix-stuff-uvw234

✗ "Work on the project"
   → claude/work-on-the-project-hij567
```

### Step 2: Verify Base Branch Settings

**Before starting your task:**

**Check Repository Default Branch:**
1. Visit your repository on GitHub
2. Navigate to Settings → Branches
3. Confirm default branch is correct
4. Claude will branch from this default

**If you need to change the default branch:**
```
GitHub Repository → Settings → Branches → Default branch
Select the branch you want Claude to use as base
```

**Note**: This affects all new tasks unless specified otherwise.

### Step 3: Start Your Task

**Navigate to Claude Code Web:**
```
https://claude.com/code
```

**Create the Task:**
1. Select your repository
2. Click "New Task" or "Start Session"
3. Enter your task description
4. (Optional) Specify base branch if interface allows
5. Click "Start Task"

**Watch for Branch Creation:**
```
Creating session...
Connecting to repository...
Creating new branch from main...
Branch created: claude/add-email-validation-abc123
Checking out branch...
Ready to begin task execution
```

### Step 4: Monitor Branch Creation

**During Task Initialization:**

The activity feed shows branch creation progress:

```
[12:34:56] Analyzing repository structure...
[12:34:58] Creating branch from main
[12:35:00] ✓ Branch created: claude/add-email-validation-abc123
[12:35:02] Switched to branch claude/add-email-validation-abc123
[12:35:03] Beginning task execution...
```

**Session Details Show:**
- **Working Branch**: `claude/add-email-validation-abc123`
- **Base Branch**: `main`
- **Branch Status**: `created, not yet pushed`

### Step 5: Verify Branch on GitHub

**Once Claude begins making changes:**

1. Navigate to your repository on GitHub
2. Click the branch dropdown
3. Look for the newly created branch
4. You should see it listed with recent activity

**Branch appears after first push:**
- Initial commits are made locally
- Branch is pushed to remote after first changes
- Usually within first few minutes of task execution

## Advanced Branch Management

### Specifying a Different Base Branch

**Method 1: Through Task Description**

Include the base branch in your task description:

```
"Starting from the 'develop' branch, add user profile settings page"
```

Claude will understand and create the branch from `develop` instead of `main`.

**Method 2: Change Repository Default**

For all future tasks to branch from a different base:

1. Go to GitHub → Repository → Settings → Branches
2. Change default branch to `develop` or your preferred branch
3. All new Claude tasks will now branch from this default

**Method 3: Use Existing Branch**

If you want Claude to continue work on an existing branch:

```
"Continue work on branch feature/user-profiles by adding settings page"
```

Claude can check out and work on existing branches when specified.

### Creating Multiple Branches for Related Tasks

**Parallel Feature Development:**

You can create multiple branches simultaneously for different aspects:

```
Task 1: "Add user authentication API endpoints"
→ claude/add-user-authentication-api-endpoints-a1b2c3

Task 2: "Create login UI components"
→ claude/create-login-ui-components-x7y8z9

Task 3: "Write authentication tests"
→ claude/write-authentication-tests-m4n5p6
```

**Each task gets its own isolated branch**:
- No conflicts between parallel tasks
- Can be merged independently
- Different PR review workflows

### Branch Naming Strategies

**For Large Features:**

Break into smaller tasks with descriptive names:

```
claude/feature-payment-gateway-stripe-integration-abc123
claude/feature-payment-gateway-ui-components-def456
claude/feature-payment-gateway-error-handling-ghi789
claude/feature-payment-gateway-testing-jkl012
```

**For Bug Fixes:**

Reference issue numbers in task descriptions:

```
"Fix issue #123: Memory leak in image processing"
→ claude/fix-issue-123-memory-leak-image-processing-xyz789
```

**For Refactoring:**

Be specific about what's being refactored:

```
"Refactor database layer to use connection pooling"
→ claude/refactor-database-layer-connection-pooling-mno345
```

## Working with Created Branches

### Viewing Branch Information

**In Claude Code Web:**
- Session page shows working branch name
- Activity feed logs all git operations
- Branch status indicates if pushed to remote

**On GitHub:**
- Branches page lists all Claude branches
- Each shows commits, last update time
- Compare with base branch to see changes

### Managing Branch Lifecycle

**Active Development:**
```
1. Claude creates branch
2. Makes commits as task progresses
3. Pushes to remote periodically
4. Continues until task complete
```

**Task Completion:**
```
1. Final commits made
2. Branch pushed to remote
3. Pull request created (automatic or manual)
4. Ready for review
```

**After Merge:**
```
1. Review and merge PR on GitHub
2. Delete branch (manual or automatic)
3. Branch cleanup keeps repository tidy
```

### Branch Protection Considerations

**If your repository has branch protection rules:**

Claude respects these rules:
- Cannot push directly to protected branches (like `main`)
- Creates feature branches as designed
- PRs must pass required checks before merge
- Review requirements are enforced

**This is why Claude creates branches**—it's the proper workflow for protected repositories.

## Expected Results

After understanding branch creation for tasks, you should:
- See a new uniquely-named branch created for each task
- Have branches automatically based on your default branch
- Find branches on GitHub shortly after task starts
- Understand the branch naming convention
- Be able to influence branch names through task descriptions
- Know how to specify different base branches
- Have isolated environments for parallel task development
- Be confident that your main branch remains untouched

## Troubleshooting

### Common Issue 1
**Problem**: Branch name is unclear or too generic
**Solution**:
- Provide more descriptive task descriptions
- Be specific about what the task accomplishes
- Use action verbs: "Add," "Fix," "Refactor," "Update"
- Include key component names in the description
- Example: Instead of "Update forms," use "Add validation to checkout form"

### Common Issue 2
**Problem**: Branch created from wrong base branch
**Solution**:
- Cancel the task immediately (KB-010)
- Check your repository's default branch setting on GitHub
- Specify the correct base branch in task description
- Or change the default branch for future tasks
- Restart the task with correct parameters

### Common Issue 3
**Problem**: Can't find Claude's branch on GitHub
**Solution**:
- Wait a few minutes—branch is pushed after first commits
- Refresh the GitHub branches page
- Check that task has started executing (not just initializing)
- Look in "All branches" not just "Active branches"
- Verify task didn't fail during initialization
- Check activity feed for branch creation confirmation

### Common Issue 4
**Problem**: Want to merge multiple Claude branches before creating PR
**Solution**:
- This is an advanced workflow
- Consider combining tasks into one session instead
- Or create a feature branch manually and specify it as base
- Have Claude create sub-branches from your feature branch
- Merge Claude branches into feature branch before main PR

### Common Issue 5
**Problem**: Too many Claude branches accumulating
**Solution**:
- Enable automatic branch deletion on GitHub after PR merge
- Periodically clean up merged branches
- Use branch naming to identify old branches
- This is normal—one branch per task is expected behavior
- Consider it temporary scaffolding, not permanent structure

### Common Issue 6
**Problem**: Need to continue work on a branch Claude created
**Solution**:
- Specify the branch name in a new task description
- Example: "Continue on branch claude/feature-xyz-123 by adding tests"
- Or work on the branch manually and let Claude create a new one
- Multiple approaches depending on your workflow preference

### Common Issue 7
**Problem**: Branch naming doesn't follow team conventions
**Solution**:
- Provide detailed task descriptions that reflect your conventions
- Example: "JIRA-123: Add user authentication" creates branch referencing ticket
- Include project prefixes in task descriptions
- Team conventions in task descriptions flow to branch names
- For strict naming, may need to rename branches manually or via git hooks

## Additional Tips

### Branch Creation Best Practices

**Clear Task Scoping:**
- One task = one branch = one purpose
- Don't combine unrelated changes in a single task
- Smaller, focused tasks create cleaner branches
- Easier to review, merge, and rollback if needed

**Task Description Templates:**

Use consistent patterns for task descriptions:

```
For features:
"Add [feature name] to [component]"

For bugs:
"Fix [issue description] in [component]"

For refactoring:
"Refactor [component] to [improvement]"

For tests:
"Add tests for [feature/component]"

For documentation:
"Update docs for [topic/feature]"
```

### Parallel Branch Management

**When running multiple tasks:**

**Track branches systematically:**
- Use task/session list to see all active branches
- Note which branch corresponds to which task
- Monitor progress independently
- Review and merge in logical order

**Consider dependencies:**
- Independent tasks can have parallel branches
- Dependent tasks should complete sequentially
- Merge foundational changes before building on them

### Integration with Team Workflows

**Git Flow:**
- Set `develop` as default branch
- Claude creates feature branches from develop
- Merge Claude branches to develop
- Release branches managed separately

**GitHub Flow:**
- Keep `main` as default (simplest)
- Claude creates feature branches from main
- Merge via PR with required reviews
- Deploy from main

**Trunk-Based Development:**
- Short-lived branches from main
- Quick task completion and merge
- Frequent integration
- Perfect for Claude's workflow

### Branch Cleanup Automation

**On GitHub:**
```
Settings → General → Pull Requests
☑ Automatically delete head branches
```

This automatically removes branches after PR merge, keeping your repository clean.

**Local Cleanup:**

After pulling merged changes:
```bash
git fetch --prune
git branch -d branch-name
```

### Naming Conventions for Complex Projects

**Include context in task descriptions:**

```
"Frontend: Add user profile component"
→ claude/frontend-add-user-profile-component-abc123

"Backend: Create user profile API"
→ claude/backend-create-user-profile-api-def456

"Database: Add user profile schema"
→ claude/database-add-user-profile-schema-ghi789
```

This creates self-documenting branch names that clarify purpose and scope.

## Related Articles
- KB-014: How to understand which branch Claude Code is working on
- KB-016: How to start a new Claude Code session
- KB-015: How to view changes made by Claude Code in your repository
- KB-004: How to connect your first GitHub repository to Claude Code Web
- KB-006: How to create your first coding task in Claude Code Web
- KB-033: How to create pull requests automatically (Coming Soon)
- KB-036: How to handle merge conflicts (Coming Soon)

## Sources
1. Claude Code on the web - Official Documentation - https://www.claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
2. Claude Code Best Practices - Anthropic Engineering - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
3. Git Branching Basics - https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging (Accessed: November 16, 2025)
4. GitHub Branch Management - https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-branches (Accessed: November 16, 2025)
5. Cooking with Claude Code: The Complete Guide - https://www.siddharthbharath.com/claude-code-the-complete-guide/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
