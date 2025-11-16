# How to create a pull request automatically from completed work

**Article ID**: KB-033
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Claude Code Web can automatically create pull requests after completing coding tasks, streamlining your workflow from implementation to code review. This guide shows you how to request automatic PR creation, configure the process, and understand what Claude includes in the pull request.

## Prerequisites
- Active Claude Pro or Max subscription
- GitHub repository connected to Claude Code Web (KB-004)
- Understanding of branches and pull requests
- Write permissions on the repository
- Completed or in-progress coding task
- Familiarity with GitHub pull request workflow

## Understanding Automatic PR Creation

### What Gets Created

When Claude creates a PR automatically, it includes:
- **Descriptive title** based on the task and changes
- **Comprehensive description** with summary of modifications
- **All commits** from the Claude branch
- **List of files changed** with context
- **Test plan** or verification steps (when applicable)
- **Link to the base branch** for comparison

### When to Use Automatic PR Creation

**Ideal scenarios:**
- Feature implementation ready for team review
- Bug fixes that need verification before merging
- Refactoring work requiring code review
- Any change that benefits from formal review process
- Changes requiring CI/CD pipeline execution
- Team collaboration on code improvements

**When to skip:**
- Experimental or work-in-progress changes
- Personal repositories where direct merge is acceptable
- Quick fixes you'll merge immediately
- Changes you want to test locally first

## Steps

### Step 1: Request PR Creation During Task Description

**Method 1: Include in Initial Task**

When describing your task to Claude, explicitly request PR creation:

```
Add dark mode toggle to the navigation bar and create a pull request when done.
```

**More specific examples:**
```
Fix the email validation bug in the contact form, then create a PR with a detailed description of the fix.
```

```
Implement user authentication with JWT tokens. When complete, open a pull request targeting the develop branch.
```

**Key phrases Claude understands:**
- "create a pull request"
- "open a PR"
- "make a pull request when done"
- "create PR after implementation"
- "submit as pull request"

### Step 2: Request PR Creation After Task Completion

**Method 2: Ask After Work is Done**

If Claude completes a task without creating a PR, you can request it afterward:

**In the same session:**
```
That looks great! Can you create a pull request for these changes?
```

**Specify details:**
```
Please create a pull request with the title "Add dark mode toggle" and include testing instructions in the description.
```

**Target specific branch:**
```
Create a pull request targeting the develop branch instead of main.
```

### Step 3: Customize PR Details (Optional)

**Provide specific instructions for the PR:**

**Custom title:**
```
Create a PR titled "feat: Add dark mode toggle component"
```

**Detailed description requirements:**
```
Create a pull request with:
- Summary of changes
- List of new components created
- Testing steps
- Screenshots of the dark mode toggle
```

**Include related issues:**
```
Create a PR that references issue #123 and includes "Closes #123" in the description.
```

**Target specific reviewers:**
```
Create a pull request and request review from @username
```

### Step 4: Monitor PR Creation Process

**What Claude does:**

**1. Validates changes:**
- Verifies all changes are committed
- Checks branch is pushed to remote
- Confirms branch is ahead of base branch

**2. Generates PR content:**
- Analyzes all commits in the branch
- Reviews file changes and diffs
- Creates meaningful title based on changes
- Writes comprehensive description

**3. Creates the pull request:**
- Uses GitHub CLI (`gh pr create`) or GitHub API
- Sets base branch (usually main or develop)
- Sets head branch (Claude's working branch)
- Submits the pull request

**4. Confirms creation:**
- Provides PR number
- Shares direct link to the PR
- Displays PR title and summary

**Example output:**
```
✓ Pull request created successfully!

PR #42: Add dark mode toggle to navigation
https://github.com/username/myproject/pull/42

Summary:
- Added DarkModeToggle component
- Implemented theme context and hook
- Updated navigation to include toggle
- Added CSS variables for dark theme
```

### Step 5: Review the Generated PR

**Navigate to the PR:**
1. Click the provided link, or
2. Go to your repository on GitHub
3. Navigate to "Pull requests" tab
4. Find the newly created PR

**What to check:**

**PR Title:**
- Clear and descriptive
- Follows your project's conventions
- Accurately reflects the changes

**PR Description:**
- Comprehensive summary of changes
- Lists key files modified
- Explains implementation approach
- Includes testing steps or verification method
- References related issues (if applicable)

**Files Changed:**
- All intended changes are included
- No unexpected modifications
- Changes are on correct branch
- Commits are clean and logical

### Step 6: Make Adjustments (If Needed)

**If the PR needs modifications:**

**Option 1: Edit PR Details on GitHub**
- Click "Edit" on PR title or description
- Update content as needed
- Add additional context or screenshots
- Request specific reviewers
- Add labels or project associations

**Option 2: Ask Claude to Update**
```
Can you update the PR description to include more details about the testing approach?
```

```
Please add a screenshot to the PR showing the dark mode toggle in action.
```

**Option 3: Add More Commits**
```
The PR looks good, but can you also add unit tests for the DarkModeToggle component and push to the same PR?
```

Claude will:
- Add the tests
- Commit the changes
- Push to the same branch
- The PR automatically updates with new commits

### Step 7: Complete the PR Workflow

**After PR is created:**

**Review process:**
1. Request reviews from team members
2. Wait for CI/CD checks to pass
3. Address any feedback or requested changes
4. Approve when ready

**Merging:**
1. Ensure all checks pass
2. Get required approvals
3. Resolve any conflicts
4. Merge using your preferred method:
   - Merge commit
   - Squash and merge
   - Rebase and merge

**Post-merge:**
1. Delete the Claude branch (optional)
2. Verify changes in base branch
3. Close related issues
4. Document in release notes

## Expected Results

When successfully creating a PR automatically, you should:
- Receive a direct link to the newly created pull request
- See a PR number and title confirmation
- Find a comprehensive PR description on GitHub
- Have all commits from Claude's branch included
- See the PR ready for review and CI/CD execution
- Be able to merge after approval without additional setup
- Have a clean, reviewable change set for your team

## Troubleshooting

### Common Issue 1
**Problem**: Claude says it can't create a PR
**Solution**:
- Verify you have write permissions on the repository
- Check that changes are committed to a branch
- Ensure the branch is pushed to remote
- Confirm GitHub authentication is active
- Verify the repository allows PR creation
- Check if you're on a protected branch that requires different workflow

### Common Issue 2
**Problem**: PR targets wrong base branch
**Solution**:
- Specify the base branch explicitly: "Create a PR targeting develop"
- Check your repository's default branch settings
- You can change the base branch on GitHub after PR creation
- Go to PR → Edit → Change base branch
- Note: Changing base branch may affect the diff shown

### Common Issue 3
**Problem**: PR description is too brief or missing details
**Solution**:
- Ask Claude to update the PR description with more details
- Manually edit the PR description on GitHub
- For future PRs, specify what you want included: "Create a PR with detailed testing instructions"
- Consider creating a PR template in your repository (.github/pull_request_template.md)
- Claude will use the template if it exists

### Common Issue 4
**Problem**: PR includes unexpected changes or files
**Solution**:
- Review the branch before requesting PR creation
- Check git history to see what commits are included
- Use `git diff main...branch-name` to see full diff
- If unwanted changes exist, ask Claude to create a new branch with only desired changes
- Or manually cherry-pick commits to a new branch
- Create PR from the cleaned-up branch instead

### Common Issue 5
**Problem**: CI/CD checks fail immediately after PR creation
**Solution**:
- Review the failure messages in the PR
- Common issues: linting, test failures, build errors
- Ask Claude to fix the issues: "The PR checks are failing due to lint errors, can you fix them?"
- Claude can push fixes to the same branch
- Checks will re-run automatically after new commits

### Common Issue 6
**Problem**: Cannot find the created PR
**Solution**:
- Use the direct link Claude provided
- Check Pull Requests tab in repository
- Filter by "Open" pull requests
- Search by branch name (starts with "claude/")
- Verify PR wasn't accidentally created in a fork
- Check repository permissions if PR is not visible

## Additional Tips

### Best Practices for Automatic PR Creation

**Clear communication:**
- Be explicit about PR requirements upfront
- Specify base branch if not main/master
- Mention if you want draft PR vs. ready for review
- Request specific content in description if needed

**Draft PRs:**
```
Create a draft pull request so I can review before marking it ready.
```

Claude can create draft PRs that won't trigger review requests or notifications until you mark them ready.

**PR Templates:**
- Create `.github/pull_request_template.md` in your repository
- Claude will automatically use the template
- Ensures consistency across all PRs
- Prompts for required information

**Example template:**
```markdown
## Summary
<!-- Describe the changes -->

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
<!-- How has this been tested? -->

## Checklist
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No breaking changes
```

### Efficient Workflow Patterns

**Single command approach:**
```
Implement feature X, run tests to verify, and create a PR when everything passes.
```

**Batch operations:**
```
Fix bugs #123, #124, and #125, then create separate PRs for each fix.
```

**Coordinated PRs:**
```
Create the backend API changes on a PR targeting develop, and create a separate frontend PR that depends on it.
```

### PR Quality Tips

**Help Claude create better PRs:**

**Provide context:**
- Mention related issues or tickets
- Explain the "why" behind the change
- Note any tradeoffs or decisions made

**Request comprehensive descriptions:**
```
Create a PR with:
1. What was changed and why
2. How to test the changes
3. Any breaking changes or migrations needed
4. Screenshots if UI changes exist
```

**Specify conventions:**
```
Create a PR following our conventional commit format: "feat: description"
```

### Integration with GitHub Features

**Auto-linking issues:**
- Include "Closes #123" or "Fixes #456" in PR description
- GitHub automatically links and closes issues when PR merges
- Claude can include these automatically if you mention the issue

**Labels and projects:**
- After Claude creates PR, add labels manually or
- Use GitHub Actions to auto-label based on files changed
- Assign to projects for tracking

**Code owners:**
- If CODEOWNERS file exists, appropriate reviewers are auto-requested
- No need to specify reviewers in Claude's task

### Team Collaboration

**Review requests:**
```
Create a PR and request review from @alice and @bob
```

**Team notifications:**
- PR creation notifies watchers and subscribers
- Use draft PRs to avoid premature notifications
- Mark ready for review when actually ready

**Continuous integration:**
- PRs trigger CI/CD automatically
- Review check results before requesting human review
- Fix any automated failures first

### Version Control Strategy

**Branch naming:**
- Claude uses branches like `claude/feature-description`
- Follows common conventions automatically
- Can specify custom branch names if needed

**Commit messages:**
- Claude writes clear, descriptive commit messages
- Based on changes made and task context
- Follows conventional commit format when it detects your project uses it

**Multiple PRs from same session:**
```
Create two separate PRs:
1. One for the refactoring changes targeting main
2. One for the new feature targeting develop
```

## Related Articles
- KB-004: How to connect your first GitHub repository to Claude Code Web
- KB-015: How to view changes made by Claude Code in your repository
- KB-019: How to review completed sessions
- KB-014: How to understand which branch Claude Code is working on
- KB-006: How to create your first coding task in Claude Code Web

## Sources
1. Claude Code on the Web - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
2. Claude Code for web—a new asynchronous coding agent from Anthropic - https://simonwillison.net/2025/Oct/20/claude-code-for-web/ (Accessed: November 16, 2025)
3. Claude Code GitHub Actions Documentation - https://docs.claude.com/en/docs/claude-code/github-actions (Referenced: November 16, 2025)
4. GitHub Pull Requests Documentation - https://docs.github.com/en/pull-requests (Accessed: November 16, 2025)
5. How to Use Claude Code for PRs and Code Reviews - https://skywork.ai/blog/how-to-use-claude-code-for-prs-code-reviews-guide/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
