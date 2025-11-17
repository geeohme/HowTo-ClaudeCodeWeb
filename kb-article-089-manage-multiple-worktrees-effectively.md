# How to manage multiple worktrees effectively

**Article ID**: KB-089
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview

Git worktrees enable you to check out multiple branches from the same repository into separate directories simultaneously. When combined with Claude Code Web, this powerful feature allows you to run multiple Claude instances in parallel across different branches, dramatically improving your development workflow. This guide teaches you how to set up, organize, and manage multiple worktrees effectively for maximum productivity.

## Prerequisites

- Familiarity with Git commands and branch management
- Active Claude Code Web session
- KB-014: Understanding your working branch
- KB-027: How to manage concurrent tasks effectively
- A Git repository with multiple branches or tasks to work on

## Steps

### Step 1: Understand Worktree Fundamentals

Git worktrees create additional working directories linked to your main repository. Each worktree has:

- **Isolated files**: Separate working directory with its own checked-out branch
- **Shared history**: All worktrees share the same Git history, reflog, and remote connections
- **Independent state**: Changes in one worktree don't affect others until merged

This architecture makes worktrees ideal for parallel development with Claude Code, where each Claude instance can work in its own sandbox without conflicts.

### Step 2: Set Up Your Worktree Directory Structure

Create a consistent organization structure for your worktrees. Use a centralized parent directory:

```bash
# Navigate to your main repository
cd /path/to/your/main-repo

# Create a worktrees directory alongside your main repo
cd ..
mkdir worktrees

# Verify your structure
ls -la
# Should show: main-repo/ worktrees/
```

This structure keeps your main repository clean while organizing all worktrees in one location.

### Step 3: Create Named Worktrees with Convention

Establish a naming convention that makes worktrees instantly recognizable:

```bash
# For feature development
git worktree add ../worktrees/feature-user-auth feature/user-auth

# For bug fixes
git worktree add ../worktrees/fix-login-error fix/login-error

# For code reviews
git worktree add ../worktrees/review-pr-123 origin/pull/123/head

# For experimental work
git worktree add ../worktrees/experiment-new-api -b experiment/new-api
```

**Naming conventions to adopt:**
- `feature-<description>`: New features
- `fix-<issue>`: Bug fixes
- `review-<identifier>`: Pull request reviews
- `experiment-<idea>`: Experimental branches
- `hotfix-<urgent>`: Production hotfixes

### Step 4: List and Track Active Worktrees

Regularly audit your worktrees to maintain awareness:

```bash
# List all worktrees with details
git worktree list

# Output example:
# /path/to/main-repo         abc123 [main]
# /path/to/worktrees/feature-auth  def456 [feature/user-auth]
# /path/to/worktrees/fix-login     ghi789 [fix/login-error]

# Get detailed information including lock status
git worktree list --porcelain
```

Create an alias for quick access:

```bash
# Add to your shell configuration (.bashrc, .zshrc)
alias gwl='git worktree list'
alias gwp='git worktree list --porcelain'
```

### Step 5: Connect Claude Code to Specific Worktrees

Connect each Claude Code session to a different worktree for parallel development:

1. **Open a new Claude Code session** for each worktree
2. **Connect to the worktree directory** (not the main repository):
   - In Claude Code Web, connect to `/path/to/worktrees/feature-user-auth`
   - This isolates Claude's work to that specific branch

3. **Give clear, branch-specific instructions**:
```
"In this worktree (feature/user-auth), implement the OAuth2
authentication flow. Focus only on the auth module."
```

4. **Monitor progress independently** - Each Claude instance operates in isolation

### Step 6: Implement Worktree Cleanup Routine

Maintain a clean worktree environment with regular cleanup:

```bash
# Remove a completed worktree (commits work first!)
git worktree remove worktrees/feature-user-auth

# Force remove if there are uncommitted changes (use cautiously)
git worktree remove --force worktrees/experiment-failed

# Prune deleted worktrees from Git's internal tracking
git worktree prune

# Check for orphaned worktrees
git worktree list
# Manually inspect and clean any stale entries
```

**Recommended cleanup schedule:**
- Daily: Review and remove completed worktrees
- Weekly: Prune orphaned worktrees
- Monthly: Audit all active worktrees for relevance

### Step 7: Handle Branch Protection and Locks

Understand Git's automatic protections:

```bash
# Git automatically prevents checking out the same branch in multiple worktrees
# This will fail if 'main' is already checked out elsewhere:
git worktree add ../worktrees/main-backup main
# Error: 'main' is already checked out at '/path/to/main-repo'

# Create a new branch from main instead:
git worktree add ../worktrees/main-backup -b main-backup main

# Check locked worktrees (locked during certain operations)
git worktree list --porcelain | grep "locked"

# Unlock a worktree if needed
git worktree unlock worktrees/feature-name
```

### Step 8: Sync Work Between Worktrees

Coordinate changes across multiple worktrees:

```bash
# From any worktree, fetch latest changes
cd /path/to/worktrees/feature-auth
git fetch origin

# View changes from main while in your feature worktree
git log main..HEAD  # Commits in current branch not in main
git log HEAD..main  # Commits in main not in current branch

# Merge or rebase from main
git merge main
# or
git rebase main

# Push changes from worktree
git push origin feature/user-auth
```

### Step 9: Optimize for Parallel Claude Code Sessions

Set up multiple Claude Code sessions for maximum efficiency:

**Strategy A: Feature Parallelization**
- Worktree 1: Claude working on frontend feature
- Worktree 2: Claude working on backend API
- Worktree 3: You reviewing PR in another branch

**Strategy B: Review + Development**
- Worktree 1: Claude generating unit tests
- Worktree 2: Claude refactoring code
- Worktree 3: You conducting code review

**Strategy C: Hotfix + Feature**
- Worktree 1: Claude fixing production bug
- Worktree 2: Claude continuing feature development
- Main repo: Clean state for emergency access

### Step 10: Create Helper Scripts for Worktree Management

Automate common worktree operations:

```bash
# Create a script: ~/scripts/worktree-create.sh
#!/bin/bash
# Usage: worktree-create.sh <type> <name>

TYPE=$1
NAME=$2
BRANCH="${TYPE}/${NAME}"
WORKTREE_PATH="../worktrees/${TYPE}-${NAME}"

git worktree add "$WORKTREE_PATH" -b "$BRANCH"
echo "Worktree created at: $WORKTREE_PATH"
echo "Branch: $BRANCH"

# Make it executable
chmod +x ~/scripts/worktree-create.sh

# Usage examples:
# ./worktree-create.sh feature user-auth
# ./worktree-create.sh fix login-error
```

Create a cleanup script:

```bash
# Create a script: ~/scripts/worktree-cleanup.sh
#!/bin/bash
# Removes merged worktrees

for worktree in $(git worktree list --porcelain | grep "worktree" | cut -d' ' -f2); do
  if [ "$worktree" != "$(git rev-parse --show-toplevel)" ]; then
    cd "$worktree"
    BRANCH=$(git branch --show-current)

    # Check if branch is merged into main
    if git merge-base --is-ancestor HEAD origin/main 2>/dev/null; then
      echo "Removing merged worktree: $worktree ($BRANCH)"
      cd ..
      git worktree remove "$worktree"
    fi
  fi
done

git worktree prune
echo "Cleanup complete!"
```

## Expected Results

After implementing this worktree management system:

- **Multiple Claude sessions** running simultaneously on different branches without conflicts
- **Organized directory structure** where all worktrees are easily discoverable
- **Clear naming conventions** that instantly communicate each worktree's purpose
- **Faster development cycles** through parallel task execution
- **Clean repository state** with regular maintenance preventing accumulation of stale worktrees
- **Seamless switching** between different contexts without branch checkout delays

You should be able to run `git worktree list` and see a clean, organized list of active worktrees, each serving a specific purpose in your development workflow.

## Troubleshooting

### Issue 1: Cannot Remove Worktree - "Contains Modified or Untracked Files"

**Problem**: `git worktree remove` fails because of uncommitted changes
**Solution**:
```bash
# Option 1: Save your work first
cd /path/to/worktrees/feature-name
git add .
git commit -m "WIP: Save current state"
cd ../..
git worktree remove worktrees/feature-name

# Option 2: Force remove (data loss warning!)
git worktree remove --force worktrees/feature-name
```

### Issue 2: "Branch Already Checked Out" Error

**Problem**: Cannot create worktree because the branch is checked out elsewhere
**Solution**:
```bash
# Find where the branch is checked out
git worktree list | grep "branch-name"

# Option 1: Use that existing worktree
cd /path/to/existing/worktree

# Option 2: Create a new branch from the same commit
git worktree add ../worktrees/new-name -b new-branch-name existing-branch

# Option 3: Checkout a different branch in the main repo
cd /path/to/main-repo
git checkout different-branch
```

### Issue 3: Claude Code Cannot Access Worktree Files

**Problem**: Claude reports file access errors or cannot see worktree contents
**Solution**:
- Ensure you connected Claude Code to the worktree directory, not the main repository
- Verify file permissions: `ls -la /path/to/worktrees/feature-name`
- Check that the worktree wasn't removed while Claude was connected
- Reconnect the repository in Claude Code Web interface

### Issue 4: Disk Space Issues with Many Worktrees

**Problem**: Multiple worktrees consuming significant disk space
**Solution**:
```bash
# Check worktree sizes
du -sh worktrees/*

# Remove node_modules from inactive worktrees
find worktrees/*/node_modules -type d -prune -exec rm -rf {} +

# Use sparse checkout for large repositories
git sparse-checkout init --cone
git sparse-checkout set <directory>

# Implement aggressive cleanup policy
# Remove worktrees older than 7 days with no commits
```

### Issue 5: Lost Track of Worktree Locations

**Problem**: Worktrees created in different locations, hard to find
**Solution**:
```bash
# List all worktrees with full paths
git worktree list

# Create a centralized tracking command
alias findworktrees='git worktree list --porcelain | grep "worktree" | cut -d" " -f2'

# Reorganize: move worktrees to standard location
git worktree move old/path/worktree-name new/path/worktree-name
```

## Additional Tips

- **Establish team conventions**: If working in a team, agree on worktree naming and location standards
- **Use relative paths**: When creating worktrees, use relative paths (`../worktrees/name`) for portability
- **Leverage tab completion**: Configure shell completion for faster worktree commands
- **Monitor with tools**: Use tools like `tmux` or terminal multiplexers to manage multiple Claude Code sessions across worktrees
- **Document active worktrees**: Keep a simple text file listing your active worktrees and their purposes
- **Automate with git hooks**: Create post-checkout hooks to set up environment variables per worktree
- **Backup before experimentation**: Worktrees are perfect for experiments, but ensure important work is committed
- **Use worktrees for CI/CD**: Build different branches simultaneously without multiple repository clones
- **Combine with git stash**: Use `git stash` to quickly switch focus between worktrees without losing work

## Related Articles

- KB-014: How to understand your working branch
- KB-027: How to manage concurrent tasks effectively
- KB-031: How to create a new branch for a specific task
- KB-036: How to handle merge conflicts identified by Claude
- KB-037: How to work on existing branches instead of creating new ones
- KB-060: How to handle parallel bug fixes

## Sources

- Anthropic Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code Common Workflows - https://docs.claude.com/en/docs/claude-code/common-workflows (Accessed: November 16, 2025)
- Git Official Documentation - git-worktree - https://git-scm.com/docs/git-worktree (Accessed: November 16, 2025)
- incident.io Engineering Blog - "How we're shipping faster with Claude Code and Git Worktrees" - https://incident.io/blog/shipping-faster-with-claude-code-and-git-worktrees (Accessed: November 16, 2025)
- Multiple community articles and best practices from 2025 development workflows

---
*This article is part of the Claude Code Web knowledge base*
