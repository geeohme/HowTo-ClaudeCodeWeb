# How to debug and test custom slash commands

**Article ID**: KB-074
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15 minutes

## Overview
Testing and debugging custom slash commands is essential for ensuring your automation workflows function correctly and reliably. This guide covers systematic testing strategies, debugging techniques using Claude Code's built-in flags, and troubleshooting methods to identify and resolve issues with your custom slash commands before deploying them to your team.

## Prerequisites
- Working knowledge of custom slash commands in Claude Code Web
- Familiarity with creating Markdown files in `.claude/commands/` directory
- Basic command line experience
- Understanding of `$ARGUMENTS` and positional parameters
- Recommended: KB-068: How to create your first custom slash command
- Recommended: KB-069: How to write slash commands as Markdown files
- Recommended: KB-070: How to use the $ARGUMENTS keyword in slash commands

## Steps

### Step 1: Verify Command File Structure and Location

Before testing command behavior, ensure your command files are properly structured and located.

**Check file location:**
```bash
# For project-scoped commands
ls -la .claude/commands/

# Verify file has .md extension
find .claude/commands/ -name "*.md"
```

**Validate file naming:**
- Use lowercase with hyphens (e.g., `analyze-code.md`)
- Avoid spaces and special characters
- File name without `.md` becomes the command name

**Example verification script:**
```bash
# List all custom commands with their paths
find .claude/commands/ -type f -name "*.md" | while read file; do
  echo "Command: /$(basename "$file" .md)"
  echo "Path: $file"
  echo "---"
done
```

### Step 2: Test Basic Command Invocation

Start with the simplest test—verify the command appears and executes.

**Test checklist:**
1. **Command visibility**: Type `/` in Claude Code Web and verify your command appears in the autocomplete menu
2. **Command execution**: Invoke the command and observe if Claude receives the prompt
3. **Response verification**: Confirm Claude responds with actions relevant to your prompt

**Example test:**
Create a simple test command (`.claude/commands/test-basic.md`):
```markdown
This is a test command. Please respond with: "Test command received successfully."
```

Invoke it:
```
/test-basic
```

**Expected result**: Claude should respond with the exact message, confirming the command was processed correctly.

### Step 3: Test Argument Substitution

Systematically test how your command handles different argument patterns.

**Create a debug command** (`.claude/commands/debug-args.md`):
```markdown
# Argument Debug Output

Please display the following information:

**Full arguments received**: $ARGUMENTS

**Positional arguments**:
- First argument ($1): $1
- Second argument ($2): $2
- Third argument ($3): $3
- Fourth argument ($4): $4

**Analysis**: Count the number of arguments and list each one separately.
```

**Test scenarios:**

1. **No arguments:**
   ```
   /debug-args
   ```
   Expected: All placeholders show as empty or literal `$ARGUMENTS`

2. **Single argument:**
   ```
   /debug-args hello
   ```
   Expected: `$1` and `$ARGUMENTS` = "hello", others empty

3. **Multiple arguments:**
   ```
   /debug-args first second third
   ```
   Expected: `$1` = "first", `$2` = "second", `$3` = "third"

4. **Arguments with special characters:**
   ```
   /debug-args src/components/Button.tsx
   ```
   Expected: Path preserved correctly

5. **Quoted arguments:**
   ```
   /debug-args "multi word argument" second
   ```
   Expected: `$1` = "multi word argument", `$2` = "second"

### Step 4: Validate Command Logic Flow

Test that your command's workflow executes in the correct order with expected results.

**Create a workflow test command** (`.claude/commands/test-workflow.md`):
```markdown
Test workflow for: $ARGUMENTS

Execute these steps in order and confirm completion after each:

1. **Step 1**: Search for files matching: $ARGUMENTS
   - Confirm files found

2. **Step 2**: Read the first file discovered
   - Confirm file contents displayed

3. **Step 3**: Count total lines in the file
   - Provide exact line count

4. **Step 4**: Report completion status
   - Summarize what was accomplished

Please explicitly confirm after each step is complete.
```

**Test execution:**
```
/test-workflow *.md
```

**Verify:**
- Each step executes sequentially
- No steps are skipped
- Step confirmations appear in output
- Final summary is accurate

### Step 5: Use Debug Flags for Command Inspection

Claude Code provides debugging flags to inspect command behavior at a deeper level.

**Available debug flags:**

1. **`--debug` flag** (Claude Code CLI):
   ```bash
   claude --debug
   ```
   - Shows which custom slash commands the SlashCommand tool can invoke
   - Displays command resolution and selection process
   - Useful for understanding why a command might not be appearing

2. **`--verbose` flag**:
   ```bash
   claude --verbose
   ```
   - Provides detailed output about Claude invocations
   - Shows command processing steps
   - Displays tool selection and execution

3. **`--mcp-debug` flag**:
   ```bash
   claude --mcp-debug
   ```
   - Specifically for MCP (Model Context Protocol) server errors
   - Helpful when commands interact with external tools or servers

**Note**: These flags are primarily for the Claude Code CLI. In Claude Code Web, debugging is typically done through observation of command behavior and responses.

### Step 6: Add Frontmatter Metadata for Better Debugging

Enhance your commands with frontmatter to control behavior and improve debuggability.

**Example with frontmatter** (`.claude/commands/advanced-test.md`):
```markdown
---
description: "Test command with comprehensive debugging output"
argument-hint: "<target-file> <action>"
allowed-tools: ["Read", "Grep", "Bash"]
model: "claude-sonnet-4-5-20250929"
---

# Advanced Test Command

Target: $1
Action: $2
Full context: $ARGUMENTS

Execute $2 on $1 with the following constraints:
- Only use tools specified in frontmatter
- Provide verbose output for each step
- Report any errors immediately
```

**Frontmatter fields:**
- **`description`**: Required for SlashCommand tool visibility (without this, the command won't be invokable programmatically)
- **`argument-hint`**: Shows users the expected argument format
- **`allowed-tools`**: Restricts which tools Claude can use (useful for testing specific workflows)
- **`model`**: Specifies which Claude model to use for this command

**Test with frontmatter:**
```
/advanced-test app.js analyze
```

Verify that:
- Claude only uses the specified tools
- Argument hint appears when typing the command
- Description is visible in command listings

### Step 7: Create Test Suites for Complex Commands

For sophisticated commands, create dedicated test suites that verify all edge cases.

**Test suite structure** (`.claude/commands/test-suite.md`):
```markdown
---
description: "Comprehensive test suite for custom commands"
---

# Command Test Suite

Run comprehensive tests on the following commands:

## Test 1: Argument Handling
- Command: /debug-args one two three
- Expected: All three arguments correctly parsed

## Test 2: File Operations
- Command: /analyze src/
- Expected: Directory analyzed without errors

## Test 3: Error Conditions
- Command: /analyze non-existent-file.txt
- Expected: Graceful error handling message

## Test 4: Complex Workflows
- Command: /pr-review 123
- Expected: PR fetched, analyzed, and review generated

Please execute each test and report:
- ✓ Pass
- ✗ Fail (with explanation)

Provide a summary of all test results.
```

**Usage:**
```
/test-suite
```

This meta-command tests your other commands systematically.

### Step 8: Test Commands in Isolation Before Team Deployment

Before committing commands to version control, test them in a personal scope first.

**Testing workflow:**

1. **Create in personal scope** (`~/.claude/commands/`):
   ```bash
   # Copy command to personal directory for testing
   cp .claude/commands/new-feature.md ~/.claude/commands/test-new-feature.md
   ```

2. **Test thoroughly** with various scenarios:
   ```
   /test-new-feature scenario1
   /test-new-feature edge-case
   /test-new-feature error-condition
   ```

3. **Refine based on results**: Edit the command file and re-test

4. **Move to project scope** when stable:
   ```bash
   mv ~/.claude/commands/test-new-feature.md .claude/commands/new-feature.md
   ```

5. **Final verification** in project context:
   ```
   /project:new-feature production-test
   ```

6. **Commit to version control**:
   ```bash
   git add .claude/commands/new-feature.md
   git commit -m "Add new-feature slash command (tested)"
   git push
   ```

### Step 9: Validate Command Output and Side Effects

Test not just that commands execute, but that they produce correct results.

**Output validation checklist:**

1. **File modifications**:
   ```bash
   # Before running command
   git status

   # Run command that should modify files
   # (in Claude Code Web)
   /refactor UserService.ts

   # After command completes
   git diff
   ```
   Verify only intended files were modified

2. **Command execution**:
   Create a command that runs specific tools:
   ```markdown
   Please run: npm test

   Report:
   - Exit code
   - Number of tests passed
   - Number of tests failed
   ```

   Verify the command actually ran and results are accurate

3. **External API calls**:
   ```markdown
   Use gh to fetch issue #$ARGUMENTS

   Verify the issue exists and display its current status
   ```

   Check that API interactions work correctly

4. **Multi-step consistency**:
   Ensure each step in a workflow produces expected intermediate results

### Step 10: Monitor Command Performance and Token Usage

Test that commands complete within reasonable time and token limits.

**Performance testing:**

1. **Create a timed test** (`.claude/commands/performance-test.md`):
   ```markdown
   Note the current time, then:

   1. Search the entire codebase for: $ARGUMENTS
   2. Analyze all matching files
   3. Generate a summary report

   Report total execution time and number of files processed.
   ```

2. **Run with varying complexity**:
   ```
   /performance-test TODO
   /performance-test import
   /performance-test function
   ```

3. **Observe**:
   - Time to completion
   - Whether commands timeout
   - If large result sets cause issues
   - Token usage (if visible in Claude Code Web interface)

**Optimization tips if performance issues occur:**
- Break large commands into smaller, focused ones
- Use more specific search patterns
- Limit scope with explicit file paths
- Add conditional logic to skip unnecessary steps

## Expected Results

After completing these debugging and testing steps, you should have:

1. **Verified command visibility**: All commands appear in `/` autocomplete menu
2. **Validated argument handling**: `$ARGUMENTS` and positional parameters work correctly across all test scenarios
3. **Confirmed workflow execution**: Multi-step commands execute in the correct order
4. **Tested edge cases**: Commands handle missing arguments, special characters, and error conditions gracefully
5. **Documented behavior**: Clear understanding of how each command behaves in different contexts
6. **Team-ready commands**: Thoroughly tested commands ready for version control and team deployment
7. **Performance baseline**: Knowledge of execution time and resource requirements for each command

**Success indicators:**
- Zero unexpected behaviors during testing
- All argument patterns work as documented
- Commands produce consistent results across multiple invocations
- Error conditions are handled gracefully with clear messages
- Team members can use commands without additional guidance

## Troubleshooting

### Issue 1: Command Appears But Doesn't Execute Correctly

**Problem**: Command shows in autocomplete but Claude doesn't follow the prompt properly.
**Solution**:
- Review prompt clarity—make instructions more explicit and structured
- Use numbered steps instead of prose for complex workflows
- Add specific examples within the command (e.g., "Use `git status` to check...")
- Test the prompt manually by copying it directly into chat before converting to slash command
- Verify Markdown formatting doesn't introduce parsing issues
- Check for special characters that might break interpretation

**Debug approach:**
```markdown
Debug mode: Please repeat back the exact prompt you received, then execute it step by step with confirmation after each action.
```

### Issue 2: Arguments Not Substituting as Expected

**Problem**: `$ARGUMENTS` or `$1`, `$2` show as literal text instead of values.
**Solution**:
- Verify exact spelling: `$ARGUMENTS` (all caps, with `$`)
- Ensure file is saved as plain text Markdown (not rich text)
- Check file extension is exactly `.md`
- Restart Claude Code session to reload command definitions
- Test with debug command from Step 3 to isolate the issue
- Verify you're actually passing arguments: `/command arg1 arg2`

**Verification test:**
```
/debug-args test1 test2 test3
```
If debug command works but your command doesn't, the issue is in your command file.

### Issue 3: Command Not Appearing in /help or Autocomplete

**Problem**: Newly created command doesn't show in the command list.
**Solution**:
- Verify file is in correct directory: `.claude/commands/` (with leading dot)
- Check filename has `.md` extension
- Ensure filename uses valid characters (lowercase, hyphens, no spaces)
- For SlashCommand tool visibility, add frontmatter with `description` field:
  ```markdown
  ---
  description: "Brief description of what this command does"
  ---
  ```
- Refresh Claude Code Web session or reconnect repository
- Check that `.claude/` directory is at repository root, not in subdirectory
- Verify file has read permissions: `ls -la .claude/commands/`

### Issue 4: Command Works Locally But Not for Team Members

**Problem**: Command executes correctly for you but team members report issues.
**Solution**:
- Verify `.claude/commands/` is committed to git:
  ```bash
  git ls-files .claude/commands/
  ```
- Check that `.claude/` is not in `.gitignore`
- Ensure team members have pulled latest changes:
  ```bash
  git pull origin main
  ```
- Verify team members are in the correct repository/branch
- Test in a fresh clone to simulate team member experience:
  ```bash
  git clone <repo-url> /tmp/test-repo
  cd /tmp/test-repo
  # Open in Claude Code Web and test commands
  ```
- Check for environment-specific assumptions in your command (absolute paths, local tools, etc.)

### Issue 5: Command Produces Inconsistent Results

**Problem**: Same command with same arguments produces different results on different runs.
**Solution**:
- Add explicit constraints to reduce variability:
  ```markdown
  Always follow these steps in exact order:
  1. [Specific action]
  2. [Specific action]

  Do not deviate from this sequence.
  ```
- Specify exact tools to use:
  ```markdown
  ---
  allowed-tools: ["Read", "Grep", "Bash"]
  ---
  ```
- Pin to a specific model version:
  ```markdown
  ---
  model: "claude-sonnet-4-5-20250929"
  ---
  ```
- Make context requirements explicit:
  ```markdown
  Before starting, verify:
  - Current directory is project root
  - All required files exist
  - No uncommitted changes (run git status)
  ```
- Add verification steps within the command to ensure state is as expected

### Issue 6: Debug Flags Not Working in Claude Code Web

**Problem**: `--debug` or `--verbose` flags don't provide output in web interface.
**Solution**:
- Debug flags are primarily for Claude Code CLI, not the web interface
- In Claude Code Web, use alternative debugging strategies:
  - Create explicit debug commands (as shown in Step 3)
  - Add verbose logging within command prompts:
    ```markdown
    Please execute each step and explicitly report completion:

    Step 1: [Action]
    Report: "Step 1 complete: [details]"

    Step 2: [Action]
    Report: "Step 2 complete: [details]"
    ```
  - Use test commands that echo their inputs and outputs
  - Observe Claude's tool usage in the conversation history
- Consider switching to Claude Code CLI for deep debugging if web interface limits troubleshooting

## Additional Tips

- **Version your commands**: Use git to track changes and roll back problematic versions
  ```bash
  git log .claude/commands/my-command.md
  git diff HEAD~1 .claude/commands/my-command.md
  ```

- **Document expected behavior**: Add comments to command files explaining intended usage
  ```markdown
  <!--
  Usage: /analyze <file-or-directory>
  Example: /analyze src/components/

  Expected behavior:
  - Searches for code quality issues
  - Reports top 5 concerns
  - Suggests specific improvements
  -->
  ```

- **Create command templates**: Use a template for consistent command structure
  ```markdown
  ---
  description: "Brief description"
  argument-hint: "<arg1> <arg2>"
  ---

  # Command: $ARGUMENTS

  ## Validation
  [Verify prerequisites]

  ## Execution
  [Main workflow]

  ## Verification
  [Confirm results]
  ```

- **Test in safe branches**: When testing destructive commands, use a test branch
  ```bash
  git checkout -b test-slash-commands
  # Test commands that modify files
  git checkout main
  git branch -D test-slash-commands
  ```

- **Use smoke tests**: Create a quick smoke test command to verify system state
  ```markdown
  Quick verification:
  - Repository status: git status
  - Current branch: git branch --show-current
  - Recent commits: git log -3 --oneline
  - File count: ls -1 | wc -l
  ```

- **Peer review commands**: Have team members review command logic before merging
  ```bash
  # Create PR for new commands
  git checkout -b add-refactor-command
  git add .claude/commands/refactor-advanced.md
  git commit -m "Add refactor-advanced command for large-scale refactoring"
  git push
  gh pr create --title "Add refactor-advanced slash command" --body "..."
  ```

- **Maintain a command changelog**: Document when commands are added or modified
  ```markdown
  # .claude/commands/CHANGELOG.md

  ## 2025-11-16
  - Added `/analyze-performance` command
  - Updated `/pr-review` to include security checks
  - Fixed argument parsing in `/deploy-staging`
  ```

- **Regular testing schedule**: Periodically re-test all commands to ensure they still work
  ```markdown
  <!-- .claude/commands/regression-test.md -->
  Run all custom slash commands with test data:

  /debug-args test1 test2 test3
  /analyze test-file.js
  /pr-review 999

  Report any commands that fail or behave unexpectedly.
  ```

## Related Articles
- KB-068: How to create your first custom slash command in .claude/commands/
- KB-069: How to write slash commands as Markdown files
- KB-070: How to use the $ARGUMENTS keyword in slash commands
- KB-066: Debug hook failures and troubleshoot issues
- KB-067: Share hooks across development team

## Sources
- Slash commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code: Part 4 - Slash Commands and Custom Commands - https://www.letanure.dev/blog/2025-08-02--claude-code-part-4-slash-commands-custom-commands (Accessed: November 16, 2025)
- Claude Code Tips & Tricks: Custom Slash Commands - https://cloudartisan.com/posts/2025-04-14-claude-code-tips-slash-commands/ (Accessed: November 16, 2025)
- awesome-claude-code GitHub Repository - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)
- Google Search: "Claude Code --debug --verbose flags slash commands 2025"

---
*This article is part of the Claude Code Web knowledge base*
