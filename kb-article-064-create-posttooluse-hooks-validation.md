# How to create PostToolUse hooks for validation

**Article ID**: KB-064
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
PostToolUse hooks are automated scripts that execute immediately after Claude Code successfully completes a tool operation (like editing or writing files). They enable real-time validation, quality checks, and automated workflows without preventing tool execution, making them ideal for enforcing code standards, running tests, and providing immediate feedback to Claude about the results of its actions.

## Prerequisites
- Active Claude Code Web session with a connected repository
- Understanding of JSON configuration format
- Familiarity with command-line tools (linters, formatters, test runners)
- Basic knowledge of shell scripting for creating hook scripts
- Related: KB-038 (Access sandboxed environment settings)

## Steps

### Step 1: Understand PostToolUse Hook Behavior

PostToolUse hooks have unique characteristics that differentiate them from other hook types:

**Key Properties:**
- **Timing**: Fires after tool execution completes successfully
- **Non-blocking**: Cannot prevent the tool action (it already happened)
- **Use cases**: Validation, formatting, cleanup, logging, feedback
- **Input**: Receives tool information via stdin as JSON and environment variables

PostToolUse hooks are perfect for observability and validation scenarios where you want to verify results and provide feedback without blocking Claude's workflow.

### Step 2: Create the .claude/settings.json configuration file

Navigate to your repository's root and create or edit `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate.sh"
          }
        ]
      }
    ]
  }
}
```

**Configuration breakdown:**
- `"matcher"`: Specifies which tools trigger the hook (case-sensitive)
- `"type"`: Set to `"command"` for executing scripts or commands
- `"command"`: The script or command to execute

### Step 3: Configure matchers for targeted validation

Matchers control which tool operations trigger your PostToolUse hook:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "*",
        "hooks": [...]
      },
      {
        "matcher": "Write|Edit",
        "hooks": [...]
      },
      {
        "matcher": "Edit:*.ts|Edit:*.tsx",
        "hooks": [...]
      },
      {
        "matcher": "Write:*.py",
        "hooks": [...]
      },
      {
        "matcher": "Bash",
        "hooks": [...]
      }
    ]
  }
}
```

**Matcher patterns:**
- `"*"` - All tools
- `"Write"` - Only Write operations
- `"Write|Edit"` - Multiple tools (OR condition)
- `"Edit:*.ts"` - File-specific patterns
- `"Bash"` - Bash command execution

### Step 4: Create a basic validation hook script

Create `.claude/hooks/validate.sh` in your repository:

```bash
#!/bin/bash

# Read JSON input from stdin
input=$(cat)

# Parse tool information using jq
tool_name=$(echo "$input" | jq -r '.tool')
tool_input=$(echo "$input" | jq -r '.tool_input')
tool_response=$(echo "$input" | jq -r '.tool_response')

# Access environment variables
file_paths="$CLAUDE_FILE_PATHS"
project_dir="$CLAUDE_PROJECT_DIR"

# Example: Validate Write operation success
if [ "$tool_name" = "Write" ]; then
    success=$(echo "$tool_response" | jq -r '.success')

    if [ "$success" != "true" ]; then
        # Return blocking feedback to Claude
        echo '{"decision": "block", "reason": "File write operation failed. Please check file permissions and retry."}'
        exit 0
    fi
fi

# Success - continue normally
echo "Validation passed"
exit 0
```

Make the script executable:
```bash
chmod +x .claude/hooks/validate.sh
```

### Step 5: Implement type checking validation

Create a PostToolUse hook for automatic TypeScript type checking:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit:*.ts|Edit:*.tsx|Write:*.ts|Write:*.tsx",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/type-check.sh"
          }
        ]
      }
    ]
  }
}
```

Create `.claude/hooks/type-check.sh`:

```bash
#!/bin/bash

# Read input
input=$(cat)
file_path=$(echo "$input" | jq -r '.tool_input.file_path')

echo "Running type check on: $file_path"

# Run TypeScript type checker
if command -v npx &> /dev/null; then
    output=$(npx tsc --noEmit "$file_path" 2>&1)
    exit_code=$?

    if [ $exit_code -ne 0 ]; then
        # Type errors found - provide feedback
        echo '{"decision": "block", "reason": "Type checking failed:\n'"$output"'"}'
        exit 0
    fi
fi

echo "Type checking passed"
exit 0
```

### Step 6: Create automated test execution hook

Set up a hook that runs tests automatically after file modifications:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit:*.test.*|Edit:*.spec.*",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/run-tests.sh"
          }
        ]
      }
    ]
  }
}
```

Create `.claude/hooks/run-tests.sh`:

```bash
#!/bin/bash

# Read input and extract file path
input=$(cat)
file_path=$(echo "$input" | jq -r '.tool_input.file_path')

echo "Running tests for: $file_path"

# Run tests related to the modified file
if command -v npm &> /dev/null; then
    test_output=$(npm test -- "$file_path" 2>&1)
    exit_code=$?

    if [ $exit_code -ne 0 ]; then
        # Tests failed - provide detailed feedback
        echo "{\"decision\": \"block\", \"reason\": \"Tests failed for $file_path. Please review and fix:\\n$test_output\"}"
        exit 0
    fi
fi

echo "All tests passed"
exit 0
```

### Step 7: Implement code formatting validation

Create a hook that validates code formatting:

```bash
#!/bin/bash
# .claude/hooks/format-check.sh

input=$(cat)
file_paths="$CLAUDE_FILE_PATHS"

# Check if Prettier is available
if command -v npx &> /dev/null; then
    for file in $file_paths; do
        # Check if file needs formatting
        if ! npx prettier --check "$file" &> /dev/null; then
            # Auto-format the file
            npx prettier --write "$file"

            # Provide feedback
            echo "{\"decision\": \"block\", \"reason\": \"File $file was auto-formatted to match style guidelines. Please review the formatting changes.\"}"
            exit 0
        fi
    done
fi

echo "Formatting validation passed"
exit 0
```

### Step 8: Use exit codes for control flow

PostToolUse hooks use exit codes to control behavior:

```bash
#!/bin/bash

input=$(cat)
tool_name=$(echo "$input" | jq -r '.tool')

# Perform validation logic
validation_result=$(some_validation_function)

if [ "$validation_result" = "critical_error" ]; then
    # Exit code 2: Blocking error with feedback to Claude
    echo '{"decision": "block", "reason": "Critical validation error detected"}'
    exit 2
elif [ "$validation_result" = "warning" ]; then
    # Exit code 1 or other: Non-blocking error (shows to user)
    echo "Warning: Minor issue detected" >&2
    exit 1
else
    # Exit code 0: Success
    echo "Validation successful"
    exit 0
fi
```

**Exit code behavior:**
- **0**: Success - execution continues normally
- **2**: Blocking error - provides feedback to Claude for correction
- **Other**: Non-blocking error - shows message to user but continues

### Step 9: Access environment variables and tool data

PostToolUse hooks have access to several environment variables:

```bash
#!/bin/bash

# Environment variables available in PostToolUse hooks
echo "Project Directory: $CLAUDE_PROJECT_DIR"
echo "Modified Files: $CLAUDE_FILE_PATHS"
echo "Execution Context: $CLAUDE_CODE_REMOTE"  # "true" for web, empty for CLI

# Read full tool data from stdin
input=$(cat)

# Parse tool information
tool=$(echo "$input" | jq -r '.tool')
tool_input=$(echo "$input" | jq -r '.tool_input')
tool_response=$(echo "$input" | jq -r '.tool_response')

# Example: Extract specific fields
if [ "$tool" = "Edit" ]; then
    file_path=$(echo "$tool_input" | jq -r '.file_path')
    old_string=$(echo "$tool_input" | jq -r '.old_string')
    new_string=$(echo "$tool_input" | jq -r '.new_string')

    echo "File edited: $file_path"
fi
```

### Step 10: Combine multiple validation checks

Create a comprehensive validation hook:

```bash
#!/bin/bash
# .claude/hooks/comprehensive-validate.sh

input=$(cat)
tool=$(echo "$input" | jq -r '.tool')
file_paths="$CLAUDE_FILE_PATHS"

errors=""

# Run multiple validations
for file in $file_paths; do
    # Check 1: Linting
    if [[ "$file" == *.js ]] || [[ "$file" == *.ts ]]; then
        if ! npx eslint "$file" &> /dev/null; then
            errors="$errors\n- Linting errors in $file"
        fi
    fi

    # Check 2: Type checking
    if [[ "$file" == *.ts ]]; then
        if ! npx tsc --noEmit "$file" &> /dev/null; then
            errors="$errors\n- Type errors in $file"
        fi
    fi

    # Check 3: Test coverage
    if [[ "$file" != *.test.* ]] && [[ "$file" == *.ts ]]; then
        test_file="${file%.ts}.test.ts"
        if [ ! -f "$test_file" ]; then
            errors="$errors\n- Missing test file: $test_file"
        fi
    fi
done

if [ -n "$errors" ]; then
    echo "{\"decision\": \"block\", \"reason\": \"Validation failures:$errors\"}"
    exit 2
fi

echo "All validations passed"
exit 0
```

## Expected Results

After implementing PostToolUse hooks for validation:

1. **Automatic execution**: Hooks run immediately after Claude completes Write, Edit, or other tool operations
2. **Real-time feedback**: Claude receives validation results and can address issues in the same session
3. **Quality enforcement**: Code automatically meets standards for formatting, type checking, and testing
4. **Transparent process**: You see validation messages in the Claude Code transcript
5. **Iterative improvement**: Claude can fix validation errors based on hook feedback

**Example transcript output:**
```
Claude: I've updated the user authentication module.
[PostToolUse hook executed]
Hook: Type checking failed: Property 'username' does not exist on type 'User'
Claude: I see the type error. Let me fix that by adding the username property to the User interface.
```

## Troubleshooting

### Common Issue 1
**Problem**: Hook doesn't execute after tool use
**Solution**:
- Verify matcher syntax is correct and case-sensitive (e.g., "Write" not "write")
- Ensure script has executable permissions: `chmod +x .claude/hooks/script.sh`
- Check `.claude/settings.json` is valid JSON (use `jq . .claude/settings.json`)
- Verify the hook script path is correct relative to `$CLAUDE_PROJECT_DIR`

### Common Issue 2
**Problem**: Environment variables are empty or undefined
**Solution**:
- Known issue: Environment variables may not always populate correctly
- Use stdin JSON input as the primary data source: `input=$(cat)`
- Parse JSON with `jq`: `tool=$(echo "$input" | jq -r '.tool')`
- Test hooks locally by echoing sample JSON: `echo '{"tool":"Write"}' | ./.claude/hooks/script.sh`

### Common Issue 3
**Problem**: Hook blocks Claude execution unexpectedly
**Solution**:
- Known bug: Exit code 1 may block despite documentation stating it's non-blocking
- Use exit code 0 for success and exit code 2 for intentional blocking with feedback
- Avoid exit codes other than 0 and 2 for predictable behavior
- Return JSON with `"decision": "block"` for controlled feedback

### Common Issue 4
**Problem**: Validation runs but Claude doesn't respond to feedback
**Solution**:
- Use JSON output format: `{"decision": "block", "reason": "error message"}`
- Ensure reason field contains actionable information
- Check exit code is 0 or 2 (other codes may not feed back to Claude)
- Verify stdout contains the JSON (not stderr)

### Common Issue 5
**Problem**: Hook times out or runs too slowly
**Solution**:
- Keep validation fast - PostToolUse should complete in seconds
- Use `--quick` or `--fast` flags for linters/type checkers
- For slow operations (full test suites), use Stop hooks instead
- Consider caching validation results for unchanged files

## Additional Tips

- **Start simple**: Begin with a basic validation hook and add complexity gradually
- **Test locally**: Run hook scripts manually with sample JSON before deploying
- **Use specific matchers**: Target only relevant file types to avoid unnecessary executions
- **Combine with Stop hooks**: Use PostToolUse for quick validations, Stop hooks for comprehensive end-of-turn checks
- **Provide clear feedback**: Make error messages actionable so Claude can fix issues
- **Handle missing tools gracefully**: Check if commands exist with `command -v` before using them
- **Log for debugging**: Temporarily add `echo` statements to debug hook execution
- **Version control hooks**: Commit `.claude/` directory to share hooks with your team
- **Web vs CLI awareness**: Use `$CLAUDE_CODE_REMOTE` to adjust behavior for web environment
- **Incremental validation**: Validate only changed files using `$CLAUDE_FILE_PATHS`

## Related Articles
- KB-038: Access sandboxed environment settings
- KB-061: How to create a SessionStart hook for repository initialization
- KB-062: How to configure hooks to auto-format code after edits
- KB-063: How to set up hooks that require approval for bash commands
- KB-065: How to use Stop hooks for cleanup operations
- KB-066: How to debug hook failures and troubleshoot issues

## Sources
- GitHub: disler/claude-code-hooks-mastery - https://github.com/disler/claude-code-hooks-mastery (Accessed: November 16, 2025)
- Claude Code Hooks reference - https://docs.claude.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
- GitButler Blog: Automate Your AI Workflows with Claude Code Hooks - https://blog.gitbutler.com/automate-your-ai-workflows-with-claude-code-hooks (Accessed: November 16, 2025)
- Steve Kinney: Claude Code Hook Control Flow - https://stevekinney.com/courses/ai-development/claude-code-hook-control-flow (Accessed: November 16, 2025)
- GitHub Issue #4809: PostToolUse Hook Exit Code Behavior - https://github.com/anthropics/claude-code/issues/4809 (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code PostToolUse hooks validation best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
