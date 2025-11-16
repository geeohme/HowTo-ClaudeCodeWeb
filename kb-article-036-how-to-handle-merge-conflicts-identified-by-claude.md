# How to handle merge conflicts identified by Claude

**Article ID**: KB-036
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-20 minutes

## Overview
When working with branches in Claude Code Web, merge conflicts can occur when changes from different branches cannot be automatically reconciled by Git. Claude Code provides intelligent assistance to identify, analyze, and resolve these conflicts by examining both versions of conflicting code, understanding the intent behind each change, and suggesting resolutions that preserve the functionality of both branches. This guide walks you through the process of handling merge conflicts with Claude's help.

## Prerequisites
- Active Claude Code Web session with a connected repository
- Basic understanding of Git branches and merging concepts
- A repository with merge conflicts (or the ability to create them)
- Related articles: [KB-014: Understand working branch](/kb-article-014-understand-working-branch.md), [KB-015: View changes made by Claude](/kb-article-015-view-changes-made-by-claude.md)

## Steps

### Step 1: Identify the Merge Conflict
When Claude attempts to merge or rebase branches, it will detect conflicts and inform you. You may also encounter conflicts when:
- Merging the main branch into your feature branch
- Rebasing your branch onto the latest main
- Applying changes from a pull request

Claude will typically display a message indicating which files have conflicts, such as:
```
CONFLICT (content): Merge conflict in src/components/UserProfile.js
Automatic merge failed; fix conflicts and then commit the result.
```

Git conflict markers will appear in the affected files:
```javascript
<<<<<<< HEAD
function getUserData(userId) {
  return fetch(`/api/users/${userId}`);
}
=======
async function getUserData(userId) {
  return await api.fetchUser(userId);
}
>>>>>>> feature-branch
```

### Step 2: Ask Claude to Analyze the Conflict
Instead of manually parsing the conflict markers, ask Claude to help you understand what's happening. Use a conversational command like:

**Example 1 (Simple):**
```
"I have a merge conflict in src/components/UserProfile.js.
Can you help me understand what both changes are trying to accomplish?"
```

**Example 2 (Detailed):**
```
"Help me resolve the merge conflict in the user authentication module.
I need to understand which approach is better and why."
```

**Example 3 (Multiple files):**
```
"I have merge conflicts in 3 files after rebasing. Can you analyze each conflict
and suggest the best resolution while maintaining backward compatibility?"
```

### Step 3: Review Claude's Analysis
Claude will examine both versions of the conflicting code and provide:

1. **Context about each change**: What was the intent behind the changes in each branch
2. **Impact analysis**: How each version affects the codebase
3. **Recommended resolution**: A suggested approach that preserves the best aspects of both changes
4. **Reasoning**: Why the recommendation maintains functionality and code quality

Claude might respond with something like:
```
"I can see two different approaches here:

HEAD (current branch): Keeps the synchronous fetch approach
feature-branch: Introduces async/await pattern

The feature-branch version is better because:
- It provides better error handling capabilities
- It's more consistent with modern JavaScript practices
- Other parts of the codebase already use async/await

I recommend accepting the async version and ensuring all callers
handle the Promise correctly."
```

### Step 4: Request Claude to Resolve the Conflict
After understanding Claude's analysis, ask it to implement the resolution:

```
"Please resolve this conflict using the async approach you recommended.
Make sure to update any affected function calls as well."
```

Claude will:
- Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
- Implement the recommended solution
- Ensure the code is syntactically correct
- Update related code if necessary

### Step 5: Verify the Resolution
Ask Claude to explain what it changed:

```
"Can you show me the final resolved version and confirm all conflicts are fixed?"
```

Review the resolved code to ensure:
- All conflict markers are removed
- The logic makes sense and preserves intended functionality
- No syntax errors were introduced
- Related code is updated if needed

### Step 6: Test the Resolved Code
Before committing, ask Claude to verify the changes work correctly:

```
"Can you run the tests for the UserProfile component to ensure
the conflict resolution didn't break anything?"
```

Or request a broader test:
```
"Run all unit tests and integration tests related to user authentication."
```

### Step 7: Commit the Resolution
Once verified, ask Claude to commit the resolved changes:

```
"Please stage these resolved files and create a commit with an appropriate message."
```

Claude will typically create a commit message like:
```
chore: resolve merge conflicts in user authentication module

- Adopted async/await pattern for getUserData
- Updated all callers to handle Promise returns
- Maintained backward compatibility with existing API
```

### Step 8: Continue Your Workflow
After the conflict is resolved and committed:
- If you were merging: complete the merge operation
- If you were rebasing: continue the rebase (`git rebase --continue`)
- Ask Claude to push changes if ready: `"Push these changes to my branch"`

## Expected Results
After successfully handling merge conflicts with Claude:
- All conflict markers are removed from your files
- The code compiles and runs without errors
- Tests pass successfully
- Your branch is ready to continue development or create a pull request
- Git history shows a clean resolution commit
- You understand why the conflict occurred and how it was resolved

## Troubleshooting

### Common Issue 1
**Problem**: Claude suggests a resolution, but you prefer a different approach
**Solution**: Provide specific guidance: "I'd like to keep the synchronous version instead because our API client doesn't support async yet. Can you resolve the conflict that way and ensure compatibility?" Claude will adjust its approach based on your requirements.

### Common Issue 2
**Problem**: Multiple conflicts across many files seem overwhelming
**Solution**: Ask Claude to handle them systematically: "Let's resolve these conflicts one file at a time, starting with the most critical ones in the core authentication module." You can also create a custom slash command for systematic conflict resolution during complex rebases.

### Common Issue 3
**Problem**: After resolving, new conflicts appear
**Solution**: This often happens during rebases with multiple commits. Ask Claude: "Continue resolving the remaining conflicts in the rebase process." Claude can handle iterative conflict resolution as you progress through commits.

### Common Issue 4
**Problem**: Unsure if Claude's suggested resolution maintains the functionality of both changes
**Solution**: Request a detailed explanation: "Can you explain step-by-step how your resolution preserves the functionality from both branches?" or "Show me test cases that verify both feature sets still work." This gives you confidence in the resolution.

## Additional Tips
- **Use descriptive branch names**: This helps Claude understand the context and purpose of changes when analyzing conflicts
- **Ask for explanations**: If you're learning, ask Claude to explain the conflict in detail so you understand Git conflict resolution better
- **Leverage Claude's code understanding**: Unlike traditional merge tools, Claude understands the semantic meaning of code, not just text differences
- **Create custom commands**: For projects with frequent conflicts, consider creating a `/resolve-conflicts` slash command that follows your team's specific resolution process
- **Request diff reviews**: Before committing resolutions, ask Claude to show you a diff of what changed: "Show me the diff between the original conflicted version and your resolution"
- **Batch similar conflicts**: If multiple files have similar conflicts (e.g., API signature changes), ask Claude to apply the same resolution pattern across all files
- **Preserve both features when possible**: Claude excels at finding creative solutions that maintain functionality from both branches rather than choosing one over the other

## Related Articles
- [KB-014: Understand working branch](/kb-article-014-understand-working-branch.md)
- [KB-015: View changes made by Claude](/kb-article-015-view-changes-made-by-claude.md)
- [KB-031: How to create a new branch for a specific task](/kb-article-031-how-to-create-a-new-branch-for-a-specific-task.md)
- [KB-033: How to create a pull request automatically from completed work](/kb-article-033-how-to-create-a-pull-request-automatically-from-completed-work.md)

## Sources
1. Anthropic - Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
2. Claude Code Documentation - Overview - https://docs.claude.com/en/docs/claude-code/overview (Accessed: November 16, 2025)
3. ClaudeCode.io - Git Workflow Guide - https://claudecode.io/guides/git-workflow (Accessed: November 16, 2025)
4. Skywork AI - Claude Code 2.0 Best Practices for AI Coding Workflow 2025 - https://skywork.ai/blog/claude-code-2-0-best-practices-ai-coding-workflow-2025/ (Accessed: November 16, 2025)
5. Claude Skills - Conflict Resolver - https://claude-plugins.dev/skills/@CuriousLearner/devkit/conflict-resolver (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
