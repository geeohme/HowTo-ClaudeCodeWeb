# How to configure hooks to auto-format code after edits

**Article ID**: KB-062
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview

PostToolUse hooks in Claude Code enable automatic code formatting every time Claude edits, writes, or modifies files in your project. By configuring these hooks, you can ensure all AI-generated code instantly matches your project's style guide without manual intervention, keeping your codebase clean and consistent across JavaScript/TypeScript, Python, Go, Kotlin, Markdown, and other languages.

## Prerequisites

- Active Claude Code Web session with a connected repository
- Basic understanding of JSON configuration files
- Familiarity with code formatters (Prettier, Black, Ruff, etc.)
- Knowledge of your project's formatting requirements
- KB-043: How to configure project-specific settings in .claude/settings.json

## Understanding PostToolUse Hooks

### What are PostToolUse Hooks?

PostToolUse hooks are automated triggers that run shell commands immediately after Claude uses specific tools. For auto-formatting, these hooks activate when Claude:

- **Edit**: Modifies existing file content
- **Write**: Creates new files or overwrites existing ones
- **MultiEdit**: Performs multiple edits across files

### How Auto-Formatting Hooks Work

The workflow follows four steps:

1. Claude uses the Edit, Write, or MultiEdit tool to modify a file
2. The PostToolUse hook triggers and sends file information to your formatter script as JSON via stdin
3. The script identifies the file extension and selects the appropriate formatter
4. The formatter runs silently, updating the file in place

### Benefits

- **Consistency**: All code matches your style guide automatically
- **Zero Effort**: No manual formatting required after Claude's changes
- **Team Alignment**: Everyone gets consistently formatted code
- **Standards Enforcement**: Project conventions are maintained automatically
- **Multi-Language Support**: Different formatters for different file types

## Steps

### Step 1: Choose Your Configuration Approach

You have two main options for setting up auto-formatting hooks:

**Option A: Use a Pre-Built Solution** - The `claude-format-hook` project provides a ready-made script supporting multiple languages (recommended for most users)

**Option B: Create Custom Hooks** - Write your own hook configuration for specific formatters or custom requirements

This guide covers both approaches.

### Step 2: Option A - Set Up claude-format-hook (Recommended)

The `claude-format-hook` project provides a battle-tested solution supporting five languages.

**Step 2a: Get the Formatting Script**

Clone the repository or download the script directly:

```bash
# Option 1: Clone the repository
cd ~/.claude/hooks
git clone https://github.com/ryanlewis/claude-format-hook.git
cd claude-format-hook

# Option 2: Download script directly
mkdir -p ~/.claude/hooks
curl -o ~/.claude/hooks/format-code.sh https://raw.githubusercontent.com/ryanlewis/claude-format-hook/main/format-code.sh
chmod +x ~/.claude/hooks/format-code.sh
```

**Step 2b: Install Required Formatters**

Install the formatters you need for your languages:

```bash
# JavaScript/TypeScript - Biome (primary) or Prettier (fallback)
npm install -g @biomejs/biome
# Or: npm install -g prettier

# Python - Ruff formatter
pip install ruff
# Or using uv: curl -LsSf https://astral.sh/uv/install.sh | sh

# Go - goimports
go install golang.org/x/tools/cmd/goimports@latest

# Kotlin - ktlint
brew install ktlint
# Or: curl -sSLO https://github.com/pinterest/ktlint/releases/latest/download/ktlint && chmod +x ktlint

# Markdown - Prettier
npm install -g prettier
```

**Step 2c: Configure the Hook in settings.json**

Add the PostToolUse hook configuration to `~/.claude/settings.json` (for all projects) or `.claude/settings.json` (for specific project):

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/format-code.sh"
          }
        ]
      }
    ]
  }
}
```

**Note**: If you cloned the repository, adjust the path accordingly:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/claude-format-hook/format-code.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 3: Option B - Create Custom Formatting Hooks

For custom formatters or specific requirements, you can create your own hook configuration.

**Step 3a: Create a Custom Formatting Script**

Create `.claude/hooks/auto-format.sh` in your project:

```bash
#!/bin/bash

# Read tool use information from stdin
TOOL_DATA=$(cat)

# Extract file path from the JSON data
FILE_PATH=$(echo "$TOOL_DATA" | jq -r '.tool_input.file_path // .file_path // empty')

# Exit if no file path found
if [ -z "$FILE_PATH" ]; then
  exit 0
fi

# Get file extension
EXT="${FILE_PATH##*.}"

# Format based on file type
case "$EXT" in
  ts|tsx|js|jsx)
    if command -v prettier &> /dev/null; then
      prettier --write "$FILE_PATH" 2>/dev/null
    fi
    ;;
  py)
    if command -v black &> /dev/null; then
      black "$FILE_PATH" 2>/dev/null
    elif command -v ruff &> /dev/null; then
      ruff format "$FILE_PATH" 2>/dev/null
    fi
    ;;
  go)
    if command -v goimports &> /dev/null; then
      goimports -w "$FILE_PATH" 2>/dev/null
    fi
    if command -v gofmt &> /dev/null; then
      gofmt -w "$FILE_PATH" 2>/dev/null
    fi
    ;;
  rs)
    if command -v rustfmt &> /dev/null; then
      rustfmt "$FILE_PATH" 2>/dev/null
    fi
    ;;
  java)
    if command -v google-java-format &> /dev/null; then
      google-java-format -i "$FILE_PATH" 2>/dev/null
    fi
    ;;
  md|markdown)
    if command -v prettier &> /dev/null; then
      prettier --write "$FILE_PATH" 2>/dev/null
    fi
    ;;
esac

# Exit successfully regardless of formatter availability
exit 0
```

**Step 3b: Make the Script Executable**

```bash
chmod +x .claude/hooks/auto-format.sh
```

**Step 3c: Configure the Hook**

Add to `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/auto-format.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 4: Configure Language-Specific Hooks

For more granular control, you can create separate hooks for different file types.

**Step 4a: TypeScript/JavaScript Only**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path // .file_path' | { read file_path; if echo \"$file_path\" | grep -qE '\\.(ts|tsx|js|jsx)$'; then npx prettier --write \"$file_path\" 2>/dev/null; fi; }"
          }
        ]
      }
    ]
  }
}
```

**Step 4b: Python Only with Black**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path // .file_path' | { read file_path; if echo \"$file_path\" | grep -q '\\.py$'; then black \"$file_path\" 2>/dev/null; fi; }"
          }
        ]
      }
    ]
  }
}
```

**Step 4c: Multiple Language Hooks**

You can stack multiple hooks for different languages:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/format-typescript.sh"
          },
          {
            "type": "command",
            "command": "~/.claude/hooks/format-python.sh"
          },
          {
            "type": "command",
            "command": "~/.claude/hooks/format-go.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 5: Configure Project-Specific Formatter Options

Create formatter configuration files to define project-specific styling rules.

**Step 5a: Prettier Configuration**

Create `.prettierrc.json` in your project root:

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false
}
```

**Step 5b: Black Configuration**

Create `pyproject.toml`:

```toml
[tool.black]
line-length = 100
target-version = ['py38', 'py39', 'py310', 'py311']
include = '\.pyi?$'
```

**Step 5c: Biome Configuration**

Create `biome.json`:

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100
  },
  "linter": {
    "enabled": true
  }
}
```

### Step 6: Test Your Hook Configuration

**Step 6a: Start a New Session**

Create a new Claude Code session or restart your current one to load the hook configuration.

**Step 6b: Ask Claude to Create a Test File**

Provide a test request to Claude:

```
"Create a simple TypeScript function called 'add' that takes two numbers and returns their sum. Don't worry about formatting - just write it."
```

**Step 6c: Verify Automatic Formatting**

Check the created file to confirm it was automatically formatted according to your prettier/biome configuration. The code should be properly indented, spaced, and styled even if Claude wrote it differently.

**Step 6d: Test Edit Operations**

Ask Claude to modify the file:

```
"Add a multiply function to the same file"
```

Verify that both the new code and the entire file remain properly formatted.

### Step 7: Configure Hook Behavior (Advanced)

Hooks can return structured JSON to control Claude's behavior after formatting.

**Step 7a: Create Advanced Hook Script**

Create `.claude/hooks/format-with-feedback.sh`:

```bash
#!/bin/bash

TOOL_DATA=$(cat)
FILE_PATH=$(echo "$TOOL_DATA" | jq -r '.tool_input.file_path // .file_path // empty')

if [ -z "$FILE_PATH" ]; then
  exit 0
fi

EXT="${FILE_PATH##*.}"

# Attempt formatting
FORMATTED=false
case "$EXT" in
  ts|tsx|js|jsx)
    if command -v prettier &> /dev/null; then
      prettier --write "$FILE_PATH" 2>/dev/null && FORMATTED=true
    fi
    ;;
  py)
    if command -v black &> /dev/null; then
      black "$FILE_PATH" 2>/dev/null && FORMATTED=true
    fi
    ;;
esac

# Return status with message
if [ "$FORMATTED" = true ]; then
  echo "{\"continue\": true, \"systemMessage\": \"Code automatically formatted with project style guide\"}"
else
  echo "{\"continue\": true}"
fi
```

**Step 7b: Understanding Hook Response Fields**

Your hook script can return JSON with these fields:

- `continue` (boolean): Whether Claude should continue normally (default: true)
- `stopReason` (string): Message explaining why execution stopped (if continue: false)
- `suppressOutput` (boolean): Hide the hook's stdout/stderr from Claude
- `systemMessage` (string): Message shown to Claude about the hook result

### Step 8: Configure Hooks for Claude Code Web vs CLI

Detect whether you're in the web environment and adjust behavior accordingly.

**Step 8a: Environment Detection Script**

```bash
#!/bin/bash

TOOL_DATA=$(cat)
FILE_PATH=$(echo "$TOOL_DATA" | jq -r '.tool_input.file_path // .file_path // empty')

# Check if running in Claude Code Web
if [ "$CLAUDE_CODE_REMOTE" = "true" ]; then
  # Web environment - use cloud-friendly formatters
  # Prefer tools that don't require local installation
  if echo "$FILE_PATH" | grep -qE '\\.(ts|tsx|js|jsx)$'; then
    npx -y prettier@latest --write "$FILE_PATH" 2>/dev/null
  fi
else
  # Local CLI environment - use locally installed formatters
  if echo "$FILE_PATH" | grep -qE '\\.(ts|tsx|js|jsx)$'; then
    prettier --write "$FILE_PATH" 2>/dev/null
  fi
fi

exit 0
```

**Step 8b: Configure Conditional Hooks**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/format-adaptive.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 9: Set Up Team-Wide Hook Configuration

Share your formatting hooks with your team through version control.

**Step 9a: Create Project Hook Directory**

```bash
mkdir -p .claude/hooks
```

**Step 9b: Add Team Hooks**

Place your formatting scripts in `.claude/hooks/`:

```bash
.claude/
├── hooks/
│   ├── format-code.sh
│   ├── format-typescript.sh
│   └── format-python.sh
└── settings.json
```

**Step 9c: Configure in Project Settings**

In `.claude/settings.json` (committed to version control):

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/format-code.sh"
          }
        ]
      }
    ]
  },
  "environmentVariables": {
    "FORMATTER_CONFIG": ".prettierrc.json"
  }
}
```

**Step 9d: Document Team Configuration**

Create `.claude/README.md`:

```markdown
# Claude Code Configuration

## Auto-Formatting Hooks

This project uses PostToolUse hooks to automatically format code after Claude edits files.

### Required Formatters

Install these tools to enable auto-formatting:

- **JavaScript/TypeScript**: `npm install -g prettier`
- **Python**: `pip install black`
- **Go**: `go install golang.org/x/tools/cmd/goimports@latest`

### Configuration Files

- `.prettierrc.json` - Prettier configuration
- `pyproject.toml` - Black configuration

### Testing Hooks

After installing formatters, restart your Claude Code session to activate hooks.
```

**Step 9e: Commit Configuration**

```bash
git add .claude/
git commit -m "Add Claude Code auto-formatting hooks"
git push
```

## Expected Results

After configuring auto-formatting hooks successfully, you should observe:

### Immediate Formatting

When Claude creates or modifies files, they are automatically formatted within 1-2 seconds of the edit. You won't see unformatted code in your repository.

### Consistent Style

All Claude-generated code matches your project's style guide exactly:
- Correct indentation (spaces vs tabs, width)
- Proper line length limits
- Consistent quote style (single vs double)
- Trailing commas, semicolons as configured
- Proper spacing around operators and keywords

### Silent Operation

Hooks run silently in the background. You'll only notice the results - properly formatted code - without any prompts or interruptions to Claude's workflow.

### Multi-File Consistency

When Claude uses MultiEdit to modify multiple files, each file is formatted independently according to its file type.

### Example Output

When you ask Claude to create a function, you'll receive properly formatted code:

**Before Hook (unformatted)**:
```typescript
function calculateTotal(items:any[]):number{
const sum=items.reduce((acc,item)=>acc+item.price,0)
return sum}
```

**After Hook (formatted with Prettier)**:
```typescript
function calculateTotal(items: any[]): number {
  const sum = items.reduce((acc, item) => acc + item.price, 0);
  return sum;
}
```

## Troubleshooting

### Common Issue 1
**Problem**: Hook doesn't run - code remains unformatted
**Solution**:
- Verify hook script has execute permissions: `chmod +x ~/.claude/hooks/format-code.sh`
- Check settings.json syntax is valid: `python3 -m json.tool ~/.claude/settings.json`
- Ensure the hook path in settings.json is correct and absolute
- Test the hook script manually: `echo '{"tool_input":{"file_path":"test.ts"}}' | ~/.claude/hooks/format-code.sh`
- Restart Claude Code session to reload configuration
- Check that the matcher includes the tool Claude used (Edit, Write, or MultiEdit)

### Common Issue 2
**Problem**: Formatter not found or command fails
**Solution**:
- Verify formatter is installed: `which prettier` or `which black`
- Install missing formatters using package managers (npm, pip, brew, etc.)
- Check formatter is in PATH: `echo $PATH`
- For web environment, use `npx` to run formatters without installation: `npx prettier`
- Add formatter location to PATH in sessionStart hook
- Use absolute paths to formatters in hook script: `/usr/local/bin/prettier`

### Common Issue 3
**Problem**: Hook runs but formatting doesn't match expectations
**Solution**:
- Check for formatter configuration files (.prettierrc, biome.json, pyproject.toml)
- Verify configuration file is in project root or user home directory
- Test formatter manually: `prettier --write yourfile.ts`
- Review formatter output for errors: remove `2>/dev/null` from hook script temporarily
- Ensure formatter version supports your configuration options
- Check for conflicting formatter configurations

### Common Issue 4
**Problem**: Hook works locally but not in Claude Code Web
**Solution**:
- Verify formatters are accessible in web environment
- Use `npx` with `-y` flag to auto-install formatters: `npx -y prettier@latest`
- Check CLAUDE_CODE_REMOTE environment variable to detect web environment
- Install formatters in sessionStart hook for web sessions
- Consider using cloud-friendly formatters (Biome over Prettier for faster execution)
- Ensure script doesn't depend on local-only tools or paths

### Common Issue 5
**Problem**: Hook runs on every file, including ones that shouldn't be formatted
**Solution**:
- Add file extension checking in your hook script
- Create ignore files (.prettierignore, .blackignore)
- Use conditional logic based on file paths
- Example: `if echo "$FILE_PATH" | grep -qE '\\.(ts|tsx)$'; then`
- Exclude generated files, vendor directories, and build outputs
- Add explicit checks for `.min.js`, `dist/`, `node_modules/`, etc.

### Common Issue 6
**Problem**: Hook causes errors or prevents Claude from continuing
**Solution**:
- Ensure hook script always exits with code 0: `exit 0` at the end
- Redirect error output: `2>/dev/null` or `2>&1`
- Wrap formatter commands in conditionals that check for command existence
- Use `command -v formatter &> /dev/null` before running formatter
- Return `{"continue": true}` JSON to ensure Claude continues
- Test hook doesn't have syntax errors: `bash -n your-hook.sh`

## Additional Tips

### Performance Optimization

**Use Fast Formatters**: Biome is significantly faster than Prettier for JavaScript/TypeScript. Ruff is faster than Black for Python.

**Conditional Execution**: Only run formatters on files that actually changed:
```bash
# Check if file was modified in last 5 seconds
if [ $(find "$FILE_PATH" -mmin -0.1 | wc -l) -gt 0 ]; then
  format_file "$FILE_PATH"
fi
```

**Parallel Formatting**: For MultiEdit operations affecting many files, consider parallel formatting:
```bash
xargs -P 4 -I {} prettier --write {}
```

### Graceful Degradation

Design hooks to fail silently when formatters aren't available:

```bash
# Good: Falls back gracefully
if command -v prettier &> /dev/null; then
  prettier --write "$FILE_PATH" 2>/dev/null || true
fi

# Bad: Fails loudly if prettier missing
prettier --write "$FILE_PATH"
```

### Security Considerations

**Validate Input**: Always validate file paths from tool data:
```bash
# Check file path is within project
if [[ "$FILE_PATH" != "$CLAUDE_PROJECT_DIR"* ]]; then
  exit 0
fi
```

**Avoid Injection**: Use proper quoting and validation:
```bash
# Good: Properly quoted
prettier --write "$FILE_PATH"

# Bad: Injection risk
eval "prettier --write $FILE_PATH"
```

### Debugging Hooks

**Enable Debug Output**: Temporarily log hook activity:
```bash
echo "Formatting: $FILE_PATH" >> /tmp/claude-format.log
```

**Test Manually**: Run hook outside Claude Code:
```bash
echo '{"tool_input":{"file_path":"src/test.ts"}}' | .claude/hooks/format-code.sh
```

**Check Hook Execution**: Add systemMessage to verify hooks run:
```bash
echo "{\"continue\": true, \"systemMessage\": \"Formatted $FILE_PATH\"}"
```

### Integration with Linters

Combine formatting with linting for comprehensive code quality:

```bash
# Format then lint
prettier --write "$FILE_PATH" 2>/dev/null
eslint --fix "$FILE_PATH" 2>/dev/null
```

Or create separate hooks for different concerns.

### Monorepo Support

For monorepos with different formatting rules per package:

```bash
# Detect package and use appropriate config
if [[ "$FILE_PATH" == */packages/frontend/* ]]; then
  prettier --config packages/frontend/.prettierrc --write "$FILE_PATH"
elif [[ "$FILE_PATH" == */packages/backend/* ]]; then
  prettier --config packages/backend/.prettierrc --write "$FILE_PATH"
fi
```

### Version Control Integration

Never format generated files or external dependencies:

```bash
# Skip if in gitignore
if git check-ignore -q "$FILE_PATH" 2>/dev/null; then
  exit 0
fi
```

## Related Articles

- KB-043: How to configure project-specific settings in .claude/settings.json
- KB-042: How to set environment variables for your session
- KB-061: How to create a SessionStart hook for repository initialization
- KB-063: How to set up hooks that require approval for bash commands
- KB-064: How to create PostToolUse hooks for validation
- KB-066: How to debug hook failures and troubleshoot issues
- KB-067: How to share hooks across your development team

## Sources

- Claude Code Hooks Reference - Anthropic - https://docs.anthropic.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
- Get started with Claude Code hooks - Claude Docs - https://docs.claude.com/en/docs/claude-code/hooks-guide (Accessed: November 16, 2025)
- GitHub: ryanlewis/claude-format-hook - https://github.com/ryanlewis/claude-format-hook (Accessed: November 16, 2025)
- A complete guide to hooks in Claude Code - eesel AI - https://www.eesel.ai/blog/hooks-in-claude-code (Accessed: November 16, 2025)
- Claude Code Developer Cheatsheet - https://awesomeclaude.ai/code-cheatsheet (Accessed: November 16, 2025)
- Claude Code Hooks - GitButler Docs - https://docs.gitbutler.com/features/ai-integration/claude-code-hooks (Accessed: November 16, 2025)
- Google Search: "PostToolUse hooks Claude Code settings.json configuration 2025" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
