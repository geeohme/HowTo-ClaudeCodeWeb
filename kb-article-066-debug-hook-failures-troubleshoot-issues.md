# How to debug hook failures and troubleshoot issues

**Article ID**: KB-066
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
Hooks in Claude Code Web are powerful automation tools, but when they fail or behave unexpectedly, they can disrupt your development workflow. This advanced guide covers systematic approaches to debugging hook failures, interpreting exit codes and error messages, using diagnostic tools to inspect hook execution, and resolving common issues that prevent hooks from running correctly in both local and web environments.

## Prerequisites
- Active Claude Code Web session with hooks configured
- Basic understanding of hooks and their lifecycle events
- Familiarity with shell scripting and exit codes
- Access to repository with `.claude/hooks/` directory
- Knowledge of your operating system's command line interface
- Understanding of environment variables and process execution

## Understanding Hook Failure Modes

Before debugging, understand how hooks communicate status to Claude Code:

### Exit Code Behavior
Hooks use exit codes to signal their status:

- **Exit code 0**: Success - hook completed successfully
- **Exit code 2**: Blocking error - execution stops, stderr is fed back to Claude automatically
- **Other exit codes**: Non-blocking error - stderr shown to user, execution continues

### Hook-Specific Error Handling

**SessionStart hooks:**
- Exit code 0: stdout added to Claude's context automatically
- Exit code 2: stderr processed by Claude, session may not start
- Other codes: Error shown, session continues with warning

**PostToolUse hooks:**
- Failures typically don't block Claude's operation
- Debug output appears in transcript mode
- Failed hooks are logged but don't halt the workflow

**Stop hooks:**
- Can cause infinite loops if they continuously fail
- Each failure triggers another attempt, creating repeated feedback

## Steps

### Step 1: Enable Debug Logging

Debug logging reveals what hooks are executing and their results.

**Check if debug mode is enabled:**

Claude Code shows debug messages like:
```
[DEBUG] Executing hooks for PostToolUse:Write
[DEBUG] Hook command completed with status 0
```

**Enable verbose logging:**

If you're not seeing debug output, ensure your session has verbose logging enabled. In Claude Code Web, hook execution details appear in the conversation transcript.

**Monitor the transcript:**
Watch for these indicators:
- Hook execution start messages
- Command output (stdout/stderr)
- Exit code reports
- Timing information

Debug logging tells you:
1. Which hooks are being triggered
2. What commands are running
3. Whether they succeed or fail
4. How long they take to execute

### Step 2: Verify Hook Configuration and Permissions

Many hook failures stem from configuration issues.

**Check hook file structure:**

Your hooks should be in `.claude/hooks/` with this structure:
```
.claude/
└── hooks/
    ├── SessionStart.json
    ├── PostToolUse.json
    └── Stop.json
```

**Verify JSON syntax:**

Ask Claude to validate your hook configuration:
```
Validate the JSON syntax in .claude/hooks/SessionStart.json
Check for syntax errors, invalid fields, or malformed structures.
```

Common JSON errors:
- Missing commas between array elements
- Trailing commas (invalid in strict JSON)
- Incorrect quote types (must use double quotes)
- Missing brackets or braces

**Example valid hook configuration:**
```json
[
  {
    "pattern": "**/*.py",
    "command": "python -m black ${FILE_PATH}"
  }
]
```

**Check file permissions:**

On Linux/Mac, hooks directory should be readable:
```bash
ls -la .claude/hooks/
```

Ensure:
- Hook files are readable (at least `r--`)
- Directory is accessible (`r-x`)
- No permission denied errors in logs

### Step 3: Test Hooks in Isolation

Debug hooks outside of Claude Code's execution to verify they work independently.

**Extract the hook command:**

From your hook configuration, identify the exact command being run. For example, if your PostToolUse hook has:
```json
{
  "pattern": "**/*.js",
  "command": "eslint ${FILE_PATH} --fix"
}
```

**Test manually:**

Run the command directly with a real file path:
```bash
# Replace ${FILE_PATH} with an actual file
eslint src/components/Button.js --fix
```

**Check for issues:**
- Does the command exist on your system?
- Are dependencies installed (e.g., eslint)?
- Does it work with the expected file paths?
- What exit code does it return?

**Verify exit codes:**
```bash
eslint src/components/Button.js --fix
echo $?  # Shows exit code (0 = success)
```

**Test with environment variables:**

Hooks receive specific environment variables. Simulate the hook environment:
```bash
# Test with the variables hooks receive
export FILE_PATH="src/test.js"
export CLAUDE_CODE_REMOTE="true"  # If testing web environment
eslint ${FILE_PATH} --fix
```

### Step 4: Use the Diagnostic Hook Technique

Create a special diagnostic hook to inspect what data Claude provides to hooks.

**Create a PostToolUse diagnostic hook:**

Add this to `.claude/hooks/PostToolUse.json`:
```json
[
  {
    "pattern": "**/*",
    "command": "cat > /tmp/hook-debug-$(date +%s).json"
  }
]
```

**How it works:**
- Triggers on every file modification
- Captures the JSON input Claude sends to hooks
- Saves to timestamped files in `/tmp/`

**Inspect the captured data:**
```bash
# View the most recent hook data
ls -lt /tmp/hook-debug-*.json | head -1 | awk '{print $9}' | xargs cat
```

**What you'll see:**
```json
{
  "tool": "Write",
  "file_path": "/path/to/file.js",
  "content": "...",
  "timestamp": "2025-11-16T10:30:00Z"
}
```

This reveals:
- Exact file paths being passed
- Tool that triggered the hook
- Available context data
- Why pattern matching might fail

**Pro Tip**: After collecting diagnostic data, remove or disable this hook to avoid cluttering `/tmp/`.

### Step 5: Check Environment-Specific Behavior

Hooks behave differently in web vs. local environments.

**Identify your environment:**

Check the `CLAUDE_CODE_REMOTE` environment variable in your hooks:

```bash
#!/bin/bash
if [ "$CLAUDE_CODE_REMOTE" = "true" ]; then
  echo "Running in Claude Code Web"
else
  echo "Running in Claude Code CLI"
fi
```

**Web environment limitations:**

In Claude Code Web:
- Limited filesystem access (sandboxed)
- No access to local binaries unless pre-installed
- Network restrictions may apply
- Different PATH environment
- Restricted system commands

**Adapt hooks for web environments:**

```bash
#!/bin/bash
# Check if command exists before using it
if command -v eslint &> /dev/null; then
  eslint ${FILE_PATH} --fix
else
  echo "eslint not available in this environment" >&2
  exit 1
fi
```

**Test environment-specific paths:**

Web and local environments may have different:
- Working directories
- Temporary file locations
- Command installation paths
- Available interpreters (python, node, etc.)

**Request environment inspection from Claude:**
```
Check what development tools are available in the current environment.
List installed interpreters (python, node, ruby) and linters (eslint, black, rubocop).
```

### Step 6: Analyze Hook Execution Timing

Performance issues can cause hooks to appear as failures.

**Identify slow hooks:**

If hooks take too long, they may:
- Timeout before completing
- Block other operations
- Cause user experience degradation

**Add timing to your hooks:**

```bash
#!/bin/bash
START=$(date +%s)

# Your hook command here
eslint ${FILE_PATH} --fix

END=$(date +%s)
DURATION=$((END - START))

echo "Hook completed in ${DURATION} seconds" >&2
exit 0
```

**Watch for timeout warnings:**

Claude Code may terminate hooks that run too long. Look for:
- Truncated output
- Timeout messages
- Incomplete operations

**Optimize slow hooks:**

For performance-intensive hooks:
```bash
#!/bin/bash
# Only run linter on files smaller than 1MB
if [ $(stat -f%z "${FILE_PATH}") -lt 1048576 ]; then
  eslint ${FILE_PATH} --fix
else
  echo "Skipping large file: ${FILE_PATH}" >&2
fi
```

### Step 7: Debug Pattern Matching Issues

Hooks only trigger when file patterns match. Misconfigurations prevent execution.

**Understand glob patterns:**

Common patterns:
- `**/*.js` - All JavaScript files in any directory
- `src/**/*.py` - All Python files under src/
- `*.{ts,tsx}` - TypeScript files in root only
- `**/*test*.js` - All files with "test" in the name

**Test pattern matching:**

Ask Claude to verify which files match your pattern:
```
List all files in the repository that match the pattern "src/**/*.js"
Show me if src/components/Button.jsx matches this pattern.
```

**Common pattern mistakes:**

```json
// WRONG - missing ** for subdirectories
{"pattern": "*.js", "command": "..."}  // Only matches root level

// CORRECT - matches all subdirectories
{"pattern": "**/*.js", "command": "..."}

// WRONG - backslashes on Unix systems
{"pattern": "src\\**\\*.py", "command": "..."}

// CORRECT - forward slashes
{"pattern": "src/**/*.py", "command": "..."}
```

**Debug pattern matching with wildcards:**

Create a test hook that logs matched files:
```json
{
  "pattern": "**/*.js",
  "command": "echo 'Matched: ${FILE_PATH}' >> /tmp/hook-matches.log"
}
```

**Verify path formats:**

File paths should be:
- Absolute paths in most cases
- Use forward slashes (even on Windows in web environment)
- Properly escaped if containing spaces

### Step 8: Handle SessionStart Hook Failures

SessionStart hooks are critical and require special debugging.

**SessionStart-specific features:**

These hooks have access to:
- `CLAUDE_ENV_FILE` - Path to write persistent environment variables
- stdout added to Claude's context on success
- Ability to block session start on critical failures

**Debug SessionStart with explicit logging:**

```bash
#!/bin/bash
LOG_FILE="/tmp/session-start.log"
echo "SessionStart hook beginning at $(date)" > "$LOG_FILE"

# Setup operations
echo "Installing dependencies..." >> "$LOG_FILE"
npm install >> "$LOG_FILE" 2>&1

if [ $? -eq 0 ]; then
  echo "Dependencies installed successfully" >> "$LOG_FILE"
  echo "Environment ready" # This goes to Claude's context
  exit 0
else
  echo "Failed to install dependencies" >&2
  exit 2  # Blocking error
fi
```

**Set persistent environment variables:**

```bash
#!/bin/bash
# SessionStart hook that sets environment variables
if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo "export PROJECT_ROOT=$(pwd)" >> "$CLAUDE_ENV_FILE"
  echo "export NODE_ENV=development" >> "$CLAUDE_ENV_FILE"
  echo "Environment variables set" >&2
fi
```

**Test SessionStart hooks:**

```
Start a new session and watch for SessionStart hook execution.
Show me any errors or warnings from the hook.
Check if environment variables were set correctly.
```

**Recover from SessionStart failures:**

If SessionStart fails with exit code 2:
- Session may not start properly
- Review stderr output shown by Claude
- Fix the hook issue
- Request a new session or reconnect

### Step 9: Debug Hook Infinite Loops

Stop hooks can create infinite loops when they fail repeatedly.

**Identify infinite loop symptoms:**

- Repeated error messages appearing continuously
- Claude showing same failure output multiple times
- Session becoming unresponsive
- Hook executing after every failed attempt

**Known issue with Stop hooks:**

When a Stop hook fails, Claude Code may trigger it again, causing:
```
[ERROR] Stop hook failed with exit code 1
[DEBUG] Executing hooks for Stop
[ERROR] Stop hook failed with exit code 1
[DEBUG] Executing hooks for Stop
[ERROR] Stop hook failed with exit code 1
...
```

**Break the infinite loop:**

Immediate action:
1. Cancel or terminate the current session
2. Edit `.claude/hooks/Stop.json`
3. Fix the failing command or remove the hook
4. Start a new session

**Add defensive error handling to Stop hooks:**

```bash
#!/bin/bash
# Stop hook with error handling to prevent loops

cleanup_resources() {
  # Attempt cleanup, but don't fail if already clean
  docker-compose down 2>/dev/null || true
  rm -f /tmp/session-* 2>/dev/null || true
}

cleanup_resources

# Always exit 0 to prevent infinite loop
exit 0
```

**Pro Tip**: Stop hooks should always succeed (exit 0) unless truly critical cleanup is required. Use exit code 2 sparingly.

### Step 10: Use the /hooks Command for Management

Claude Code provides a `/hooks` command for interactive hook management.

**View configured hooks:**
```
/hooks
```

This shows:
- All configured hooks and their patterns
- Commands that will execute
- Current hook status
- Available actions (edit, test, disable)

**Test hooks through the interface:**

The `/hooks` command may provide:
- Ability to trigger hooks manually
- Preview what files match patterns
- View recent hook execution logs
- Edit hook configurations interactively

**Temporarily disable problematic hooks:**

While debugging:
1. Rename the hook file to disable it:
   ```bash
   mv .claude/hooks/PostToolUse.json .claude/hooks/PostToolUse.json.disabled
   ```

2. Fix the issue in isolation

3. Re-enable when ready:
   ```bash
   mv .claude/hooks/PostToolUse.json.disabled .claude/hooks/PostToolUse.json
   ```

### Step 11: Capture and Analyze stderr/stdout

Proper output handling helps debug what went wrong.

**Understanding output streams:**

Hooks can write to:
- **stdout** - Normal output (shown to user or added to context)
- **stderr** - Error output (shown to user, may trigger different handling)

**Redirect output for debugging:**

```bash
#!/bin/bash
# Capture all output for debugging
exec 1> >(tee -a /tmp/hook-stdout.log)
exec 2> >(tee -a /tmp/hook-stderr.log)

echo "Starting hook execution"
python scripts/format.py "${FILE_PATH}"
RESULT=$?

echo "Hook finished with exit code $RESULT"
exit $RESULT
```

**Analyze captured output:**
```bash
# Check what the hook actually output
tail -n 50 /tmp/hook-stdout.log
tail -n 50 /tmp/hook-stderr.log
```

**Signal errors correctly:**

```bash
#!/bin/bash
if [ ! -f "${FILE_PATH}" ]; then
  echo "Error: File does not exist: ${FILE_PATH}" >&2
  exit 1  # Non-blocking error
fi

# Critical error that should stop execution
if [ ! -w "${FILE_PATH}" ]; then
  echo "Critical: Cannot write to file: ${FILE_PATH}" >&2
  exit 2  # Blocking error
fi
```

### Step 12: Debug Cross-Platform Hook Issues

Hooks may behave differently across operating systems.

**Common cross-platform issues:**

**Shebang lines:**
```bash
#!/bin/bash  # Works on Linux/Mac
#!/usr/bin/env bash  # More portable

# Windows WSL may need:
#!/bin/bash
set -e  # Exit on error
```

**Path separators:**
```bash
# WRONG - assumes Unix paths
FULL_PATH="$PROJECT_ROOT/$FILE_PATH"

# CORRECT - handle any path format
FULL_PATH="$(realpath "${FILE_PATH}")"
```

**Line endings:**
- Windows uses CRLF (`\r\n`)
- Unix uses LF (`\n`)
- Hooks may fail if line endings are wrong

**Fix line endings:**
```bash
# Ensure LF line endings in hook files
dos2unix .claude/hooks/*.json
```

**Command availability:**
```bash
# Check before using OS-specific commands
if [[ "$OSTYPE" == "darwin"* ]]; then
  # macOS specific
  stat -f%z "${FILE_PATH}"
elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
  # Linux specific
  stat -c%s "${FILE_PATH}"
fi
```

## Expected Results

After successfully debugging hooks, you should achieve:

### Working Hook Execution
- **Hooks trigger reliably**: Match expected patterns and execute when files change
- **Correct exit codes**: Success (0), blocking errors (2), or non-blocking errors (other)
- **Proper output**: stdout/stderr handled correctly based on hook type
- **Expected timing**: Hooks complete within reasonable timeframes

### Comprehensive Debugging Capability
- **Clear error messages**: Understand exactly why hooks fail
- **Diagnostic data**: Captured JSON showing what Claude passes to hooks
- **Environment awareness**: Know what tools and paths are available
- **Pattern matching verification**: Confidence in which files trigger hooks

### Reliable Development Workflow
- **No unexpected failures**: Hooks execute consistently across sessions
- **Appropriate error handling**: Failures don't block workflow inappropriately
- **Clean session starts**: SessionStart hooks initialize environment correctly
- **Graceful cleanup**: Stop hooks complete without infinite loops

## Troubleshooting

### Common Issue 1
**Problem**: Hook configured but never executes, no debug output appears
**Solution**:
- Verify hook file is in `.claude/hooks/` directory (not `.claude/hook/` or `claude/hooks/`)
- Check JSON syntax with a validator: `jq . .claude/hooks/SessionStart.json`
- Ensure file pattern actually matches files you're modifying
- Test pattern: "Create a file matching pattern `src/**/*.js` and verify the hook triggers"
- Check that the hook file has proper read permissions
- Verify you're modifying files, not just reading them (PostToolUse only triggers on file writes)

### Common Issue 2
**Problem**: Hook executes but command not found errors occur
**Solution**:
- Verify the command exists: `which eslint` or `command -v black`
- Check if running in web environment where tools may not be pre-installed
- Install missing dependencies: "Install eslint as a dev dependency in package.json"
- Use absolute paths: `/usr/local/bin/python` instead of `python`
- Add conditional execution: Only run hook if command exists
- For web environment, request pre-installed tools or use alternatives

### Common Issue 3
**Problem**: SessionStart hook fails and blocks session initialization
**Solution**:
- Check stderr output that Claude shows - it contains the error details
- Temporarily disable SessionStart: `mv .claude/hooks/SessionStart.json .claude/hooks/SessionStart.json.disabled`
- Start a new session without the hook
- Debug the SessionStart script manually: Run commands from the hook one by one
- Fix the issue, re-enable, and start fresh session
- Use exit code 1 instead of 2 if failure shouldn't block session start

### Common Issue 4
**Problem**: Stop hook creates infinite loop and session becomes unresponsive
**Solution**:
- Immediately cancel/terminate the current session
- In a new session or locally, edit `.claude/hooks/Stop.json`
- Change hook to always exit 0: Add `|| exit 0` after commands
- Remove complex cleanup logic that might fail
- Test Stop hook manually before relying on it
- Consider using non-blocking exit codes (1) instead of blocking (2)

### Common Issue 5
**Problem**: Hook works locally but fails in Claude Code Web environment
**Solution**:
- Check `CLAUDE_CODE_REMOTE` environment variable in your hook
- Request list of pre-installed tools: "What linters and formatters are available?"
- Adapt hook to check for command existence before running
- Use web-compatible alternatives (e.g., tools available in npm instead of system tools)
- Consider different file system paths in sandboxed environment
- Test hook in web environment specifically before relying on it

### Common Issue 6
**Problem**: Hook produces unexpected output or modifies files incorrectly
**Solution**:
- Use the diagnostic hook technique to capture input data
- Verify file paths are correct and absolute
- Check if environment variables contain expected values
- Test hook command manually with captured data
- Review pattern matching - may trigger on unintended files
- Add logging: Echo debug information to stderr for visibility

### Common Issue 7
**Problem**: Hook performance degrades session responsiveness
**Solution**:
- Add timing measurements to identify slow operations
- Skip large files: Check file size before processing
- Limit hook scope: Use more specific patterns (e.g., `src/**/*.js` not `**/*`)
- Run expensive operations asynchronously if possible
- Cache results when appropriate
- Consider if hook is really necessary for every file change

## Additional Tips

- **Start simple**: Create minimal hooks that just echo messages before adding complex logic. Verify they trigger correctly first.

- **Version control hooks**: Commit `.claude/hooks/` to your repository so the team shares configurations and improvements.

- **Document hook purpose**: Add comments in hook files explaining what they do and why they exist.

- **Use exit codes intentionally**: Reserve exit code 2 for truly critical failures. Most hooks should use 0 (success) or 1 (non-blocking error).

- **Leverage hook templates**: Create reusable hook patterns for common tasks (formatting, linting, testing) and adapt them per project.

- **Test before deploying**: Always test hooks in isolation before relying on them in active development sessions.

- **Monitor hook execution**: Keep logs of hook activity to identify patterns in failures or performance issues.

- **Keep hooks fast**: Hooks that take >1 second should be optimized or made more selective in their pattern matching.

- **Graceful degradation**: Hooks should enhance workflow, not block it. Make them fault-tolerant.

- **Environment detection**: Write hooks that adapt to their environment (web vs local, development vs CI).

- **Clear error messages**: When hooks fail, their stderr should clearly explain what went wrong and how to fix it.

- **Avoid hook dependencies**: Don't make one hook depend on another completing successfully—it creates fragile chains.

- **Review official examples**: Check Claude Code documentation for hook best practices and proven patterns.

## Related Articles
- KB-044: How to use the /config command to configure Claude Code settings
- KB-043: How to configure project-specific settings in .claude/settings.json
- KB-042: How to set environment variables for your project
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-050: How to audit and review Claude Code's actions

## Sources
- Hooks reference - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
- A complete guide to hooks in Claude Code: Automating your development workflow - eesel AI - https://www.eesel.ai/blog/hooks-in-claude-code (Accessed: November 16, 2025)
- Complete Guide: Creating Claude Code Hooks - Suite Insider - https://suiteinsider.com/complete-guide-creating-claude-code-hooks/ (Accessed: November 16, 2025)
- Hooks not executing despite following documentation · Issue #2891 - GitHub - https://github.com/anthropics/claude-code/issues/2891 (Accessed: November 16, 2025)
- Feature Request: Introduce SessionStart and SessionEnd Lifecycle Hooks · Issue #4318 - GitHub - https://github.com/anthropics/claude-code/issues/4318 (Accessed: November 16, 2025)
- Demystifying Claude Code Hooks - Aaron Brethorst - https://www.brethorsting.com/blog/2025/08/demystifying-claude-code-hooks/ (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code Web hooks debugging exit codes troubleshooting 2025"

---
*This article is part of the Claude Code Web knowledge base*
