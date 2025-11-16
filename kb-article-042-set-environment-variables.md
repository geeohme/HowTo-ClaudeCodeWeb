# How to set environment variables for your session

**Article ID**: KB-042
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Environment variables in Claude Code Web allow you to configure session behavior, set API keys, define project-specific settings, and customize the execution environment. This guide explains how to set and manage environment variables for your coding sessions in the cloud-based environment.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one connected repository
- Basic understanding of environment variables
- Familiarity with JSON syntax (for configuration files)

## Understanding Environment Variables in Claude Code Web

### What Are Environment Variables?

Environment variables are dynamic values that:
- Configure Claude's behavior during a session
- Store sensitive information like API keys
- Define project-specific settings
- Persist across bash commands in a session
- Control tool integrations and features

### Important Limitations in Claude Code Web

**Cloud-Based Execution:**
- Sessions run on Anthropic-managed cloud infrastructure
- Cannot directly access your local machine's environment variables
- All variables must be explicitly configured
- Sensitive data should be handled carefully

**Key Difference from CLI:**
The CLI version can access your local environment variables, but the web version requires explicit configuration through settings files or sessionStart hooks.

## Steps

### Step 1: Choose Your Configuration Method

There are three main approaches to setting environment variables:

**Method A: Settings Files** - Best for team-wide or project-wide variables
**Method B: SessionStart Hooks** - Best for dynamic, per-session configuration
**Method C: Manual Per-Session** - Best for testing or one-time use

### Step 2: Method A - Configure via Settings Files

Environment variables can be defined in JSON configuration files at different levels.

**Configuration Hierarchy (lowest to highest priority):**
1. User settings: `~/.claude/settings.json` (applies to all projects)
2. Project settings: `.claude/settings.json` (shared with team)
3. Local project settings: `.claude/settings.local.json` (personal, gitignored)

**Step 2a: Create or Edit Settings File**

In your repository, create `.claude/settings.json`:

```json
{
  "environmentVariables": {
    "NODE_ENV": "development",
    "API_BASE_URL": "https://api.example.com",
    "LOG_LEVEL": "debug",
    "ENABLE_FEATURE_X": "true"
  }
}
```

**Step 2b: For Team Settings**

Use `.claude/settings.json` for settings to share with your team:

```json
{
  "environmentVariables": {
    "PROJECT_NAME": "my-awesome-app",
    "DEFAULT_REGION": "us-east-1",
    "BUILD_TARGET": "production"
  }
}
```

**Step 2c: For Personal Settings**

Use `.claude/settings.local.json` for personal overrides (add to `.gitignore`):

```json
{
  "environmentVariables": {
    "DEBUG_MODE": "true",
    "DEV_API_KEY": "your-personal-dev-key",
    "LOCAL_PORT": "3000"
  }
}
```

**Step 2d: Commit Team Settings**

```bash
# Add team settings to version control
git add .claude/settings.json

# Ensure local settings are gitignored
echo ".claude/settings.local.json" >> .gitignore
```

### Step 3: Method B - Use SessionStart Hooks

SessionStart hooks provide dynamic environment variable configuration that runs at the start of each session.

**Step 3a: Create SessionStart Hook**

Create `.claude/sessionStart.sh` in your repository:

```bash
#!/bin/bash

# This script runs at the start of each Claude Code session
# Use CLAUDE_ENV_FILE to persist environment variables

# Set environment variables for this session
cat >> "$CLAUDE_ENV_FILE" << EOF
export NODE_ENV=production
export API_ENDPOINT=https://api.production.example.com
export MAX_WORKERS=4
EOF

# Dynamic variables based on git branch
CURRENT_BRANCH=$(git branch --show-current)
echo "export GIT_BRANCH=$CURRENT_BRANCH" >> "$CLAUDE_ENV_FILE"

# Load secrets from a file (if available)
if [ -f ".env.local" ]; then
  cat .env.local >> "$CLAUDE_ENV_FILE"
fi

echo "Environment variables configured for session"
```

**Step 3b: Make Hook Executable**

```bash
chmod +x .claude/sessionStart.sh
```

**Step 3c: Understanding CLAUDE_ENV_FILE**

The `CLAUDE_ENV_FILE` variable points to a file that:
- Persists environment variables across bash commands
- Is automatically sourced before each command execution
- Allows sessionStart hooks to configure the session environment
- Enables dynamic variable generation

**Example Hook for Multiple Environments:**

```bash
#!/bin/bash

# Determine environment based on branch name
BRANCH=$(git branch --show-current)

if [[ "$BRANCH" == "main" ]] || [[ "$BRANCH" == "master" ]]; then
  ENV="production"
elif [[ "$BRANCH" == "staging" ]]; then
  ENV="staging"
else
  ENV="development"
fi

# Write environment-specific variables
cat >> "$CLAUDE_ENV_FILE" << EOF
export ENVIRONMENT=$ENV
export LOG_LEVEL=$([ "$ENV" = "production" ] && echo "error" || echo "debug")
export DEBUG=$([ "$ENV" = "development" ] && echo "true" || echo "false")
EOF
```

### Step 4: Method C - Manual Per-Session Configuration

For testing or one-time variable configuration within an active session.

**Step 4a: Set Variables in Session**

When Claude is working on your task, you can ask:

```
"Please set the environment variable NODE_ENV to 'test' and run the test suite"
```

or

```
"Export DATABASE_URL='postgres://localhost:5432/testdb' before running migrations"
```

**Step 4b: Verify Variables Are Set**

Ask Claude to verify:

```
"Show me the current environment variables"
```

Claude can run `env` or `printenv` to display all variables.

### Step 5: Common Environment Variables for Claude Code Web

**Authentication and API Keys:**
```json
{
  "environmentVariables": {
    "ANTHROPIC_API_KEY": "sk-ant-...",
    "GITHUB_TOKEN": "ghp_...",
    "NPM_TOKEN": "npm_..."
  }
}
```

**Build and Deploy Configuration:**
```json
{
  "environmentVariables": {
    "NODE_ENV": "production",
    "BUILD_DIR": "dist",
    "PUBLIC_URL": "/app",
    "CI": "true"
  }
}
```

**Database and Service URLs:**
```json
{
  "environmentVariables": {
    "DATABASE_URL": "postgresql://localhost:5432/mydb",
    "REDIS_URL": "redis://localhost:6379",
    "API_BASE_URL": "https://api.example.com/v1"
  }
}
```

**Feature Flags and Debug:**
```json
{
  "environmentVariables": {
    "ENABLE_ANALYTICS": "false",
    "DEBUG": "true",
    "VERBOSE_LOGGING": "true",
    "DRY_RUN": "true"
  }
}
```

### Step 6: Verify Environment Variables

**Option A: During Session Creation**

When creating a task, specify:
```
"Before starting, verify that NODE_ENV is set to 'development' and show me all environment variables"
```

**Option B: In SessionStart Hook**

Add verification to your hook:

```bash
#!/bin/bash

# Set variables
cat >> "$CLAUDE_ENV_FILE" << EOF
export NODE_ENV=production
export API_KEY=secret123
EOF

# Verify and log
echo "=== Environment Variables Set ==="
echo "NODE_ENV=production"
echo "API_KEY=[REDACTED]"
```

**Option C: Ask Claude to Check**

During an active session:
```
"Run 'echo $NODE_ENV' to verify the environment"
```

## Expected Results

After setting environment variables, you should:
- See variables available in bash commands during sessions
- Have variables persist across multiple bash executions in same session
- Be able to reference variables in scripts and commands
- See consistent behavior based on configured values
- Have team members share the same environment configuration (for team settings)

**Example Verification:**

When you ask Claude to run:
```bash
echo "Environment: $NODE_ENV"
echo "API URL: $API_BASE_URL"
```

You should see output like:
```
Environment: production
API URL: https://api.example.com
```

## Troubleshooting

### Common Issue 1
**Problem**: Environment variables not available in session
**Solution**:
- Verify settings file syntax is valid JSON
- Check that file is in correct location (`.claude/settings.json`)
- Ensure sessionStart hook has execute permissions (`chmod +x`)
- Verify `CLAUDE_ENV_FILE` is being written to in sessionStart hook
- Check for typos in variable names
- Restart the session to pick up new configuration

### Common Issue 2
**Problem**: Variables set in sessionStart hook not persisting
**Solution**:
- Ensure you're writing to `$CLAUDE_ENV_FILE`, not just using `export`
- Use `>>` to append, not `>` to overwrite
- Verify the hook is executing (add echo statements)
- Check that hook file has Unix line endings (LF, not CRLF)
- Make sure the hook doesn't exit early with errors

### Common Issue 3
**Problem**: Sensitive values (API keys) exposed in logs
**Solution**:
- Use `.claude/settings.local.json` for secrets (and gitignore it)
- Never commit secrets to `.claude/settings.json`
- Consider using redacted placeholders in team settings
- Ask team members to create their own `settings.local.json`
- Review git history to ensure no secrets were committed

### Common Issue 4
**Problem**: Variables have wrong values or get overridden
**Solution**:
- Understand the configuration hierarchy (local > project > user)
- Check all settings files for conflicting definitions
- Local settings override project settings override user settings
- Use `settings.local.json` for personal overrides
- Remove duplicate variable definitions

### Common Issue 5
**Problem**: SessionStart hook not running
**Solution**:
- Verify file is named exactly `.claude/sessionStart.sh`
- Check file has execute permissions (`chmod +x`)
- Ensure file has valid bash syntax (`bash -n .claude/sessionStart.sh`)
- Look for errors in session logs
- Try adding `echo "Hook running"` at the start to debug

## Additional Tips

### Security Best Practices

**DO:**
- Use `settings.local.json` for sensitive values
- Add `.claude/settings.local.json` to `.gitignore`
- Use placeholder values in shared settings
- Document required variables in README
- Rotate API keys regularly

**DON'T:**
- Commit secrets to version control
- Share API keys in team settings files
- Hardcode credentials in sessionStart hooks
- Log sensitive values to console
- Use production credentials in development

### Variable Naming Conventions

**Use consistent naming:**
```bash
# Good - uppercase with underscores
DATABASE_URL=postgres://...
API_BASE_URL=https://...
MAX_RETRY_ATTEMPTS=3

# Avoid - mixed case or unclear names
dbUrl=postgres://...
apiURL=https://...
maxRetries=3
```

### Team Collaboration

**Setup for team settings:**

1. Create `.claude/settings.json` with non-sensitive defaults
2. Document required variables in README
3. Have team members create their own `settings.local.json`
4. Provide a template file (`.claude/settings.local.example.json`)

**Example template:**
```json
{
  "environmentVariables": {
    "DATABASE_URL": "postgresql://localhost:5432/mydb_dev",
    "API_KEY": "your-development-api-key-here",
    "DEBUG": "true"
  }
}
```

### Dynamic Configuration

**Branch-based environments:**
```bash
# In sessionStart.sh
BRANCH=$(git branch --show-current)
case "$BRANCH" in
  main|master)
    echo "export ENV=production" >> "$CLAUDE_ENV_FILE"
    ;;
  staging)
    echo "export ENV=staging" >> "$CLAUDE_ENV_FILE"
    ;;
  *)
    echo "export ENV=development" >> "$CLAUDE_ENV_FILE"
    ;;
esac
```

**Date/time-based variables:**
```bash
# Set build timestamp
echo "export BUILD_TIMESTAMP=$(date +%Y%m%d_%H%M%S)" >> "$CLAUDE_ENV_FILE"
```

### Testing Your Configuration

**Create a test task:**
```
"Please display all environment variables set in this session and verify that NODE_ENV, API_URL, and DATABASE_URL are configured correctly"
```

**Test sessionStart hook:**
```
"Show me the contents of the CLAUDE_ENV_FILE after sessionStart runs"
```

## Related Articles
- KB-016: How to start a new Claude Code session
- KB-006: How to create your first coding task in Claude Code Web
- KB-021: How to provide additional context to Claude during a task

## Sources
- Claude Code Settings Documentation - https://docs.claude.com/en/docs/claude-code/settings (Accessed: November 16, 2025)
- Claude Code Web Official Documentation - https://code.claude.com/docs/en/claude-code-on-the-web (Accessed: November 16, 2025)
- Environment Variables in Claude Code - Complete Guide - https://www.eesel.ai/blog/environment-variables-claude-code (Accessed: November 16, 2025)
- Claude Code Environment Variables Reference - https://medium.com/@dan.avila7/claude-code-environment-variables-a-complete-reference-guide-41229ef18120 (Accessed: November 16, 2025)
- Google Search: "claude code web environment variables sessionstart hook 2025" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
