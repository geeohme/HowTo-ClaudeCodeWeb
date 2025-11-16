# How to View Changes Made by Claude Code in Your Repository

**Article ID**: KB-015
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 5 minutes

## Overview
After Claude completes a task, you'll want to review the changes made to your code. This guide shows you multiple ways to view, understand, and verify Claude's modifications.

## Prerequisites
- At least one completed or in-progress task
- Connected repository in Claude Code Web
- Basic understanding of code diffs and changes

## Where to View Changes

### Method 1: In Claude Code Web (During/After Task)

**Activity Feed:**
- Shows files being modified in real-time
- Displays summary of changes
- Lists files created, edited, or deleted

**Example:**
```
✓ Edited src/components/Header.js
  Added: validateEmail function
  Modified: handleSubmit function
  +45 lines, -12 lines

✓ Created tests/Header.test.js
  Added: email validation tests
  +28 lines
```

**Completion Summary:**
- Detailed summary when task finishes
- List of all files changed
- High-level description of modifications
- Links to view changes on GitHub

### Method 2: On GitHub (Recommended for Full Review)

**Step 1: Find the Branch**
1. Go to your repository on GitHub
2. Click the branch dropdown
3. Find the Claude branch (starts with `claude/`)
4. Select it

**Step 2: View the Diff**

**Option A: Compare Branches**
1. Click "Compare" or "Compare & pull request"
2. Select base branch (usually main)
3. Select Claude's branch
4. View full diff

**Option B: View Commits**
1. On Claude's branch, click "Commits"
2. Click on any commit to see changes
3. Green = additions, Red = deletions
4. Side-by-side or unified diff view

**Option C: Create Pull Request**
1. Click "Create Pull Request"
2. GitHub shows all changes in the PR
3. Can comment on specific lines
4. Request reviews if needed

### Method 3: Locally (Using Git)

If you have the repository cloned locally:

**Fetch the Branch:**
```bash
git fetch origin
git checkout claude/feature-name
```

**View Changes:**
```bash
# Compare with main branch
git diff main

# See commit log
git log main..HEAD

# View specific file changes
git diff main -- path/to/file.js
```

**Use Git UI Tools:**
- GitHub Desktop
- GitKraken
- VS Code Source Control
- SourceTree

## Understanding the Changes Display

### File Status Indicators

**Green (Additions):**
- New lines of code
- `+ added line`
- New files created

**Red (Deletions):**
- Removed lines of code
- `- deleted line`
- Files deleted

**Diff Summary:**
```
src/components/Header.js
  +45  -12
  ↑     ↑
  Added  Removed
  lines  lines
```

### Types of Changes

**Modified Files:**
- Existing files with changes
- Shows before/after comparison
- Highlighted differences

**New Files:**
- Entirely new files created
- All content shown in green
- "+++ new file" indicator

**Deleted Files:**
- Files removed
- All content shown in red
- "--- deleted file" indicator

**Renamed Files:**
- File moved or renamed
- Shows old → new path
- Content diff if also modified

## What to Look For

### Code Quality

**✓ Check for:**
- Proper syntax and formatting
- Logical implementations
- Code follows your project's style
- Comments are clear and helpful
- No hardcoded sensitive data

**⚠ Watch for:**
- Unexpected changes to unrelated files
- Overly complex solutions
- Missing error handling
- Incomplete implementations
- TODO comments that need addressing

### Functionality

**✓ Verify:**
- Changes match your task description
- Requested features are implemented
- Bug fixes actually resolve the issue
- Tests are included (if requested)
- Edge cases are handled

**⚠ Red flags:**
- Changes in unexpected files
- Removed code that seems important
- Missing requested functionality
- Unrelated modifications

### Testing

**✓ Confirm:**
- Existing tests still pass
- New tests added for new features
- Tests cover important scenarios
- Test names are descriptive

## Reviewing Changes in Detail

### GitHub PR Review Flow

**Step 1: Files Changed Tab**
- See all modified files
- Expandable diff for each file
- Comment on specific lines

**Step 2: Conversation Tab**
- Overview of changes
- Summary and description
- General comments and discussion

**Step 3: Commits Tab**
- Individual commit messages
- Chronological change history
- Can view each commit's diff

### Line-by-Line Review

**In GitHub:**
1. Click on line numbers to comment
2. Add inline feedback
3. Suggest changes
4. Request modifications

**Common Review Actions:**
- ✓ Approve changes
- 💬 Comment for discussion
- 🔄 Request changes
- ❓ Ask clarifying questions

## Expected Results

After learning to view changes, you should:
- Locate Claude's branches on GitHub
- View detailed diffs of all modifications
- Understand what files were changed and how
- Be able to review code quality
- Know how to approve or request revisions
- Feel confident merging Claude's work

## Troubleshooting

### Common Issue 1
**Problem**: Can't find the changes on GitHub
**Solution**:
- Ensure task completed successfully
- Refresh the GitHub page
- Check all branches (not just recent)
- Search for branches starting with "claude/"
- Verify changes were pushed (check activity feed)

### Common Issue 2
**Problem**: Diff looks confusing or overwhelming
**Solution**:
- Use GitHub's split view instead of unified
- Review one file at a time
- Hide whitespace changes if they're obscuring real changes
- Focus on green (additions) first
- Read Claude's commit messages for context

### Common Issue 3
**Problem**: Changes seem incomplete or wrong
**Solution**:
- Review the task description—was it clear?
- Check if there were errors during execution
- Look for comments Claude left explaining decisions
- Create a new task to refine or fix issues
- Or manually edit the branch and push changes

### Common Issue 4
**Problem**: Too many files changed
**Solution**:
- This might be normal for refactoring tasks
- Check if auto-formatting was applied
- Look for unintended changes in node_modules or build files
- Verify the scope matches your task
- May need to cherry-pick commits if some changes are unwanted

## Additional Tips

### Before Merging

**Best Practice Checklist:**
- [ ] Review all changed files
- [ ] Understand each modification
- [ ] Test the changes locally (if possible)
- [ ] Ensure tests pass
- [ ] Verify no sensitive data was added
- [ ] Check commit messages are meaningful
- [ ] Confirm changes match task description

### Using Pull Requests

**Benefits:**
- Formal review process
- Team members can comment
- CI/CD runs automatically
- Easy to see exactly what will merge
- Can make additional commits before merging

**Workflow:**
1. Claude completes task
2. You review changes
3. Create PR from Claude's branch
4. Request reviews (optional)
5. Make any final adjustments
6. Merge to main

### Local Testing

**Pull Claude's Branch:**
```bash
git fetch origin
git checkout claude/feature-name
npm install  # or pip install, etc.
npm test
npm run build
```

**Test the Changes:**
- Run the application
- Execute tests
- Check functionality
- Verify no breaking changes

### Documentation

**Keep Track Of:**
- What Claude implemented
- Any manual adjustments you made
- Learnings for future task descriptions
- Patterns that work well for your codebase

## Related Articles
- KB-014: How to understand which branch Claude Code is working on
- KB-013: How to view the file structure of your connected repository
- KB-019: How to review completed sessions
- KB-022: How to intervene and guide Claude mid-session when it encounters issues

## Sources
1. GitHub Pull Request Reviews - https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/about-pull-request-reviews (Accessed: November 16, 2025)
2. Understanding Git Diffs - https://git-scm.com/docs/git-diff (Accessed: November 16, 2025)
3. Code Review Best Practices - Search: "code review best practices GitHub" (Accessed: November 16, 2025)
4. Claude Code Web Change Review - Search: "review changes made by claude code" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
