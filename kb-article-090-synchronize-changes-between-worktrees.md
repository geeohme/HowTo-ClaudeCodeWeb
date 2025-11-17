# How to synchronize changes between worktrees

**Article ID**: KB-090
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Git worktrees allow you to have multiple working directories from the same repository, enabling parallel development with Claude Code Web. Understanding how to synchronize changes between worktrees is essential for efficiently managing concurrent tasks. The good news: most synchronization happens automatically because all worktrees share the same underlying Git repository database. This guide shows you how to leverage this architecture and handle the few operations that require manual intervention.

## Prerequisites
- Basic understanding of Git branches and commits
- Familiarity with git worktrees (see related articles)
- Active Claude Code Web session
- Multiple worktrees created from the same repository

## Understanding Worktree Synchronization

### Automatic Synchronization

All worktrees share the same `.git` directory, which means:

- **Commits**: Any commit made in one worktree is immediately available in all other worktrees
- **Branches**: Creating, deleting, or renaming branches affects all worktrees instantly
- **Fetch/Pull**: Running `git fetch` in any worktree updates the shared repository
- **Remotes**: All worktrees use the same remote configuration
- **Tags**: Tags created in one worktree are visible in all worktrees
- **Stashes**: The stash list is shared across all worktrees

### Per-Worktree Elements

Each worktree maintains its own:

- **HEAD**: Each worktree can be on a different branch or commit
- **Working directory**: File contents are independent across worktrees
- **Index (staging area)**: Staged changes are local to each worktree
- **Specific refs**: `refs/bisect`, `refs/worktree`, and `refs/rewritten` are per-worktree

## Steps

### Step 1: Verify Shared Repository Status

Before synchronizing changes, understand what's shared across your worktrees.

```bash
# In any worktree, list all worktrees
git worktree list
```

**Example Output**:
```
/home/user/project          abc123d [main]
/home/user/project-feature1 def456e [feature/auth]
/home/user/project-feature2 789ghij [feature/dashboard]
```

Check recent commits visible from all worktrees:

```bash
# View all branches and their latest commits
git log --all --oneline --graph --decorate -10
```

This shows commits from all branches, regardless of which worktree created them.

### Step 2: Access Commits Made in Another Worktree

When Claude Code makes a commit in one worktree, you can immediately access it from another worktree without any synchronization command.

**Scenario**: Claude committed to `feature/auth` in worktree-1, and you want to see those changes in worktree-2.

```bash
# In worktree-2, view commits on the other branch
git log feature/auth

# View the changes
git show feature/auth

# See what files changed
git diff main..feature/auth
```

**No synchronization needed** - the commits are already accessible because all worktrees share the same repository database.

### Step 3: Merge Changes from Another Worktree's Branch

To incorporate changes from a branch being worked on in another worktree:

```bash
# Check your current branch
git branch --show-current

# Merge changes from another branch (worked on in different worktree)
git merge feature/auth
```

**Example workflow**:
```bash
# In worktree-1 (on feature/auth)
git add .
git commit -m "Add authentication module"

# In worktree-2 (on feature/dashboard)
# Immediately merge the auth changes
git merge feature/auth
```

### Step 4: Cherry-Pick Specific Commits Between Worktrees

When you need only specific commits from another worktree's branch:

```bash
# Find the commit hash from the other worktree's branch
git log feature/auth --oneline -5

# Cherry-pick specific commits
git cherry-pick abc1234
```

**Use case**: Claude Code completed a bug fix in worktree-1 that you also need in worktree-2's feature branch.

```bash
# In worktree-1 (feature/bugfix)
git add .
git commit -m "Fix authentication timeout issue"
# Note the commit hash: def5678

# In worktree-2 (feature/dashboard)
git cherry-pick def5678
```

### Step 5: Rebase Across Worktrees

Update a branch to incorporate changes from another branch worked on in a different worktree:

```bash
# In worktree-2, rebase your branch onto changes from worktree-1
git rebase feature/auth
```

**Important**: Don't rebase branches currently checked out in other worktrees - it will cause conflicts.

**Safe workflow**:
```bash
# Check which branches are checked out
git worktree list

# Only rebase if the target branch is NOT checked out elsewhere
# In worktree-2 (on feature/dashboard)
git rebase main  # Safe if main is not checked out in another worktree
```

### Step 6: Fetch and Pull in Multi-Worktree Environment

Fetching remote changes updates the shared repository, making new commits available to all worktrees:

```bash
# In any worktree
git fetch origin

# All worktrees now have access to new remote commits
```

Pulling in one worktree only updates that worktree's working directory:

```bash
# In worktree-1 (on main branch)
git pull origin main
# Only worktree-1's files are updated
# But the commits are available to all worktrees
```

**Best practice for Claude Code Web parallel sessions**:

```bash
# Worktree-1: Claude working on feature/auth
git fetch origin  # Get latest remote changes
git pull origin main  # Update main in this worktree
git merge main  # Merge main into feature/auth

# Worktree-2: Claude working on feature/dashboard
# No action needed - can immediately access the commits
git log main  # Shows updated commits from worktree-1's pull
```

### Step 7: Push Changes from Any Worktree

Pushing commits to remote affects all worktrees because branches are shared:

```bash
# In worktree-1
git push origin feature/auth
```

The branch `feature/auth` is now updated on the remote, and all worktrees see this:

```bash
# In worktree-2
git branch -vv  # Shows that feature/auth is up to date with remote
```

### Step 8: Handle Branch Protection Across Worktrees

Git prevents you from checking out the same branch in multiple worktrees. To work on the same logical changes:

**Option A: Create related branches**
```bash
# Worktree-1
git checkout -b feature/auth-backend

# Worktree-2
git checkout -b feature/auth-frontend

# Later, merge both into a single branch
git checkout -b feature/auth-complete
git merge feature/auth-backend
git merge feature/auth-frontend
```

**Option B: Work sequentially**
```bash
# Finish work in worktree-1 first
cd /path/to/worktree-1
git add .
git commit -m "Complete backend auth"
git checkout main  # Switch away from feature/auth

# Now feature/auth can be checked out in worktree-2
cd /path/to/worktree-2
git checkout feature/auth
```

### Step 9: Synchronize Using Intermediate Branches

For complex parallel workflows, use integration branches:

```bash
# In worktree-1 (feature/auth)
git add .
git commit -m "Add user authentication"
git push origin feature/auth

# Create integration branch
git checkout -b integration/sprint-1
git merge feature/auth
git push origin integration/sprint-1

# In worktree-2 (feature/dashboard)
git fetch origin
git merge origin/integration/sprint-1
# Now has auth changes without directly merging feature/auth
```

### Step 10: Monitor Changes Across All Worktrees

Use Git commands to track what's happening across your worktrees:

```bash
# See all branches and their status
git branch -vv --all

# View recent activity across all branches
git log --all --since="1 day ago" --oneline --graph

# Check for uncommitted changes in current worktree
git status

# Find which worktree has a specific branch checked out
git worktree list | grep feature/auth
```

**Pro tip for Claude Code Web**: Create a custom slash command to monitor all worktrees:

```bash
# .claude/commands/worktree-status.md
#!/bin/bash
echo "=== All Worktrees ==="
git worktree list
echo ""
echo "=== Recent Activity (All Branches) ==="
git log --all --oneline --graph --decorate -10
echo ""
echo "=== Current Worktree Status ==="
git status --short
```

## Expected Results

After properly synchronizing changes between worktrees, you should experience:

### Immediate Access to Commits

1. **Commits visible instantly**: Any commit made in worktree-1 appears immediately in `git log` from worktree-2
2. **Branch updates reflected**: Creating or updating branches in one worktree is instantly visible in all others
3. **No manual sync required**: No need to push/pull between worktrees for local development

### Successful Merging

1. **Clean merges**: Merging a branch from another worktree works exactly like merging any branch
2. **Conflict resolution**: Merge conflicts are handled normally with standard Git workflow
3. **Consistent history**: Git history remains clean and linear (or branched as desired)

### Parallel Claude Code Sessions

1. **Independent work**: Claude can work on different features simultaneously without conflicts
2. **Shared progress**: Each Claude session sees commits from other sessions
3. **Efficient collaboration**: Changes can be integrated as soon as they're ready

### Example Success Scenario

```
# Worktree-1 (Claude session 1)
$ git commit -m "Add login endpoint"
[feature/auth abc1234] Add login endpoint

# Worktree-2 (Claude session 2) - immediately after
$ git log feature/auth -1
abc1234 Add login endpoint

$ git merge feature/auth
Updating def5678..abc1234
Fast-forward
 src/auth/login.js | 45 +++++++++++++++++++++++
 1 file changed, 45 insertions(+)
```

## Troubleshooting

### Common Issue 1
**Problem**: Cannot checkout branch - error "already checked out at /path/to/other/worktree"
**Solution**:
- Git protects you from checking out the same branch in multiple worktrees
- Either switch the other worktree to a different branch first
- Or create a new branch based on the desired branch: `git checkout -b feature/auth-copy feature/auth`
- Use `git worktree list` to see where the branch is checked out
- Consider if you really need the same branch in multiple places, or if separate feature branches would be better
- For read-only access, use `git show` or `git diff` without checking out

### Common Issue 2
**Problem**: Changes made in one worktree don't appear in another worktree's working directory
**Solution**:
- This is expected behavior - working directories are independent
- Commits are shared, but working files are not
- You need to explicitly merge or checkout to update working files
- Check if you're on the right branch: `git branch --show-current`
- Merge the changes: `git merge other-branch`
- Or checkout the branch (if not checked out elsewhere): `git checkout other-branch`
- Remember: `git pull` updates current branch's working directory only

### Common Issue 3
**Problem**: Merge conflicts when integrating changes from another worktree
**Solution**:
- Conflicts are normal when changes overlap
- Resolve them using standard Git conflict resolution
- Use `git status` to see conflicting files
- Edit files to resolve conflicts (remove `<<<<<<<`, `=======`, `>>>>>>>` markers)
- After resolving: `git add <resolved-files>`
- Complete the merge: `git commit`
- For complex conflicts, use `git mergetool`
- Communicate with Claude in the other worktree about overlapping changes

### Common Issue 4
**Problem**: `git fetch` in one worktree doesn't update another worktree's files
**Solution**:
- `git fetch` only downloads commits; it doesn't update working directories
- After fetch, you must explicitly merge or pull in each worktree
- In worktree-1: `git fetch origin`
- In worktree-2: Commits are now available, but working files unchanged
- To update worktree-2: `git merge origin/main` or `git pull`
- This is correct behavior - allows you to review changes before applying them
- Use `git log origin/main` to see fetched commits before merging

### Common Issue 5
**Problem**: Pushed commits from one worktree don't appear in another worktree after pulling
**Solution**:
- Ensure you're on the correct branch: `git branch --show-current`
- The pull command updates only the current branch
- If worktree-1 pushed to `feature/auth`, worktree-2 needs: `git checkout feature/auth && git pull`
- Or if on different branch: `git fetch origin` then `git merge origin/feature/auth`
- Check remote tracking: `git branch -vv`
- Verify remote URL is same: `git remote -v`

### Common Issue 6
**Problem**: Accidentally rebased a branch that's checked out in another worktree
**Solution**:
- This causes the other worktree to be in a detached HEAD state or have outdated refs
- In the affected worktree, run: `git status` to check state
- If safe to reset: `git reset --hard origin/branch-name`
- Or if you want to keep changes: `git rebase origin/branch-name`
- Best practice: Never rebase branches checked out in other worktrees
- Always check `git worktree list` before rebasing
- Use merge instead of rebase for branches in active worktrees

## Additional Tips

### Optimize for Claude Code Web Parallel Development

**Use branch naming conventions**:
```bash
# Make it clear which worktree owns which branch
# Worktree-1
git checkout -b claude-1/feature/auth

# Worktree-2
git checkout -b claude-2/feature/dashboard
```

**Create integration checkpoints**:
```bash
# Periodically merge both branches into an integration branch
git checkout -b integration/all-features
git merge claude-1/feature/auth
git merge claude-2/feature/dashboard
git push origin integration/all-features
```

### Leverage Shared Stash

The stash is shared across all worktrees:

```bash
# In worktree-1, stash some work
git stash save "WIP: auth validation"

# In worktree-2, you can apply it
git stash list  # Shows stashes from all worktrees
git stash apply stash@{0}
```

**Use case**: Transfer incomplete work between worktrees without committing.

### Use Worktree-Specific Git Hooks

While repository hooks are shared, you can create worktree-specific behavior:

```bash
# In each worktree's .git/hooks/post-commit
#!/bin/bash
echo "Commit made in worktree: $(basename $(pwd))"
# Notify other worktrees or update shared status
```

### Fast-Forward Merges Across Worktrees

When possible, structure your work for fast-forward merges:

```bash
# In worktree-1 (on main)
git pull origin main  # Ensure up to date

# In worktree-2 (on feature branch)
git merge main  # Keep feature branch up to date with main
# Do your work and commit

# Back in worktree-1
git merge feature-branch  # Fast-forward merge
```

### Monitor All Worktrees with Git Aliases

Add to `.gitconfig`:

```ini
[alias]
    wt-log = log --all --graph --oneline --decorate
    wt-status = "!git worktree list && echo '' && git status"
    wt-sync = "!f() { git fetch origin && git worktree list; }; f"
```

Use them in any worktree:
```bash
git wt-log    # See commits from all branches
git wt-status # See all worktrees and current status
git wt-sync   # Fetch and list worktrees
```

### Coordinate Claude Code Sessions

**Best practices for running multiple Claude sessions**:

1. **Clear ownership**: Assign each worktree/Claude session a specific feature or area
2. **Regular integration**: Merge integration branches frequently to catch conflicts early
3. **Communicate changes**: Use commit messages to inform other sessions about important changes
4. **Avoid overlap**: Don't have multiple sessions editing the same files
5. **Use PR workflow**: Have each worktree create separate PRs for easier review

### Clean Up After Synchronization

Remove branches that have been successfully synchronized:

```bash
# After merging feature/auth into main
git branch -d feature/auth  # Affects all worktrees

# Clean up remote branches
git push origin --delete feature/auth

# Prune deleted remote branches in all worktrees
git fetch --prune
```

### Backup Before Complex Synchronization

Before complex merges or rebases across worktrees:

```bash
# Create backup branches
git branch backup/feature-auth feature/auth
git branch backup/feature-dashboard feature/dashboard

# If something goes wrong
git reset --hard backup/feature-auth
```

## Related Articles
- KB-026: How to run multiple tasks in parallel across different repositories
- KB-027: How to manage concurrent tasks effectively
- KB-029: How to track progress across multiple parallel sessions
- KB-031: How to create a new branch for a specific task
- KB-036: How to handle merge conflicts identified by Claude
- KB-037: How to work on existing branches instead of creating new ones

## Sources
- Git Worktrees and Claude Code: A Guide for Developers in 2025 - https://www.geeky-gadgets.com/how-to-use-git-worktrees-with-claude-code-for-seamless-multitasking/ (Accessed: November 16, 2025)
- How we're shipping faster with Claude Code and Git Worktrees - https://incident.io/blog/shipping-faster-with-claude-code-and-git-worktrees (Accessed: November 16, 2025)
- Git - git-worktree Documentation - https://git-scm.com/docs/git-worktree (Accessed: November 16, 2025)
- How to Use Git Worktree - https://www.gitkraken.com/learn/git/git-worktree (Accessed: November 16, 2025)
- Working on two git branches at once with git worktree - https://andrewlock.net/working-on-two-git-branches-at-once-with-git-worktree/ (Accessed: November 16, 2025)
- Running Multiple Claude Code Sessions in Parallel with git worktree - https://dev.to/datadeer/part-2-running-multiple-claude-code-sessions-in-parallel-with-git-worktree-165i (Accessed: November 16, 2025)
- Google Search: "git worktree synchronize changes between worktrees official documentation" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
