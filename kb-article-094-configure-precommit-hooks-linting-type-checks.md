# How to configure pre-commit hooks for linting and type checks

**Article ID**: KB-094
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview

Pre-commit hooks ensure code quality by automatically running linting and type checking before commits reach your repository. In Claude Code Web, you can implement two complementary approaches: traditional Git pre-commit hooks that validate code before commits, and Claude Code's Stop/PostToolUse hooks that enforce quality standards immediately after AI edits. This comprehensive setup creates multiple layers of code quality enforcement, preventing defects from entering your codebase.

## Prerequisites

- Active Claude Code Web session with connected repository
- Understanding of JSON configuration files
- Basic knowledge of shell scripting
- Familiarity with linting tools (ESLint, Pylint, etc.) and type checkers (TypeScript, mypy)
- Git repository with write access
- KB-062: How to configure hooks to auto-format code after edits
- KB-064: How to create PostToolUse hooks for validation
- KB-065: How to use Stop hooks for cleanup operations

## Steps

### Step 1: Choose Your Hook Strategy

Understanding the different hook types helps you implement the right quality checks at the right time:

**Traditional Git Pre-Commit Hooks**
- **When**: Before git commit completes
- **Scope**: All staged files
- **Best for**: Final validation before commits, blocking bad code from entering version control
- **Works with**: Local git, CI/CD, any developer workflow

**Claude Code Stop Hooks**
- **When**: After Claude finishes responding (end-of-turn)
- **Scope**: All files modified during Claude's response
- **Best for**: Comprehensive quality gates (tests, linting, type checking, builds)
- **Works with**: Claude Code CLI and Web

**Claude Code PostToolUse Hooks**
- **When**: Immediately after Claude edits/writes a file
- **Scope**: Individual file just modified
- **Best for**: Quick validation, immediate feedback, auto-fixing
- **Works with**: Claude Code CLI and Web

**Recommended Approach**: Use all three layers for maximum code quality:
1. PostToolUse for instant feedback and auto-fixes
2. Stop hooks for comprehensive end-of-turn validation
3. Git pre-commit hooks as final safety net

### Step 2: Set Up Traditional Git Pre-Commit Hooks

Configure standard git hooks that run before any commit (works with or without Claude Code).

**Step 2a: Install pre-commit Framework**

The `pre-commit` framework provides a standardized way to manage pre-commit hooks:

```bash
# Install pre-commit
pip install pre-commit

# Or using homebrew
brew install pre-commit

# Verify installation
pre-commit --version
```

**Step 2b: Create .pre-commit-config.yaml**

Create a configuration file in your repository root:

```yaml
# .pre-commit-config.yaml
repos:
  # Python linting and type checking
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.7.4
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.13.0
    hooks:
      - id: mypy
        additional_dependencies: [types-all]
        args: [--strict, --ignore-missing-imports]

  # JavaScript/TypeScript linting and type checking
  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v9.15.0
    hooks:
      - id: eslint
        files: \.(js|jsx|ts|tsx)$
        types: [file]
        additional_dependencies:
          - eslint@9.15.0
          - '@typescript-eslint/parser@8.14.0'
          - '@typescript-eslint/eslint-plugin@8.14.0'

  # TypeScript type checking
  - repo: local
    hooks:
      - id: tsc
        name: TypeScript type check
        entry: npx tsc --noEmit
        language: system
        types: [ts, tsx]
        pass_filenames: false

  # Go linting and formatting
  - repo: https://github.com/golangci/golangci-lint
    rev: v1.62.2
    hooks:
      - id: golangci-lint

  # General checks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-merge-conflict
      - id: detect-private-key
```

**Step 2c: Install Git Hook Scripts**

```bash
# Install pre-commit hooks into .git/hooks/
pre-commit install

# Verify installation
ls -la .git/hooks/pre-commit

# Test on all files (optional)
pre-commit run --all-files
```

**Step 2d: Configure Language-Specific Settings**

Create configuration files for your linters and type checkers:

**For TypeScript (tsconfig.json):**
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "esModuleInterop": true
  }
}
```

**For ESLint (.eslintrc.json):**
```json
{
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint"],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/explicit-function-return-type": "error"
  }
}
```

**For Python (pyproject.toml):**
```toml
[tool.ruff]
line-length = 100
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP"]
ignore = ["E501"]

[tool.mypy]
python_version = "3.11"
strict = true
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
```

### Step 3: Configure Claude Code PostToolUse Hooks for Instant Validation

Set up immediate linting and type checking after each file edit.

**Step 3a: Create Validation Script**

Create `.claude/hooks/lint-and-typecheck.sh`:

```bash
#!/bin/bash

# Read tool data from stdin
TOOL_DATA=$(cat)

# Extract file path
FILE_PATH=$(echo "$TOOL_DATA" | jq -r '.tool_input.file_path // .file_path // empty')

if [ -z "$FILE_PATH" ]; then
  exit 0
fi

# Get file extension
EXT="${FILE_PATH##*.}"

# Track if any checks failed
FAILED=false
ERROR_MSG=""

# TypeScript/JavaScript linting and type checking
if [[ "$EXT" =~ ^(ts|tsx|js|jsx)$ ]]; then
  # Run ESLint
  if command -v npx &> /dev/null; then
    ESLINT_OUTPUT=$(npx eslint "$FILE_PATH" 2>&1)
    if [ $? -ne 0 ]; then
      FAILED=true
      ERROR_MSG="${ERROR_MSG}\n\nESLint errors:\n${ESLINT_OUTPUT}"
    fi
  fi

  # Run TypeScript type checking for .ts/.tsx files
  if [[ "$EXT" =~ ^(ts|tsx)$ ]]; then
    if command -v npx &> /dev/null; then
      TSC_OUTPUT=$(npx tsc --noEmit "$FILE_PATH" 2>&1)
      if [ $? -ne 0 ]; then
        FAILED=true
        ERROR_MSG="${ERROR_MSG}\n\nTypeScript errors:\n${TSC_OUTPUT}"
      fi
    fi
  fi
fi

# Python linting and type checking
if [ "$EXT" = "py" ]; then
  # Run Ruff linter
  if command -v ruff &> /dev/null; then
    RUFF_OUTPUT=$(ruff check "$FILE_PATH" 2>&1)
    if [ $? -ne 0 ]; then
      FAILED=true
      ERROR_MSG="${ERROR_MSG}\n\nRuff linting errors:\n${RUFF_OUTPUT}"
    fi
  fi

  # Run mypy type checker
  if command -v mypy &> /dev/null; then
    MYPY_OUTPUT=$(mypy "$FILE_PATH" 2>&1)
    if [ $? -ne 0 ]; then
      FAILED=true
      ERROR_MSG="${ERROR_MSG}\n\nMypy type errors:\n${MYPY_OUTPUT}"
    fi
  fi
fi

# Go linting
if [ "$EXT" = "go" ]; then
  if command -v golangci-lint &> /dev/null; then
    GOLINT_OUTPUT=$(golangci-lint run "$FILE_PATH" 2>&1)
    if [ $? -ne 0 ]; then
      FAILED=true
      ERROR_MSG="${ERROR_MSG}\n\nGo linting errors:\n${GOLINT_OUTPUT}"
    fi
  fi
fi

# Return result
if [ "$FAILED" = true ]; then
  echo "{\"continue\": true, \"systemMessage\": \"Quality check failed for $FILE_PATH:${ERROR_MSG}\"}"
  exit 0
else
  echo "{\"continue\": true, \"systemMessage\": \"Quality checks passed for $FILE_PATH\"}"
  exit 0
fi
```

Make it executable:
```bash
chmod +x .claude/hooks/lint-and-typecheck.sh
```

**Step 3b: Configure PostToolUse Hook**

Add to `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/lint-and-typecheck.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 4: Configure Claude Code Stop Hooks for End-of-Turn Validation

Set up comprehensive quality gates that run after Claude completes its response.

**Step 4a: Create Comprehensive Quality Check Script**

Create `.claude/hooks/end-of-turn-quality.sh`:

```bash
#!/bin/bash

# Read hook input
INPUT=$(cat)

PROJECT_DIR="$CLAUDE_PROJECT_DIR"
cd "$PROJECT_DIR" || exit 0

echo "Running end-of-turn quality checks..."

ERRORS=""
EXIT_CODE=0

# Run full TypeScript type checking
if [ -f "tsconfig.json" ]; then
  echo "Checking TypeScript types..."
  TSC_OUTPUT=$(npx tsc --noEmit 2>&1)
  if [ $? -ne 0 ]; then
    ERRORS="${ERRORS}\n\n=== TypeScript Type Errors ===\n${TSC_OUTPUT}"
    EXIT_CODE=1
  fi
fi

# Run ESLint on all TypeScript/JavaScript files
if [ -f ".eslintrc.json" ] || [ -f ".eslintrc.js" ]; then
  echo "Running ESLint..."
  ESLINT_OUTPUT=$(npx eslint "src/**/*.{ts,tsx,js,jsx}" 2>&1)
  if [ $? -ne 0 ]; then
    ERRORS="${ERRORS}\n\n=== ESLint Errors ===\n${ESLINT_OUTPUT}"
    EXIT_CODE=1
  fi
fi

# Run Python type checking with mypy
if command -v mypy &> /dev/null && [ -f "pyproject.toml" ]; then
  echo "Running mypy type checker..."
  MYPY_OUTPUT=$(mypy . 2>&1)
  if [ $? -ne 0 ]; then
    ERRORS="${ERRORS}\n\n=== Mypy Type Errors ===\n${MYPY_OUTPUT}"
    EXIT_CODE=1
  fi
fi

# Run Python linting with Ruff
if command -v ruff &> /dev/null; then
  echo "Running Ruff linter..."
  RUFF_OUTPUT=$(ruff check . 2>&1)
  if [ $? -ne 0 ]; then
    ERRORS="${ERRORS}\n\n=== Ruff Linting Errors ===\n${RUFF_OUTPUT}"
    EXIT_CODE=1
  fi
fi

# Run tests (optional, can be time-consuming)
# Uncomment if you want tests to run at end of turn
# if [ -f "package.json" ]; then
#   echo "Running tests..."
#   TEST_OUTPUT=$(npm test 2>&1)
#   if [ $? -ne 0 ]; then
#     ERRORS="${ERRORS}\n\n=== Test Failures ===\n${TEST_OUTPUT}"
#     EXIT_CODE=1
#   fi
# fi

# Return results
if [ $EXIT_CODE -ne 0 ]; then
  cat <<EOF
{
  "continue": false,
  "stopReason": "Quality checks failed. Please fix the following issues before proceeding:${ERRORS}",
  "systemMessage": "Code quality validation detected issues that need to be addressed."
}
EOF
  exit 2
else
  echo "All quality checks passed!"
  echo '{"continue": true, "systemMessage": "All quality checks passed successfully."}'
  exit 0
fi
```

Make it executable:
```bash
chmod +x .claude/hooks/end-of-turn-quality.sh
```

**Step 4b: Configure Stop Hook**

Update `.claude/settings.json` to include the Stop hook:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/lint-and-typecheck.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/end-of-turn-quality.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 5: Create Language-Specific Pre-Commit Hook Scripts

For teams not using the pre-commit framework, create custom git hooks.

**Step 5a: Create Custom Git Pre-Commit Hook**

Create or edit `.git/hooks/pre-commit`:

```bash
#!/bin/bash

echo "Running pre-commit quality checks..."

# Get list of staged files
STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM)

# Track failures
FAILED=false

# Check TypeScript files
TS_FILES=$(echo "$STAGED_FILES" | grep -E '\.(ts|tsx)$')
if [ -n "$TS_FILES" ]; then
  echo "Type-checking TypeScript files..."
  npx tsc --noEmit
  if [ $? -ne 0 ]; then
    echo "❌ TypeScript type check failed"
    FAILED=true
  fi

  echo "Linting TypeScript files..."
  echo "$TS_FILES" | xargs npx eslint
  if [ $? -ne 0 ]; then
    echo "❌ ESLint check failed"
    FAILED=true
  fi
fi

# Check JavaScript files
JS_FILES=$(echo "$STAGED_FILES" | grep -E '\.(js|jsx)$')
if [ -n "$JS_FILES" ]; then
  echo "Linting JavaScript files..."
  echo "$JS_FILES" | xargs npx eslint
  if [ $? -ne 0 ]; then
    echo "❌ ESLint check failed"
    FAILED=true
  fi
fi

# Check Python files
PY_FILES=$(echo "$STAGED_FILES" | grep '\.py$')
if [ -n "$PY_FILES" ]; then
  echo "Linting Python files..."
  echo "$PY_FILES" | xargs ruff check
  if [ $? -ne 0 ]; then
    echo "❌ Ruff linting failed"
    FAILED=true
  fi

  echo "Type-checking Python files..."
  echo "$PY_FILES" | xargs mypy
  if [ $? -ne 0 ]; then
    echo "❌ Mypy type check failed"
    FAILED=true
  fi
fi

# Check Go files
GO_FILES=$(echo "$STAGED_FILES" | grep '\.go$')
if [ -n "$GO_FILES" ]; then
  echo "Linting Go files..."
  golangci-lint run $GO_FILES
  if [ $? -ne 0 ]; then
    echo "❌ Go linting failed"
    FAILED=true
  fi
fi

# Exit with appropriate code
if [ "$FAILED" = true ]; then
  echo ""
  echo "❌ Pre-commit checks failed. Please fix the issues above."
  echo "   Use 'git commit --no-verify' to bypass (not recommended)"
  exit 1
else
  echo "✅ All pre-commit checks passed"
  exit 0
fi
```

Make it executable:
```bash
chmod +x .git/hooks/pre-commit
```

### Step 6: Configure Auto-Fix Capabilities

Enhance hooks to automatically fix issues when possible.

**Step 6a: Create Auto-Fix PostToolUse Hook**

Create `.claude/hooks/lint-and-fix.sh`:

```bash
#!/bin/bash

TOOL_DATA=$(cat)
FILE_PATH=$(echo "$TOOL_DATA" | jq -r '.tool_input.file_path // .file_path // empty')

if [ -z "$FILE_PATH" ]; then
  exit 0
fi

EXT="${FILE_PATH##*.}"
FIXED=false

# Auto-fix TypeScript/JavaScript
if [[ "$EXT" =~ ^(ts|tsx|js|jsx)$ ]]; then
  if command -v npx &> /dev/null; then
    # Run ESLint with --fix
    npx eslint --fix "$FILE_PATH" 2>/dev/null && FIXED=true
  fi
fi

# Auto-fix Python
if [ "$EXT" = "py" ]; then
  if command -v ruff &> /dev/null; then
    # Run Ruff with auto-fix
    ruff check --fix "$FILE_PATH" 2>/dev/null && FIXED=true
    ruff format "$FILE_PATH" 2>/dev/null && FIXED=true
  fi
fi

# Auto-fix Go
if [ "$EXT" = "go" ]; then
  if command -v gofmt &> /dev/null; then
    gofmt -w "$FILE_PATH" 2>/dev/null && FIXED=true
  fi
fi

# Return result
if [ "$FIXED" = true ]; then
  echo "{\"continue\": true, \"systemMessage\": \"Auto-fixed linting issues in $FILE_PATH\"}"
else
  echo "{\"continue\": true}"
fi

exit 0
```

**Step 6b: Update Settings for Auto-Fix**

Modify `.claude/settings.json` to run auto-fix before validation:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/lint-and-fix.sh"
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/lint-and-typecheck.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/end-of-turn-quality.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 7: Install Required Tools in Claude Code Web Environment

Ensure linting and type checking tools are available in your sessions.

**Step 7a: Create SessionStart Hook for Tool Installation**

Create `.claude/hooks/install-tools.sh`:

```bash
#!/bin/bash

echo "Installing quality check tools..."

# Install Node.js tools
if [ -f "package.json" ]; then
  echo "Installing npm dependencies..."
  npm install --silent 2>/dev/null || true
fi

# Install Python tools globally if not available
if ! command -v ruff &> /dev/null; then
  echo "Installing Ruff..."
  pip install --quiet ruff 2>/dev/null || true
fi

if ! command -v mypy &> /dev/null; then
  echo "Installing mypy..."
  pip install --quiet mypy 2>/dev/null || true
fi

# Install Go tools if needed
if [ -f "go.mod" ]; then
  if ! command -v golangci-lint &> /dev/null; then
    echo "Installing golangci-lint..."
    go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest 2>/dev/null || true
  fi
fi

echo "Tool installation complete"
exit 0
```

Make it executable and configure:
```bash
chmod +x .claude/hooks/install-tools.sh
```

**Step 7b: Add SessionStart Hook**

Update `.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/install-tools.sh"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/lint-and-fix.sh"
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/lint-and-typecheck.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/end-of-turn-quality.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 8: Test Your Pre-Commit Hook Configuration

Verify all hooks work correctly.

**Step 8a: Test Git Pre-Commit Hooks**

```bash
# Create a file with intentional errors
echo "const x: number = 'hello';" > test-error.ts

# Stage and try to commit
git add test-error.ts
git commit -m "Test pre-commit hooks"

# Should fail with type error
# Fix and retry
echo "const x: number = 42;" > test-error.ts
git add test-error.ts
git commit -m "Test pre-commit hooks"

# Should succeed
```

**Step 8b: Test Claude Code Hooks**

Start a Claude Code session and ask:
```
"Create a TypeScript function that takes a parameter without a type annotation"
```

You should see:
1. PostToolUse hook runs immediately after file creation
2. Linting/type errors are reported
3. Claude receives feedback and can fix the issues
4. Stop hook runs at end of turn for final validation

**Step 8c: Test Auto-Fix**

Ask Claude:
```
"Create a Python file with inconsistent spacing and missing type hints"
```

The auto-fix hook should:
1. Automatically format the file with Ruff
2. Report remaining type checking issues that need manual fixing

### Step 9: Configure Team-Wide Settings

Share your pre-commit hook configuration with your team.

**Step 9a: Commit Hook Configuration Files**

```bash
# Add all configuration files to git
git add .pre-commit-config.yaml
git add .claude/
git add .eslintrc.json
git add tsconfig.json
git add pyproject.toml

# Commit
git commit -m "Add pre-commit hooks and quality check configuration"

# Push to share with team
git push
```

**Step 9b: Create Setup Documentation**

Create `.claude/README.md`:

```markdown
# Claude Code Quality Checks

## Setup Instructions

### For All Team Members

1. Install pre-commit framework:
   ```bash
   pip install pre-commit
   pre-commit install
   ```

2. Install language-specific tools:
   ```bash
   # Node.js
   npm install

   # Python
   pip install ruff mypy

   # Go
   go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
   ```

3. Test pre-commit hooks:
   ```bash
   pre-commit run --all-files
   ```

### For Claude Code Web Users

Claude Code hooks are configured in `.claude/settings.json` and will:
- Auto-fix common issues after each edit
- Validate code quality after each file modification
- Run comprehensive checks at end of each turn
- Install required tools automatically via SessionStart hook

### Quality Standards

All code must pass:
- Linting (ESLint, Ruff, golangci-lint)
- Type checking (TypeScript, mypy)
- Formatting (Prettier, Ruff format, gofmt)

### Bypassing Hooks (Emergency Only)

```bash
# Skip git pre-commit hooks (not recommended)
git commit --no-verify

# Claude Code hooks cannot be bypassed during sessions
```
```

### Step 10: Monitor and Debug Hook Execution

Set up logging and debugging for your hooks.

**Step 10a: Add Logging to Hooks**

Modify your hook scripts to include logging:

```bash
#!/bin/bash

LOG_FILE="$HOME/.claude/hook-execution.log"

log() {
  echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" >> "$LOG_FILE"
}

log "PostToolUse hook started"

# ... rest of hook script ...

log "PostToolUse hook completed with exit code $EXIT_CODE"
```

**Step 10b: View Hook Logs**

```bash
# Watch hook execution in real-time
tail -f ~/.claude/hook-execution.log

# View recent hook activity
tail -n 50 ~/.claude/hook-execution.log

# Search for errors
grep -i "error\|failed" ~/.claude/hook-execution.log
```

## Expected Results

After successfully configuring pre-commit hooks for linting and type checks, you should experience:

### Immediate Feedback Loop

1. **Real-time validation**: When Claude edits a file, linting and type checking run within 1-2 seconds
2. **Auto-fix application**: Common style issues are automatically corrected
3. **Error reporting**: Type errors and linting violations are reported to Claude immediately
4. **Iterative fixes**: Claude can address issues in the same turn based on hook feedback

### End-of-Turn Quality Gates

1. **Comprehensive validation**: Stop hooks run full project-wide checks when Claude finishes
2. **Blocking on errors**: Critical issues prevent Claude from completing until resolved
3. **Clean working tree**: No uncommitted code with quality issues

### Pre-Commit Safety Net

1. **Git-level protection**: Commits are blocked if they contain linting or type errors
2. **Staged file validation**: Only files being committed are checked (fast)
3. **Team consistency**: All team members use identical quality standards

### Example Session Flow

```
User: "Add a new user authentication function"

Claude: *Creates auth.ts file*
[PostToolUse hook: Auto-fixes formatting]
[PostToolUse hook: Reports type error - missing return type]

Claude: "I see a type error. Let me fix that..."
*Adds explicit return type*
[PostToolUse hook: Validation passed]

Claude: "Authentication function complete"
[Stop hook: Running comprehensive checks...]
[Stop hook: All checks passed ✓]

User: *Makes manual edits and commits*
[Git pre-commit hook: Running...]
[Git pre-commit hook: All checks passed ✓]
[Commit succeeds]
```

## Troubleshooting

### Common Issue 1
**Problem**: Pre-commit hooks fail with "command not found" errors
**Solution**:
- Ensure tools are installed: `which eslint`, `which ruff`, `which mypy`
- Install missing tools: `npm install -g eslint`, `pip install ruff mypy`
- Check PATH includes tool locations: `echo $PATH`
- For pre-commit framework: Add `additional_dependencies` in `.pre-commit-config.yaml`
- Verify Claude Code SessionStart hook installs tools correctly
- Check hook logs: `cat ~/.claude/hook-execution.log`

### Common Issue 2
**Problem**: TypeScript type checking fails with "Cannot find module" errors
**Solution**:
- Run `npm install` to install dependencies
- Verify `tsconfig.json` includes correct paths and file patterns
- Check that `node_modules/@types` contains necessary type definitions
- Add missing type packages: `npm install -D @types/node @types/react`
- For Claude Code hooks, ensure SessionStart hook runs `npm install`
- Use `npx tsc --noEmit --listFiles` to debug which files are included

### Common Issue 3
**Problem**: Hooks are too slow and delay commits/Claude responses
**Solution**:
- Use fast linters: Biome (faster than ESLint), Ruff (faster than Pylint)
- Cache results in git pre-commit hooks: `pre-commit` framework does this automatically
- Limit PostToolUse hooks to single file only (not project-wide)
- Move comprehensive checks to Stop hooks instead of PostToolUse
- Parallelize checks where possible using `&` and `wait` in bash scripts
- Skip tests in PostToolUse, run only in Stop or git hooks
- Use `--quick` flags for linters when available

### Common Issue 4
**Problem**: Claude Code hooks don't execute
**Solution**:
- Verify hook scripts have execute permissions: `chmod +x .claude/hooks/*.sh`
- Check `.claude/settings.json` is valid JSON: `jq . .claude/settings.json`
- Ensure hook paths use `$CLAUDE_PROJECT_DIR` variable correctly
- Verify matcher patterns are case-sensitive: use `Edit` not `edit`
- Test hook manually: `echo '{"tool_input":{"file_path":"test.ts"}}' | ./.claude/hooks/script.sh`
- Check for syntax errors: `bash -n .claude/hooks/script.sh`
- Review Claude Code session logs for hook errors

### Common Issue 5
**Problem**: Stop hook blocks Claude indefinitely
**Solution**:
- Always exit with code 0 (success) or 2 (intentional block) only
- Avoid exit code 1 - it may cause unpredictable blocking behavior
- Set reasonable timeouts for long-running checks
- Use `continue: false` in JSON response to block intentionally
- Provide clear `stopReason` explaining what needs to be fixed
- Test Stop hooks don't hang: add timeout wrapper around slow operations
- Consider using non-blocking PostToolUse for feedback instead

### Common Issue 6
**Problem**: Pre-commit framework conflicts with Claude Code hooks
**Solution**:
- Both systems can coexist - they run at different times
- Pre-commit runs before git commits (git-level)
- Claude Code hooks run during Claude sessions (editor-level)
- Ensure both use same configuration files (.eslintrc.json, tsconfig.json, etc.)
- Keep tool versions synchronized across both systems
- Use `.pre-commit-config.yaml` `rev` tags to pin versions
- Test both independently: `pre-commit run --all-files` and Claude Code session

## Additional Tips

### Performance Optimization

- **Incremental checking**: Only check changed files in PostToolUse hooks
- **Caching**: Use linter caches (ESLint `--cache`, mypy cache)
- **Parallel execution**: Run independent checks concurrently
- **Smart triggers**: Use specific matchers (`Edit:*.ts`) instead of broad ones (`Edit`)
- **Skip on CI**: Disable slow hooks in CI environments where tests already run

### Security Best Practices

- **Validate file paths**: Ensure hooks only process files within project directory
- **Avoid code injection**: Use proper quoting in bash scripts
- **Review hook scripts**: Treat hooks as code - review and test thoroughly
- **Pin versions**: Lock tool versions to prevent supply chain attacks
- **Scan for secrets**: Add secret detection to pre-commit hooks

### Advanced Patterns

**Conditional validation based on file changes:**
```bash
# Only run tests if test files or source files changed
if echo "$CLAUDE_FILE_PATHS" | grep -qE '(test\.|spec\.|src/)'; then
  npm test
fi
```

**Progressive type checking:**
```bash
# Strict checking for new files, lenient for legacy code
if git diff --cached --name-only | grep -q "src/new/"; then
  npx tsc --strict --noEmit
else
  npx tsc --noEmit
fi
```

**Integration with CI/CD:**
```yaml
# .github/workflows/quality-checks.yml
name: Quality Checks
on: [push, pull_request]
jobs:
  lint-and-typecheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
      - run: pip install pre-commit
      - run: pre-commit run --all-files
```

### Hook Maintenance

- **Regular updates**: Keep linters and type checkers up to date
- **Team feedback**: Collect developer feedback on hook annoyances
- **Gradual adoption**: Start lenient, increase strictness over time
- **Documentation**: Keep `.claude/README.md` current with setup instructions
- **Shared configs**: Use extends/shared configs for consistency

### Debugging Workflow

1. **Isolate the problem**: Test git hooks and Claude Code hooks separately
2. **Run manually**: Execute hook scripts directly with test input
3. **Enable verbose output**: Temporarily remove `2>/dev/null` redirects
4. **Check tool versions**: Verify compatible versions across team
5. **Review logs**: Check both hook logs and Claude Code session transcripts

## Related Articles

- KB-062: How to configure hooks to auto-format code after edits
- KB-064: How to create PostToolUse hooks for validation
- KB-065: How to use Stop hooks for cleanup operations
- KB-061: How to create a SessionStart hook for repository initialization
- KB-066: How to debug hook failures and troubleshoot issues
- KB-067: How to share hooks across your development team
- KB-054: How to write unit tests automatically
- KB-056: How to perform code reviews with Claude

## Sources

- pre-commit: A framework for managing pre-commit hooks - https://pre-commit.com/ (Accessed: November 16, 2025)
- Claude Code Best Practices - Anthropic - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code — Use Hooks to Enforce End-of-Turn Quality Gates - Medium - https://jpcaparas.medium.com/claude-code-use-hooks-to-enforce-end-of-turn-quality-gates-5bed84e89a0d (Accessed: November 16, 2025)
- A complete guide to hooks in Claude Code: Automating your development workflow - eesel AI - https://www.eesel.ai/blog/hooks-in-claude-code (Accessed: November 16, 2025)
- Claude Code Hooks: What is and How to Use It - CometAPI - https://www.cometapi.com/claude-code-hooks-what-is-and-how-to-use-it/ (Accessed: November 16, 2025)
- Effortless Code Quality: The Ultimate Pre-Commit Hooks Guide for 2025 - Medium - https://gatlenculp.medium.com/effortless-code-quality-the-ultimate-pre-commit-hooks-guide-for-2025-57ca501d9835 (Accessed: November 16, 2025)
- GitHub: pre-commit/pre-commit-hooks - https://github.com/pre-commit/pre-commit-hooks (Accessed: November 16, 2025)
- GitHub: dhofheinz/claude-code-quality-hook - https://github.com/dhofheinz/claude-code-quality-hook (Accessed: November 16, 2025)
- Automate Your AI Workflows with Claude Code Hooks - GitButler Blog - https://blog.gitbutler.com/automate-your-ai-workflows-with-claude-code-hooks (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
