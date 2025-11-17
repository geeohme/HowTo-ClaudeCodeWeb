# How to create a SessionStart hook for repository initialization

**Article ID**: KB-061
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
SessionStart hooks are powerful automation tools in Claude Code Web that execute automatically when a new session begins or resumes. They enable you to initialize your repository environment by installing dependencies, loading project context, setting environment variables, and preparing the workspace for development. This automation ensures consistent, ready-to-code environments across all sessions without manual setup.

## Prerequisites
- Claude Code Web with an active Pro or Max subscription
- A connected GitHub repository
- Basic understanding of shell scripting (bash)
- Familiarity with JSON configuration files
- Understanding of environment variables (see KB-042)

## Understanding SessionStart Hooks

### What Are SessionStart Hooks?

SessionStart hooks are scripts or commands that run automatically when:
- A new Claude Code session starts (`startup`)
- An existing session resumes (`resume`)
- A session is cleared and restarted (`clear`)

### Key Characteristics

**Context Injection**: Unlike other hooks, SessionStart hook output (stdout) is automatically added to Claude's context when the hook exits with code 0. This allows you to inject important project information, conventions, or constraints that Claude should know throughout the session.

**Environment Persistence**: SessionStart hooks have access to `CLAUDE_ENV_FILE`, a special environment variable pointing to a file that persists environment variables across all bash commands in the session.

**Platform Support**: SessionStart hooks run in both local Claude Code CLI and Claude Code Web environments, making them ideal for team-wide repository initialization.

**Execution Timing**: The hook executes after the repository is cloned but before Claude starts processing user tasks.

## Steps

### Step 1: Create the .claude Directory Structure

First, ensure your repository has the proper directory structure for Claude Code configuration.

```bash
# Navigate to your repository root
cd /path/to/your/repository

# Create .claude directory if it doesn't exist
mkdir -p .claude
```

**Verification**: Check that the directory was created:

```bash
ls -la | grep .claude
```

You should see: `drwxr-xr-x  .claude`

### Step 2: Choose Your Hook Configuration Method

There are two approaches to configuring SessionStart hooks:

**Method A: Command-based** - Direct bash commands in settings.json (best for simple operations)
**Method B: Script-based** - Separate shell script file (best for complex initialization)

For repository initialization, Method B (script-based) is recommended as it provides better maintainability and readability.

### Step 3: Create the SessionStart Hook Script

Create a new shell script file in the `.claude` directory:

```bash
touch .claude/sessionStart.sh
chmod +x .claude/sessionStart.sh
```

Open `.claude/sessionStart.sh` in your editor and add your initialization logic:

```bash
#!/bin/bash

# SessionStart Hook for Repository Initialization
# This script runs automatically when Claude Code starts a new session

set -e  # Exit on error

echo "=== Initializing Repository Environment ==="

# Install project dependencies based on your stack
if [ -f "package.json" ]; then
  echo "Installing Node.js dependencies..."
  npm install
fi

if [ -f "requirements.txt" ]; then
  echo "Installing Python dependencies..."
  pip install -r requirements.txt
fi

if [ -f "Gemfile" ]; then
  echo "Installing Ruby dependencies..."
  bundle install
fi

if [ -f "go.mod" ]; then
  echo "Installing Go dependencies..."
  go mod download
fi

# Set environment variables for the session
if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo "Configuring environment variables..."
  cat >> "$CLAUDE_ENV_FILE" << EOF
export NODE_ENV=development
export PROJECT_ROOT=$(pwd)
export PATH="\$PATH:./node_modules/.bin"
EOF
fi

# Load project context and conventions
echo ""
echo "=== Project Context ==="
echo "Repository: $(basename $(pwd))"
echo "Branch: $(git branch --show-current)"
echo "Last commit: $(git log -1 --oneline)"
echo ""
echo "=== Development Conventions ==="
echo "- Use ESLint for JavaScript linting"
echo "- Run 'npm test' before committing"
echo "- Follow conventional commits format"
echo "- All PRs require code review"
echo ""

echo "✓ Repository initialization complete"
exit 0
```

**Important**: The `exit 0` ensures the stdout output is added to Claude's context. Any non-zero exit code will cause the output to be hidden.

### Step 4: Configure the Hook in settings.json

Create or edit `.claude/settings.json` to reference your SessionStart script:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/sessionStart.sh"
          }
        ]
      }
    ]
  }
}
```

**Configuration Breakdown**:
- `matcher: "startup"` - Hook runs when a new session starts
- `type: "command"` - Execute a shell command
- `$CLAUDE_PROJECT_DIR` - Environment variable pointing to project root
- Command path is quoted to handle spaces in directory names

### Step 5: Add Multiple Matchers (Optional)

You can configure different behaviors for startup vs. resume scenarios:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/sessionStart.sh"
          }
        ]
      },
      {
        "matcher": "resume",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/sessionResume.sh"
          }
        ]
      }
    ]
  }
}
```

This allows you to run full dependency installation on `startup` but only quick environment checks on `resume`.

### Step 6: Advanced - Load Project Issues and Context

Enhance your SessionStart hook to load relevant project context:

```bash
#!/bin/bash

set -e

echo "=== Loading Project Context ==="

# Load recent issues (if you have gh CLI configured)
if command -v gh &> /dev/null; then
  echo ""
  echo "Open Issues (last 5):"
  gh issue list --limit 5 --json number,title --template '{{range .}}#{{.number}}: {{.title}}{{"\n"}}{{end}}' || echo "No issues or gh not authenticated"
fi

# Show recent changes
echo ""
echo "Recent Changes:"
git log --oneline --max-count=5

# Load TODO comments from codebase
echo ""
echo "Active TODOs in codebase:"
grep -r "TODO:" --include="*.js" --include="*.py" --include="*.go" . 2>/dev/null | head -n 10 || echo "No TODOs found"

# Set environment based on branch
BRANCH=$(git branch --show-current)
if [ -n "$CLAUDE_ENV_FILE" ]; then
  if [[ "$BRANCH" == "main" ]] || [[ "$BRANCH" == "master" ]]; then
    echo "export ENV_TYPE=production" >> "$CLAUDE_ENV_FILE"
  elif [[ "$BRANCH" == "staging" ]]; then
    echo "export ENV_TYPE=staging" >> "$CLAUDE_ENV_FILE"
  else
    echo "export ENV_TYPE=development" >> "$CLAUDE_ENV_FILE"
  fi
fi

echo ""
echo "✓ Context loaded successfully"
exit 0
```

### Step 7: Environment-Specific Configuration

Configure different initialization for web vs. local environments:

```bash
#!/bin/bash

set -e

# Detect environment
if [ -n "$CLAUDE_WEB" ]; then
  echo "=== Initializing for Claude Code Web ==="
  ENV="web"
else
  echo "=== Initializing for Claude Code CLI ==="
  ENV="local"
fi

# Install dependencies (always needed in web sessions)
if [ "$ENV" = "web" ] || [ ! -d "node_modules" ]; then
  echo "Installing dependencies..."
  npm install --prefer-offline --no-audit
fi

# Set environment-specific variables
if [ -n "$CLAUDE_ENV_FILE" ]; then
  if [ "$ENV" = "web" ]; then
    echo "export RUNNING_IN_WEB=true" >> "$CLAUDE_ENV_FILE"
    echo "export DATABASE_URL=sqlite:///tmp/dev.db" >> "$CLAUDE_ENV_FILE"
  else
    echo "export RUNNING_IN_WEB=false" >> "$CLAUDE_ENV_FILE"
    # Load local .env file if it exists
    if [ -f ".env" ]; then
      cat .env >> "$CLAUDE_ENV_FILE"
    fi
  fi
fi

exit 0
```

### Step 8: Add Error Handling

Make your hook resilient to failures:

```bash
#!/bin/bash

# Function to handle errors
handle_error() {
  echo "⚠ Error during initialization: $1"
  # Don't exit with error - allow session to continue
  exit 0
}

echo "=== Repository Initialization ==="

# Try to install dependencies with error handling
if [ -f "package.json" ]; then
  echo "Installing Node.js dependencies..."
  npm install 2>&1 || handle_error "npm install failed"
fi

# Verify critical files exist
if [ ! -f "package.json" ]; then
  handle_error "package.json not found - is this a Node.js project?"
fi

# Always succeed so Claude can start
echo "✓ Initialization complete"
exit 0
```

### Step 9: Test Your SessionStart Hook

Before committing, test your hook locally:

```bash
# Test the script directly
bash .claude/sessionStart.sh

# Verify it runs successfully
echo $?  # Should output: 0

# Check for syntax errors
bash -n .claude/sessionStart.sh
```

**Expected Output**: You should see all your echo statements and no error messages.

### Step 10: Commit and Deploy

Add your SessionStart hook configuration to version control:

```bash
# Add the configuration files
git add .claude/sessionStart.sh
git add .claude/settings.json

# Ensure the script is executable in git
git update-index --chmod=+x .claude/sessionStart.sh

# Commit
git commit -m "Add SessionStart hook for repository initialization"

# Push to remote
git push
```

**Note**: The SessionStart hook will now run automatically for all team members when they start a Claude Code session with this repository.

## Expected Results

After implementing a SessionStart hook, you should observe:

### During Session Start

1. **Hook Execution Confirmation**: Claude Code displays output from your SessionStart script
2. **Dependencies Installed**: All project dependencies are ready without manual intervention
3. **Environment Variables Set**: Configured variables are available in all bash commands
4. **Context Loaded**: Project information, conventions, and constraints are visible to Claude

### In Active Sessions

1. **Ready-to-Code Environment**: No need to manually run `npm install` or similar commands
2. **Consistent Configuration**: All team members have identical development environments
3. **Context-Aware Claude**: Claude understands project conventions from SessionStart output
4. **Persistent Variables**: Environment variables set in SessionStart work across all commands

### Example Session Output

When you start a new session, you'll see:

```
=== Initializing Repository Environment ===
Installing Node.js dependencies...
added 234 packages in 12s
Configuring environment variables...

=== Project Context ===
Repository: my-awesome-app
Branch: feature/new-dashboard
Last commit: abc1234 Add user authentication

=== Development Conventions ===
- Use ESLint for JavaScript linting
- Run 'npm test' before committing
- Follow conventional commits format
- All PRs require code review

✓ Repository initialization complete
```

## Troubleshooting

### Common Issue 1
**Problem**: SessionStart hook doesn't execute when starting a new session
**Solution**:
- Verify `.claude/settings.json` has valid JSON syntax (use `python3 -m json.tool .claude/settings.json`)
- Check that the hook script path in settings.json is correct
- Ensure the script file has execute permissions (`chmod +x .claude/sessionStart.sh`)
- Verify the file has Unix line endings (LF), not Windows (CRLF)
- Test the script manually: `bash .claude/sessionStart.sh`
- Check for typos in the matcher value ("startup" not "start")

### Common Issue 2
**Problem**: Hook runs but output isn't visible in Claude's context
**Solution**:
- Ensure your script exits with code 0 (`exit 0` at the end)
- Check that echo statements are going to stdout, not stderr
- Verify there are no syntax errors that cause early exit
- Test exit code: Run the script and check `echo $?` (should be 0)
- Remove `set -e` temporarily to see if a command is failing silently
- Add more verbose logging to identify where execution stops

### Common Issue 3
**Problem**: Environment variables not persisting across bash commands
**Solution**:
- Verify you're writing to `$CLAUDE_ENV_FILE`, not creating a new file
- Use `>>` (append) not `>` (overwrite) when writing to CLAUDE_ENV_FILE
- Check that CLAUDE_ENV_FILE is not empty: Add `test -n "$CLAUDE_ENV_FILE"` check
- Use proper export syntax: `echo "export VAR=value" >> "$CLAUDE_ENV_FILE"`
- Verify the format matches bash export syntax exactly
- Test manually: `cat "$CLAUDE_ENV_FILE"` to see what was written

### Common Issue 4
**Problem**: Dependency installation fails in Claude Code Web
**Solution**:
- Check network access is enabled for your repository (see KB-039)
- Verify package manager is available in the web environment
- Use `--prefer-offline` flag for npm to use cached packages
- Add timeout handling for slow network operations
- Consider caching dependencies or using lockfiles
- Check for platform-specific dependencies that might fail in web environment
- Use conditional installation: Check if node_modules exists before installing

### Common Issue 5
**Problem**: Hook runs on Windows but causes infinite hang (known bug)
**Solution**:
- This is a known issue on Windows platforms as of November 2025
- Use Claude Code Web instead of Windows CLI until the bug is fixed
- Or disable SessionStart hooks temporarily on Windows
- Use `matcher` conditions to skip hooks on Windows
- Track the issue at: github.com/anthropics/claude-code/issues/9542
- As workaround, use manual initialization commands instead of hooks

### Common Issue 6
**Problem**: Hook script works locally but fails in Claude Code Web
**Solution**:
- Check that script doesn't rely on local-only tools or files
- Verify all required tools are available in web environment (see KB-040)
- Don't reference files outside the repository
- Avoid using absolute paths; use relative paths from project root
- Test without relying on user-specific configurations
- Use `$CLAUDE_PROJECT_DIR` instead of `$(pwd)` for portability

## Additional Tips

### Performance Optimization

**Keep Hooks Fast**: SessionStart hooks delay the start of every session. Optimize for speed:

```bash
# Good - parallel installation where possible
npm install & pip install -r requirements.txt & wait

# Good - skip if already installed
[ -d "node_modules" ] || npm install

# Avoid - slow operations that could be deferred
# Don't run full test suites or heavy builds in SessionStart
```

### Security Best Practices

**Never Hardcode Secrets**: Don't put API keys or credentials in SessionStart hooks:

```bash
# BAD - Don't do this
echo "export API_KEY=sk-1234567890abcdef" >> "$CLAUDE_ENV_FILE"

# GOOD - Load from external secure source
if [ -f ".env.local" ]; then
  cat .env.local >> "$CLAUDE_ENV_FILE"
fi
```

**Validate Input**: If your hook accepts parameters, validate them:

```bash
# Validate required tools exist
command -v npm >/dev/null 2>&1 || {
  echo "npm is required but not installed"
  exit 1
}
```

### Team Collaboration

**Document Your Hook**: Add comments explaining what the hook does:

```bash
#!/bin/bash
# SessionStart Hook - Repository Initialization
#
# This hook automatically:
# - Installs npm dependencies
# - Sets up development environment variables
# - Loads project conventions into Claude's context
#
# Last updated: 2025-11-16
# Maintainer: @yourteam
```

**Provide Examples**: Create a `.claude/sessionStart.example.sh` as a template for team members to customize.

**Version Control**: Always commit hooks to git so team members benefit automatically.

### Conditional Logic

**Branch-based Initialization**:

```bash
BRANCH=$(git branch --show-current)

if [[ "$BRANCH" =~ ^feature/.* ]]; then
  echo "Feature branch detected - using development settings"
  echo "export ENV=development" >> "$CLAUDE_ENV_FILE"
elif [[ "$BRANCH" == "main" ]]; then
  echo "Main branch - using production settings"
  echo "export ENV=production" >> "$CLAUDE_ENV_FILE"
fi
```

**Stack Detection**:

```bash
# Auto-detect project type and install accordingly
if [ -f "package.json" ]; then
  STACK="node"
  npm install
elif [ -f "requirements.txt" ]; then
  STACK="python"
  pip install -r requirements.txt
elif [ -f "go.mod" ]; then
  STACK="go"
  go mod download
fi

echo "export PROJECT_STACK=$STACK" >> "$CLAUDE_ENV_FILE"
```

### Debugging Techniques

**Verbose Logging**:

```bash
#!/bin/bash
set -x  # Enable debug mode - prints each command before execution

# Your hook logic here

set +x  # Disable debug mode
```

**Log to File**:

```bash
LOG_FILE=".claude/sessionStart.log"
exec 1> >(tee -a "$LOG_FILE")
exec 2>&1

echo "[$(date)] SessionStart hook starting..."
# Your hook logic here
echo "[$(date)] SessionStart hook completed"
```

### Integration with Other Tools

**Load from .env Files**:

```bash
# Load shared .env file
if [ -f ".env" ]; then
  while IFS='=' read -r key value; do
    # Skip comments and empty lines
    [[ $key =~ ^#.*$ ]] && continue
    [[ -z $key ]] && continue
    echo "export $key=$value" >> "$CLAUDE_ENV_FILE"
  done < .env
fi
```

**GitHub CLI Integration**:

```bash
# Load assigned issues for context
if command -v gh &> /dev/null; then
  echo "Your assigned issues:"
  gh issue list --assignee @me --limit 5
fi
```

**Docker Environment**:

```bash
# Start required services
if [ -f "docker-compose.yml" ]; then
  echo "Starting Docker services..."
  docker-compose up -d database redis
fi
```

## Related Articles
- KB-042: How to set environment variables for your session
- KB-043: How to configure project-specific settings in .claude/settings.json
- KB-044: How to use the config command
- KB-016: How to start a new Claude Code session
- KB-039: How to configure network access
- KB-040: How to view preinstalled toolchains

## Sources
- Claude Code Hooks Reference - https://docs.claude.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
- Claude Code on the Web - https://docs.claude.com/en/docs/claude-code/claude-code-on-the-web (Accessed: November 16, 2025)
- A complete guide to hooks in Claude Code - https://www.eesel.ai/blog/hooks-in-claude-code (Accessed: November 16, 2025)
- The ultimate guide to Claude Code Hooks - https://www.augmentedswe.com/p/guide-to-claude-code-hooks (Accessed: November 16, 2025)
- Claude Code Hooks Mastery Repository - https://github.com/disler/claude-code-hooks-mastery (Accessed: November 16, 2025)
- Feature Request: SessionStart and SessionEnd Lifecycle Hooks - https://github.com/anthropics/claude-code/issues/4318 (Accessed: November 16, 2025)
- Google Search: "Claude Code Web SessionStart hook repository initialization 2025" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
