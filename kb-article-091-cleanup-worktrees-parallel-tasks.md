# How to clean up worktrees after completing parallel tasks

**Article ID**: KB-091
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
When using git worktrees to run parallel Claude Code Web sessions in the same repository, cleanup becomes essential to prevent clutter and maintain a healthy development environment. This guide shows you how to safely remove worktrees after completing your parallel tasks, preserve important session context, and automate cleanup workflows for efficient repository management.

## Prerequisites
- Understanding of git worktrees and parallel workflows (KB-088, KB-089)
- Familiarity with managing concurrent tasks (KB-027)
- At least one completed worktree-based session
- Basic command-line git knowledge
- Understanding of Claude Code Web session context

## Understanding Worktree Cleanup

### Why Cleanup Matters

**Problems with Neglected Worktrees:**
- Disk space consumption (each worktree is a full working copy)
- Confusing directory structure with many abandoned worktrees
- Stale references in `.git/worktrees/` that slow git operations
- Difficulty identifying active vs completed work
- Potential confusion when starting new sessions

**When to Clean Up:**
- ✅ After merging a worktree's branch to main
- ✅ When a parallel task is completed and reviewed
- ✅ After pulling changes and no longer needing the branch
- ✅ When abandoning experimental or exploratory work
- ✅ During regular weekly/monthly repository maintenance

### Types of Cleanup Operations

**1. Complete Removal** (`git worktree remove`)
- Deletes the worktree directory completely
- Removes administrative data from `.git/worktrees/`
- Use when: Task is done and you won't return to it

**2. Pruning Stale References** (`git worktree prune`)
- Cleans up references to manually deleted worktrees
- Doesn't delete any actual directories
- Use when: You've already deleted worktree folders manually

**3. Administrative Cleanup**
- Removes saved session contexts
- Deletes temporary files and logs
- Updates tracking documentation

## Steps

### Step 1: Identify Worktrees Ready for Cleanup

Before removing worktrees, determine which ones are safe to delete.

**List all existing worktrees:**
```bash
git worktree list
```

**Example output:**
```
/home/user/myproject              abc123d [main]
/home/user/myproject-feature-a    def456e [feature/user-auth]
/home/user/myproject-feature-b    ghi789f [feature/api-endpoint]
/home/user/myproject-bugfix       jkl012g [bugfix/login-validation]
```

**Check which branches have been merged:**
```bash
# See merged branches
git branch --merged main

# See unmerged branches
git branch --no-merged main
```

**Identify safe cleanup candidates:**
- ✅ Branches that show in `--merged` list
- ✅ Pull requests that have been merged and closed
- ✅ Experimental branches you're abandoning
- ✅ Worktrees where Claude sessions have completed
- ❌ Active sessions still running
- ❌ Unmerged work you plan to continue

### Step 2: Save Session Context Before Cleanup

If the worktree contains valuable session context or conversation history, preserve it first.

**For Claude Code Web sessions:**

While in the session associated with the worktree, save the context:
```bash
# If using session save feature
/sc:save feature-auth-implementation
```

**Alternative: Export important artifacts manually:**
```bash
# Navigate to the worktree
cd /home/user/myproject-feature-a

# Copy any important logs or notes
cp .claude/session-notes.md ~/archive/feature-a-notes.md

# Save the git log for this branch
git log --oneline main..HEAD > ~/archive/feature-a-commits.txt

# Document final state
git status > ~/archive/feature-a-final-status.txt
```

**Take screenshots if needed:**
- Capture Claude's final summary or recommendations
- Save any important conversations or decisions
- Document test results or performance metrics

### Step 3: Remove Individual Worktrees

**Basic removal for clean worktrees:**
```bash
# Remove a worktree (must have no uncommitted changes)
git worktree remove ../myproject-feature-a
```

**What happens:**
- The worktree directory is deleted from disk
- The branch remains (can still access commits)
- Administrative files in `.git/worktrees/` are removed
- Your main repository is unaffected

**If the worktree has uncommitted changes:**
```bash
# This will fail with an error
git worktree remove ../myproject-feature-a
# error: 'myproject-feature-a' contains modified or untracked files

# Review the changes first
cd ../myproject-feature-a
git status

# Either commit them:
git add .
git commit -m "Final changes before cleanup"

# Or discard them (if truly unwanted):
git checkout .
git clean -fd

# Then remove
cd /home/user/myproject
git worktree remove ../myproject-feature-a
```

**Force removal (use with caution):**
```bash
# Force remove even with uncommitted changes
git worktree remove --force ../myproject-feature-a

# Force remove a locked worktree (double force)
git worktree remove --force --force ../myproject-locked
```

**When to use force:**
- You've confirmed the changes are unnecessary
- The worktree is corrupted or inaccessible
- You're cleaning up abandoned experimental work
- The worktree directory was already manually deleted

### Step 4: Clean Up Associated Branches

After removing worktrees, decide whether to keep or delete the branches.

**Keep the branch (preserves history):**
```bash
# Branch remains available for future reference
# No action needed - git worktree remove doesn't delete branches
```

**Delete the merged branch:**
```bash
# Delete a fully merged local branch
git branch -d feature/user-auth

# Output: Deleted branch feature/user-auth (was def456e)
```

**Delete an unmerged branch (careful!):**
```bash
# Force delete an unmerged branch
git branch -D feature/experimental-approach

# Output: Deleted branch feature/experimental-approach (was xyz789a)
```

**Clean up remote tracking branches:**
```bash
# If you also pushed the branch and it's been merged/deleted remotely
git fetch --prune

# Delete the remote branch
git push origin --delete feature/user-auth
```

### Step 5: Prune Stale Worktree References

If you manually deleted worktree directories (without using `git worktree remove`), clean up the stale references.

**Check for stale references:**
```bash
# Prune with dry-run to see what would be removed
git worktree prune --dry-run
```

**Example output:**
```
would remove worktrees/myproject-feature-a (missing directory)
would remove worktrees/myproject-old-experiment (missing directory)
```

**Perform the prune:**
```bash
git worktree prune
```

**What this does:**
- Removes administrative data in `.git/worktrees/` for missing directories
- Doesn't delete any actual worktree directories
- Safe to run regularly as part of maintenance

**Advanced: Prune old worktree data:**
```bash
# Only prune worktree info older than 3 months
git worktree prune --expire 3.months.ago

# See what's old without removing
git worktree prune --expire 30.days.ago --dry-run
```

### Step 6: Verify Cleanup Success

**Confirm worktrees are removed:**
```bash
git worktree list
```

**Expected output (after cleanup):**
```
/home/user/myproject  abc123d [main]
```

**Check branch status:**
```bash
# List remaining local branches
git branch

# Verify merged branches are cleaned up
git branch --merged main
```

**Verify disk space reclaimed:**
```bash
# Check repository size
du -sh /home/user/myproject*

# Should only see main directory if all worktrees removed
```

**Check for orphaned worktree data:**
```bash
# List administrative worktree directories
ls -la .git/worktrees/

# Should be empty or only contain active worktrees
```

### Step 7: Automate Cleanup for Regular Maintenance

**Create a cleanup script for merged worktrees:**

```bash
#!/bin/bash
# cleanup-merged-worktrees.sh

echo "Cleaning up merged worktrees..."

# Get list of merged branches
MERGED_BRANCHES=$(git branch --merged main | grep -v "main" | sed 's/^[ *]*//')

# For each merged branch, find and remove associated worktree
for branch in $MERGED_BRANCHES; do
    # Find worktree for this branch
    WORKTREE_PATH=$(git worktree list --porcelain | grep -B2 "branch refs/heads/$branch" | grep "worktree" | awk '{print $2}')

    if [ -n "$WORKTREE_PATH" ]; then
        echo "Removing worktree: $WORKTREE_PATH (branch: $branch)"
        git worktree remove "$WORKTREE_PATH"

        # Optionally delete the branch too
        echo "Deleting merged branch: $branch"
        git branch -d "$branch"
    fi
done

# Prune any stale references
echo "Pruning stale worktree references..."
git worktree prune

echo "Cleanup complete!"
```

**Make it executable and use it:**
```bash
chmod +x cleanup-merged-worktrees.sh
./cleanup-merged-worktrees.sh
```

**Schedule regular cleanup (weekly):**
```bash
# Add to cron (edit with: crontab -e)
# Run every Sunday at 2 AM
0 2 * * 0 cd /home/user/myproject && git worktree prune
```

### Step 8: Batch Cleanup Multiple Worktrees

**Remove multiple worktrees at once:**

```bash
# Remove all worktrees except main
for worktree in $(git worktree list --porcelain | grep "worktree" | awk '{print $2}' | grep -v "$(pwd)"); do
    echo "Removing: $worktree"
    git worktree remove "$worktree"
done
```

**Interactive cleanup (safer):**
```bash
# List worktrees with numbers
git worktree list | nl

# Remove by confirming each one
for worktree in $(git worktree list --porcelain | grep "worktree" | awk '{print $2}' | grep -v "$(pwd)"); do
    echo "Remove $worktree? (y/n)"
    read answer
    if [ "$answer" = "y" ]; then
        git worktree remove "$worktree"
    fi
done
```

**Clean up by age:**
```bash
# Find and remove worktrees not modified in 30 days
find /home/user/myproject-* -maxdepth 0 -type d -mtime +30 -exec basename {} \; | while read dir; do
    echo "Old worktree found: $dir"
    git worktree remove "/home/user/$dir" --force
done
```

## Expected Results

After successfully cleaning up your worktrees, you should:

- See only active worktrees when running `git worktree list`
- Have a clean `.git/worktrees/` directory with no orphaned data
- Reclaim disk space from removed working copies
- See a cleaner directory structure without abandoned worktrees
- Have only relevant branches remaining in `git branch`
- Experience faster git operations without stale worktree overhead
- Maintain clear organization of active parallel work
- Have preserved important session context in archives

**Success indicators:**
```
✓ git worktree list shows only active worktrees
✓ git worktree prune reports nothing to clean
✓ Merged branches removed from local repository
✓ Disk space recovered (check with du -sh)
✓ Session context saved before removal
✓ No orphaned directories in parent folder
✓ Clean git branch output
```

## Troubleshooting

### Common Issue 1
**Problem**: "Cannot remove worktree - contains modified or untracked files"
**Solution**:
- Navigate to the worktree: `cd /path/to/worktree`
- Review changes: `git status`
- Commit important changes: `git add . && git commit -m "Final changes"`
- Or discard unwanted changes: `git checkout . && git clean -fd`
- Return to main repo and retry removal
- Alternatively, use `--force` flag if you're certain changes aren't needed
- For stubborn cases, use double force: `git worktree remove --force --force`

### Common Issue 2
**Problem**: Worktree directory manually deleted but git still shows it
**Solution**:
- This is normal when directories are removed outside of git
- Run `git worktree prune` to clean up references
- This removes the administrative data safely
- Check with `git worktree list` to confirm
- For persistent references, check `.git/worktrees/` and remove manually
- Always prefer `git worktree remove` to avoid this issue

### Common Issue 3
**Problem**: "Worktree is locked" error preventing removal
**Solution**:
- Check why it's locked: `cat .git/worktrees/[name]/locked`
- Unlock it: `git worktree unlock /path/to/worktree`
- Then remove normally: `git worktree remove /path/to/worktree`
- Or force remove: `git worktree remove --force --force /path/to/worktree`
- Locked worktrees typically indicate active use or protection
- Verify no Claude session is actively using the worktree

### Common Issue 4
**Problem**: Accidentally deleted worktree with important uncommitted work
**Solution**:
- If you used `git worktree remove`, the directory is gone
- Check if changes were committed: `git log [branch-name]`
- Look for unreachable commits: `git fsck --lost-found`
- Search git reflog: `git reflog show [branch-name]`
- Recovery is only possible if changes were committed
- Uncommitted changes are permanently lost
- Prevention: Always review `git status` before removal

### Common Issue 5
**Problem**: Lost session context after removing worktree
**Solution**:
- Session context is stored per worktree in Claude Code Web
- If not saved before removal, context is lost
- Check if session was archived: Look in Claude Code Web session history
- Review branch commits for implementation details
- Use `git log` and commit messages to reconstruct context
- Prevention: Always use `/sc:save` or export notes before cleanup
- Consider maintaining external documentation for important sessions

### Common Issue 6
**Problem**: Cleaned up worktree but branch shows as unmerged
**Solution**:
- Check merge status: `git branch --merged main`
- If unmerged, work might not be incorporated yet
- Review the branch: `git log main..[branch-name]`
- Decide: merge the branch or accept the loss
- Merge if needed: `git checkout main && git merge [branch-name]`
- Cherry-pick specific commits: `git cherry-pick [commit-hash]`
- If truly unwanted, force delete: `git branch -D [branch-name]`

### Common Issue 7
**Problem**: Script cleanup removed active worktrees
**Solution**:
- This happens with overly aggressive automation
- Check if branches are recoverable: `git branch -a`
- Recreate needed worktrees: `git worktree add ../project-name branch-name`
- Restore from backups if available
- Prevention: Use dry-run flags in scripts (`--dry-run`)
- Add confirmation prompts for automated cleanup
- Test scripts on a clone repository first
- Maintain a whitelist of worktrees to never auto-remove

## Additional Tips

### Best Practices for Cleanup Workflow

**Establish a regular schedule:**
- Daily: Quick check of merged branches and completed sessions
- Weekly: Full worktree cleanup and branch pruning
- Monthly: Deep cleanup including stale references and old branches

**Pre-cleanup checklist:**
```
☐ Save important session contexts
☐ Review uncommitted changes in each worktree
☐ Verify branches are merged (if intended)
☐ Export any logs or notes worth keeping
☐ Check no active Claude sessions are using worktrees
☐ Run git worktree list to document current state
```

**Post-cleanup verification:**
```
☐ git worktree list shows expected state
☐ git worktree prune reports nothing to clean
☐ Directory structure is clean
☐ Disk space reclaimed (use du -sh)
☐ Active worktrees still accessible
☐ No broken references in git
```

### Organizing Worktrees for Easy Cleanup

**Use naming conventions:**
```bash
# Good: Includes date for easy age identification
git worktree add ../myproject-auth-2025-11-15 feature/auth

# Good: Includes ticket number for tracking
git worktree add ../myproject-JIRA-123 feature/user-profile

# Good: Clear purpose indication
git worktree add ../myproject-experiment-redis feature/redis-cache
```

**Group by purpose:**
```
/home/user/
├── myproject/                    # Main repository
├── myproject-stable/             # Long-term feature branches
├── myproject-bugfix-LOGIN/       # Short-term bug fixes (clean often)
└── myproject-experiment-API/     # Experiments (clean after evaluation)
```

### Preserving Knowledge Before Cleanup

**What to save:**
- Claude's architectural decisions and reasoning
- Performance measurements or test results
- Useful code patterns discovered during implementation
- Failed approaches and why they didn't work
- Links to relevant documentation or resources
- Any session-specific configuration or setup steps

**Where to save it:**
```bash
# Project wiki or documentation
~/docs/projects/myproject/sessions/auth-implementation.md

# Personal notes
~/notes/claude-sessions/2025-11-auth.md

# Git commit messages (most accessible)
git commit -m "Implement OAuth integration

Per Claude session discussion:
- Chose OAuth 2.0 over SAML for flexibility
- Implemented PKCE flow for mobile support
- Session timeout set to 30 min (industry standard)
"
```

### Automation Strategies

**NPM packages for advanced management:**
```bash
# Install a worktree management tool
npm install -g @akiojin/claude-worktree

# Provides automated cleanup for merged PR branches
# Integrates with GitHub for PR status checking
```

**Git aliases for quick cleanup:**
```bash
# Add to ~/.gitconfig
[alias]
    wclean = "!f() { git branch --merged main | grep -v main | xargs -n 1 git branch -d; git worktree prune; }; f"
    wlist = worktree list
    wremove = worktree remove

# Usage
git wclean     # Clean all merged branches and prune worktrees
git wlist      # Quick list
git wremove ../path/to/worktree  # Quick remove
```

### Safety Measures

**Create backups before batch operations:**
```bash
# Backup branch list
git branch > ~/backup/branches-before-cleanup-2025-11-16.txt

# Backup worktree list
git worktree list > ~/backup/worktrees-before-cleanup-2025-11-16.txt

# Create repository backup (tarball)
tar -czf ~/backup/myproject-2025-11-16.tar.gz /home/user/myproject
```

**Test cleanup operations:**
```bash
# Clone repository for testing
git clone /home/user/myproject /tmp/test-cleanup
cd /tmp/test-cleanup

# Test your cleanup script
./cleanup-merged-worktrees.sh

# If successful, run on actual repository
cd /home/user/myproject
./cleanup-merged-worktrees.sh
```

### When NOT to Clean Up

**Keep worktrees for:**
- Active development you'll return to within days
- Long-running feature branches with ongoing work
- Reference implementations you consult frequently
- Branches awaiting review or approval
- Work-in-progress that's paused temporarily
- Experimental branches with valuable insights

**Signs a worktree is still valuable:**
- Recent commits (within last week)
- Unmerged work that's intentionally separate
- Documentation or examples you reference
- Performance benchmarks or test configurations
- Integration test environments

## Related Articles
- KB-088: How to use git worktrees for parallel sessions in the same repository
- KB-089: How to manage multiple worktrees effectively
- KB-090: How to synchronize changes between worktrees
- KB-026: How to run multiple tasks in parallel across different repositories
- KB-027: How to manage concurrent tasks effectively
- KB-053: How to refactor code for better maintainability (uses worktrees)
- KB-059: How to migrate code between frameworks or libraries (uses worktrees)

## Sources
- Git Worktree Official Documentation - https://git-scm.com/docs/git-worktree (Accessed: November 16, 2025)
- Git Worktrees + Claude Code: Effortless Parallel Development - https://medium.com/@francoisschuers/git-worktrees-claude-code-effortless-parallel-development-2a43e746c28c (Accessed: November 16, 2025)
- Git worktree + Claude Code: My Secret to 10x Developer Productivity - https://dev.to/kevinz103/git-worktree-claude-code-my-secret-to-10x-developer-productivity-520b (Accessed: November 16, 2025)
- Supercharging Claude Code with Git Worktrees and Docker - https://sangyh.com/posts/productivity/claude-code-with-git-worktrees/ (Accessed: November 16, 2025)
- Git Worktree Management - DeepWiki - https://deepwiki.com/smtg-ai/claude-squad/4.1-git-worktree-management (Accessed: November 16, 2025)
- Google Search Suggestion: "git worktree prune and remove best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
