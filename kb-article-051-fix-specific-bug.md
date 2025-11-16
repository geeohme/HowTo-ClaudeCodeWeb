# How to fix a specific bug using Claude Code Web

**Article ID**: KB-051
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15-30 minutes

## Overview
This guide demonstrates how to effectively use Claude Code Web to diagnose and fix a specific bug in your codebase. Claude Code Web's agentic debugging capabilities allow you to provide error context, analyze issues across multiple files, and implement fixes directly in your cloud-based development environment. Unlike generic debugging approaches, this workflow emphasizes specificity and structured problem-solving to achieve faster, more accurate bug resolution.

## Prerequisites
- Active Claude Code Web session with your project loaded
- Basic understanding of your project's codebase and file structure
- Error message, stack trace, or clear description of the bug behavior
- Appropriate permissions to modify code files in your session
- (Optional) Related KB articles: KB-001 (Getting Started), KB-018 (Creating a Session)

## Steps

### Step 1: Gather Specific Bug Information
Before engaging Claude Code Web, collect precise details about the bug to maximize debugging efficiency.

**What to collect:**
- Exact error message or stack trace
- File name and line number where the error occurs
- Steps to reproduce the bug
- Expected vs. actual behavior
- Recent changes that may have introduced the bug

**Example error context:**
```
File: src/auth/middleware.py, line 45
Error: ValueError: Cookie SameSite attribute mismatch
Context: After upgrading Django from 4.2 to 5.0, CSRF validation fails on subdomain requests
```

**Pro tip:** Be specific rather than generic. Instead of asking "fix bugs," ask "in file X.py line 45, error Y occurs when Z happens - propose a patch and commit message."

### Step 2: Initiate Debugging with Clear Context
Provide Claude Code Web with structured information about the bug using a clear, detailed prompt.

**Effective debugging prompt structure:**
```
I have a bug in [FILE_NAME] at line [LINE_NUMBER].

Error: [EXACT_ERROR_MESSAGE]

Context:
- What I'm trying to do: [DESCRIPTION]
- What's happening instead: [ACTUAL_BEHAVIOR]
- Recent changes: [RELEVANT_CHANGES]

Please:
1. Analyze the error and identify the root cause
2. Check related files for similar issues
3. Propose a fix with explanation
4. Suggest how to prevent this in the future
```

**Example prompt:**
```
I have a bug in src/auth/middleware.py at line 45.

Error: ValueError: Cookie SameSite attribute mismatch on CSRF validation

Context:
- What I'm trying to do: Handle authentication across subdomains
- What's happening instead: CSRF tokens fail validation on subdomain requests
- Recent changes: Upgraded Django 4.2 → 5.0 last week

Please analyze and propose a fix.
```

### Step 3: Review Claude's Analysis
Claude Code Web will analyze the error by:
- Reading the specified file and surrounding context
- Examining related files (imports, dependencies, configuration)
- Identifying the root cause based on error patterns
- Formulating specific hypotheses about the issue

**What to look for in the analysis:**
- Does Claude identify the correct root cause?
- Are there secondary issues mentioned?
- Does the explanation make sense given your codebase?

If the initial analysis seems off-track, provide additional context:
```
Actually, the error only occurs in production, not locally.
Environment difference: Production uses HTTPS with subdomains.
```

### Step 4: Choose Your Fix Approach
Depending on the bug's complexity and your confidence level, select the appropriate approach:

**Approach A: Direct Fix (for straightforward bugs)**
Allow Claude to immediately implement the fix:
```
That analysis looks correct. Please implement the fix and show me the changes.
```

**Approach B: Plan Mode (for complex or critical bugs)**
Request a detailed plan before making changes:
```
Before making changes, please create a detailed fix plan including:
1. All files that need modification
2. Specific changes to each file
3. Potential side effects or risks
4. How to test the fix

I'll review before you proceed.
```

**Best practice:** Use Plan Mode for security-related bugs, database migrations, or changes affecting multiple systems.

### Step 5: Implement the Fix
Once you approve the approach, Claude Code Web will:
- Modify the necessary files using the Edit tool
- Make surgical, targeted changes to fix the specific issue
- Preserve existing code structure and style
- Show you exactly what changed

**Example fix for the CSRF cookie issue:**
```python
# File: src/auth/middleware.py
# Line 45-50

# BEFORE:
SESSION_COOKIE_SAMESITE = 'Lax'
CSRF_COOKIE_SAMESITE = 'Lax'

# AFTER:
SESSION_COOKIE_SAMESITE = 'None'
CSRF_COOKIE_SAMESITE = 'None'
SESSION_COOKIE_SECURE = True  # Required when SameSite=None
CSRF_COOKIE_SECURE = True
```

### Step 6: Verify the Fix
After implementation, verify that the bug is resolved:

**Verification checklist:**
1. **Run tests:** Execute relevant test suite
   ```
   Please run the authentication tests to verify the fix.
   ```

2. **Check for regressions:** Ensure the fix didn't break other functionality
   ```
   Run the full test suite and report any new failures.
   ```

3. **Manual testing:** Test the specific scenario that triggered the bug
   ```
   Let's test this by simulating a subdomain request in the dev environment.
   ```

4. **Code review:** Review the changes for quality and completeness
   ```
   Show me a diff of all changes made.
   ```

### Step 7: Document and Commit
Once verified, document the fix and commit the changes:

```
Create a commit with:
- Message: "Fix CSRF cookie SameSite mismatch for subdomain auth"
- Include details about the Django 5.0 compatibility issue
- Reference the error that was fixed
```

Claude Code Web will create a descriptive commit message following your repository's conventions.

## Expected Results
After completing this workflow, you should see:

✓ **Error eliminated:** The specific error no longer occurs under the same conditions
✓ **Tests passing:** All relevant tests execute successfully
✓ **Changes committed:** Fix is saved with clear commit message
✓ **Understanding gained:** You know why the bug occurred and how it was fixed
✓ **Prevention knowledge:** You understand how to avoid similar bugs

**Example success output:**
```
✓ Fixed cookie configuration in src/auth/middleware.py
✓ All 47 authentication tests passing
✓ Committed: "Fix CSRF cookie SameSite mismatch for subdomain auth"
✓ Root cause: Django 5.0 requires SameSite=None with Secure flag for cross-subdomain cookies
```

## Troubleshooting

### Common Issue 1: Claude Can't Locate the Error
**Problem:** Claude reports it cannot find the file or error mentioned
**Solution:**
- Verify the file path is correct and uses forward slashes
- Ensure the file is within your Claude Code Web session directory
- Use Glob tool to search for the file: "Please find all files named *middleware.py*"
- Provide the full file path from repository root

### Common Issue 2: Fix Doesn't Resolve the Bug
**Problem:** After implementing the fix, the bug still occurs
**Solution:**
- Provide additional context about environment differences
- Ask Claude to check related configuration files
- Request a deeper analysis: "The fix didn't work. Please analyze dependencies and imports for other potential causes."
- Consider if there are multiple bugs masquerading as one issue

### Common Issue 3: Fix Breaks Other Functionality
**Problem:** The fix resolves the bug but introduces new errors elsewhere
**Solution:**
- Run full test suite to identify all affected areas
- Request a more conservative fix: "This broke X. Please revise to fix the CSRF issue without changing the session cookie behavior."
- Use Plan Mode to review impact across the codebase before implementing
- Ask Claude to explain potential side effects before making changes

### Common Issue 4: Too Generic or Vague Analysis
**Problem:** Claude provides general advice instead of specific fixes
**Solution:**
- Provide more specific context (exact line numbers, error messages, stack traces)
- Share relevant code snippets from the error location
- Describe what you've already tried
- Ask pointed questions: "Why would line 45 throw this specific ValueError given the current code?"

## Additional Tips

- **Be specific:** The more precise your error description, the better the fix. Include file paths, line numbers, and exact error text.

- **Start simple:** For complex bugs, ask Claude to start with the most likely cause rather than analyzing everything at once.

- **Use git diff:** Before committing, review changes with "Show me a complete diff of all modified files."

- **Iterative debugging:** If the first fix doesn't work, provide the new error message and continue the dialogue.

- **Learn from fixes:** Ask Claude to explain the root cause and prevention strategies to improve your debugging skills.

- **Session context matters:** Claude Code Web maintains context within a session, so you can reference previous fixes and build on earlier analysis.

- **Test incrementally:** For multi-file fixes, test after each file modification to isolate which change resolves the bug.

- **Check dependencies:** Many bugs stem from version mismatches or dependency conflicts. Ask Claude to examine package.json, requirements.txt, or similar files.

## Related Articles
- KB-001: Getting Started with Claude Code Web
- KB-018: Creating and Managing Sessions
- KB-025: Running Tests in Claude Code Web
- KB-040: Using Plan Mode for Complex Changes
- KB-045: Committing Code Changes Effectively

## Sources
- Claude Code 2025 Bug & Backlog Workflow - https://skywork.ai/blog/agent/claude-code-2025-bug-backlog-workflow/ (Accessed: November 16, 2025)
- Debugging with Claude Code: How I Fixed 3 Real-World Bugs - https://skywork.ai/blog/debugging-with-claude-code-how-i-fixed-3-real-world-bugs/ (Accessed: November 16, 2025)
- How to Use Claude Code for Debugging - https://claudelog.com/faqs/how-to-use-claude-code-for-debugging/ (Accessed: November 16, 2025)
- Claude Code on the Web - https://www.claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
- Official Claude Code Troubleshooting Documentation - https://code.claude.com/docs/en/troubleshooting (Accessed: November 16, 2025)
- Google Search Query for Latest Updates: "Claude Code Web debugging workflow 2025"

---
*This article is part of the Claude Code Web knowledge base*
