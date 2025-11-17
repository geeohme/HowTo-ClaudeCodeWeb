# How to use git worktrees for parallel sessions in the same repository

**Article ID**: KB-088
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-25 minutes

## Overview
Git worktrees allow you to maintain multiple working directories from a single repository, each checked out to different branches. This enables you to run multiple Claude Code Web sessions in parallel on the same repository without conflicts, perfect for working on multiple features simultaneously or testing different approaches to the same problem.

## Prerequisites
- Active Claude Code Web account with Pro or Max plan
- Repository connected to Claude Code Web
- Basic understanding of Git branches and repository structure
- Familiarity with command-line Git operations
- Related: [KB-004: How to connect your first repository](#)
- Related: [KB-014: How to understand the working branch](#)

## Steps

### Step 1: Understand Git Worktrees

Git worktrees create separate working directories that share the same Git repository data. Each worktree can have a different branch checked out, allowing independent work without switching branches.

**Key benefits for Claude Code Web:**
- Run multiple Claude sessions on different features simultaneously
- Each session has isolated file state, preventing interference
- All worktrees share the same Git history and remote connections
- Changes in one worktree don't affect others until merged

### Step 2: Create Your First Worktree

Navigate to your repository and create a worktree for a new feature branch:

```bash
# From your main repository directory
cd /path/to/your/repository

# Create a worktree for a new feature
git worktree add ../repo-feature-auth feature/auth

# Or create a worktree with a new branch from main
git worktree add -b feature/new-api ../repo-new-api main
```

**Command breakdown:**
- `git worktree add`: Creates a new worktree
- `../repo-feature-auth`: Path where the new worktree will be created
- `feature/auth`: Branch to check out (creates if doesn't exist with `-b`)
- `main`: Base branch for new branch (optional)

**Best practice for organization:** Create worktrees in a dedicated parent directory alongside your main repository:

```bash
# Recommended structure
~/projects/
  ├── myapp/              # Main repository
  ├── myapp-feature-1/    # Worktree for feature 1
  ├── myapp-feature-2/    # Worktree for feature 2
  └── myapp-bugfix/       # Worktree for bug fix
```

### Step 3: Connect Each Worktree to Claude Code Web

For each worktree you want to use with Claude Code Web:

1. Open Claude Code Web in your browser
2. Navigate to the repository connection page
3. Connect the worktree directory as if it were a separate repository
4. Claude Code Web will recognize it shares the same Git repository

**Important:** Each worktree appears as a separate workspace in Claude Code Web, but they all share the same underlying Git data.

### Step 4: Start Parallel Sessions

Now you can run multiple Claude Code Web sessions simultaneously:

```bash
# List all your worktrees to see what's available
git worktree list
```

Example output:
```
/home/user/projects/myapp              abc123d [main]
/home/user/projects/myapp-feature-1    def456e [feature/auth]
/home/user/projects/myapp-feature-2    ghi789f [feature/api]
```

**Workflow example:**
1. Session 1 in main worktree: Ask Claude to review and refactor existing code
2. Session 2 in feature-auth worktree: Ask Claude to implement authentication
3. Session 3 in feature-api worktree: Ask Claude to build a new API endpoint

All three sessions run independently without conflicts.

### Step 5: Manage Your Worktrees

**List all worktrees:**
```bash
git worktree list
```

**Remove a worktree when done:**
```bash
# Remove the worktree directory
git worktree remove ../myapp-feature-1

# Or remove and delete even if there are uncommitted changes
git worktree remove --force ../myapp-feature-1
```

**Move a worktree to a new location:**
```bash
git worktree move ../myapp-feature-1 ../new-location/myapp-feature-1
```

**Prune stale worktree metadata:**
```bash
# Clean up metadata for manually deleted worktrees
git worktree prune
```

### Step 6: Merge Work from Different Worktrees

When Claude completes work in different worktrees, merge them back to main:

```bash
# From your main worktree
cd /path/to/main/worktree

# Merge feature from another worktree
git merge feature/auth

# Or create pull requests from each branch
gh pr create --base main --head feature/auth
```

**Best practice:** Use pull requests for better visibility and code review, especially when managing multiple parallel Claude sessions.

### Step 7: Clean Up When Done

Once features are merged, clean up worktrees:

```bash
# Remove the worktree
git worktree remove ../myapp-feature-1

# Delete the merged branch
git branch -d feature/auth

# Push deletion to remote if needed
git push origin --delete feature/auth
```

## Expected Results

After setting up git worktrees for parallel Claude Code Web sessions, you should be able to:

- See multiple worktrees listed with `git worktree list`
- Connect each worktree to Claude Code Web as a separate workspace
- Run multiple Claude sessions simultaneously on different branches
- Make changes in one worktree without affecting others
- Merge completed work from different sessions back to main
- Switch between sessions without losing context or progress

Each Claude session will have complete isolation, preventing conflicts and allowing true parallel development.

## Troubleshooting

### Common Issue 1: Cannot Create Worktree - Branch Already Checked Out
**Problem**: Error message "fatal: 'feature/auth' is already checked out at '/path/to/worktree'"
**Solution**: Each branch can only be checked out in one worktree at a time. Either:
- Use a different branch for the new worktree
- Remove the existing worktree first with `git worktree remove`
- Create a new branch based on the desired branch: `git worktree add -b feature/auth-v2 ../new-worktree feature/auth`

### Common Issue 2: Worktree Directory Already Exists
**Problem**: Cannot create worktree because the target directory already exists
**Solution**:
```bash
# Remove the directory first
rm -rf ../myapp-feature-1

# Or choose a different path
git worktree add ../myapp-feature-1-new feature/auth
```

### Common Issue 3: Claude Code Web Shows "Not a Git Repository"
**Problem**: Worktree not recognized as a valid repository in Claude Code Web
**Solution**:
- Verify the worktree was created successfully with `git worktree list`
- Check that the `.git` file exists in the worktree directory (it's a file, not a directory)
- Reconnect the repository in Claude Code Web settings
- Ensure you have proper permissions to access the worktree directory

### Common Issue 4: Changes Not Visible Across Worktrees
**Problem**: Commits made in one worktree don't appear in another
**Solution**: This is expected behavior. Each worktree shows only its checked-out branch. To see changes:
```bash
# Fetch latest changes (if from remote)
git fetch origin

# Switch to the branch or merge it
git checkout feature/auth
# or
git merge feature/auth
```

### Common Issue 5: Stale Worktree References
**Problem**: Manually deleted worktree directory, but git still thinks it exists
**Solution**:
```bash
# Clean up stale worktree administrative files
git worktree prune

# Verify cleanup
git worktree list
```

## Additional Tips

- **Naming convention**: Use descriptive worktree directory names that match the branch or feature: `myapp-feature-auth`, `myapp-bugfix-123`, etc.
- **Shared dependencies**: All worktrees share the same `.git` directory, so Git objects and history are not duplicated. However, `node_modules`, `venv`, or other dependency directories are separate per worktree.
- **Install dependencies per worktree**: After creating a worktree, run `npm install`, `pip install -r requirements.txt`, or equivalent to set up dependencies for that workspace.
- **Disk space**: While Git data is shared, each worktree has its own working files, so monitor disk space when creating many worktrees.
- **Lock worktrees**: Use `git worktree lock ../myapp-feature` to prevent accidental removal of important worktrees.
- **Parallel limits**: Check your Claude Code Web plan's concurrent session limits before creating many worktrees. See [KB-005: Understand Pro and Max limits](#).
- **Branch cleanup**: Regularly clean up merged feature branches and their worktrees to avoid clutter.
- **Session management**: Use descriptive session names in Claude Code Web that match your worktree/branch names for easier tracking.
- **Commit frequently**: Since each worktree is independent, commit work in each session to avoid losing changes.
- **Background tasks**: Long-running tasks can continue in one worktree while you work in another, maximizing productivity.

## Related Articles
- KB-004: How to connect your first repository
- KB-014: How to understand the working branch
- KB-031: How to create a new branch for a specific task
- KB-037: How to work on existing branches instead of creating new ones
- KB-027: How to manage concurrent tasks effectively
- KB-029: How to track progress across multiple parallel sessions

## Sources
- Git Official Documentation - git-worktree - https://git-scm.com/docs/git-worktree (Accessed: November 16, 2025)
- Git Kernel Documentation - git-worktree(1) - https://www.kernel.org/pub/software/scm/git/docs/git-worktree.html (Accessed: November 16, 2025)
- DEV Community - Running Multiple Claude Code Sessions in Parallel with git worktree - https://dev.to/datadeer/part-2-running-multiple-claude-code-sessions-in-parallel-with-git-worktree-165i (Accessed: November 16, 2025)
- incident.io Blog - How we're shipping faster with Claude Code and Git Worktrees - https://incident.io/blog/shipping-faster-with-claude-code-and-git-worktrees (Accessed: November 16, 2025)
- Medium - Mastering Git Worktrees with Claude Code for Parallel Development Workflow - https://medium.com/@dtunai/mastering-git-worktrees-with-claude-code-for-parallel-development-workflow-41dc91e645fe (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
