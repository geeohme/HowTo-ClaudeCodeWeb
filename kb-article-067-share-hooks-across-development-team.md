# How to share hooks across your development team

**Article ID**: KB-067
**Difficulty**: Advanced
**Estimated Time**: 15-20 minutes
**Last Updated**: November 16, 2025

## Overview
Hooks in Claude Code Web are automated triggers that run shell commands at specific points in Claude's operation. Sharing hooks across your development team ensures consistent code quality, formatting, testing, and security practices. By properly configuring hooks in version control, you can create standardized workflows that every team member benefits from automatically, whether running linters, formatters, or custom validation scripts.

## Prerequisites
- Claude Code Web Pro or Max account with repository access
- A connected GitHub repository with write permissions
- Basic understanding of JSON file format
- Familiarity with Git and version control
- Understanding of your team's development toolchain (linters, formatters, test frameworks)
- KB-043: How to configure project-specific settings in .claude/settings.json

## Steps

### Step 1: Understand Hook Types and Lifecycle Events

Claude Code supports 8 lifecycle events where hooks can be triggered:

- **SessionStart**: Runs when Claude Code starts a new session or resumes an existing session. Useful for loading development context, installing dependencies, or setting environment variables.
- **UserPromptSubmit**: Triggered when a user submits a prompt
- **PreToolUse**: Runs before Claude executes a tool (read, write, edit, bash, etc.)
- **PostToolUse**: Runs after a tool completes execution
- **Stop**: Triggered when Claude finishes responding

For team sharing, the most commonly used hooks are:
- **SessionStart**: For environment setup and context loading
- **PostToolUse**: For running formatters, linters, and validators after code changes
- **PreToolUse**: For security checks and preventing unwanted operations

### Step 2: Create the Shared Hooks Configuration

Navigate to your project root and ensure the `.claude` directory exists:

```bash
mkdir -p .claude
```

Create or edit `.claude/settings.json` in your repository:

```bash
touch .claude/settings.json
```

Open the file and add your hooks configuration. Here's a comprehensive example for a JavaScript/TypeScript project:

```json
{
  "hooks": {
    "SessionStart": {
      "command": "npm install && npm run build",
      "description": "Install dependencies and build project on session start"
    },
    "PostToolUse": [
      {
        "tool": "Write",
        "pattern": "**/*.{js,jsx,ts,tsx}",
        "command": "npx prettier --write ${file}",
        "description": "Auto-format JavaScript/TypeScript files"
      },
      {
        "tool": "Edit",
        "pattern": "**/*.{js,jsx,ts,tsx}",
        "command": "npx prettier --write ${file} && npx eslint --fix ${file}",
        "description": "Format and lint on edit"
      },
      {
        "tool": "Write",
        "pattern": "**/*.py",
        "command": "black ${file} && pylint ${file}",
        "description": "Auto-format and lint Python files"
      }
    ],
    "PreToolUse": {
      "tool": "Write",
      "pattern": "**/production/**",
      "command": "echo 'ERROR: Cannot modify production files' && exit 1",
      "description": "Prevent modifications to production directory"
    }
  }
}
```

**Note**: The `${file}` variable is automatically replaced with the path of the affected file.

### Step 3: Configure SessionStart Hooks for Team Context

SessionStart hooks are particularly valuable for teams. They can load shared development context and ensure everyone has the same environment:

```json
{
  "hooks": {
    "SessionStart": {
      "command": "cat .claude/team-context.md",
      "hookSpecificOutput": {
        "additionalContext": true
      },
      "description": "Load team development guidelines and context"
    }
  }
}
```

The `hookSpecificOutput.additionalContext` field adds the command's output to Claude's context. This is useful for:
- Loading team coding standards
- Displaying recent issues or PRs
- Showing current sprint goals
- Loading architectural decision records (ADRs)

You can chain multiple commands in SessionStart:

```json
{
  "hooks": {
    "SessionStart": {
      "command": "npm install && npm test && cat .claude/team-context.md",
      "hookSpecificOutput": {
        "additionalContext": true
      },
      "description": "Install deps, run tests, load context"
    }
  }
}
```

### Step 4: Create Language-Specific Hook Configurations

Different tech stacks require different hooks. Here are examples for common languages:

**Python Project:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "tool": "Write",
        "pattern": "**/*.py",
        "command": "black ${file}",
        "description": "Format Python with Black"
      },
      {
        "tool": "Edit",
        "pattern": "**/*.py",
        "command": "black ${file} && isort ${file} && mypy ${file}",
        "description": "Format, sort imports, and type check"
      }
    ],
    "SessionStart": {
      "command": "pip install -r requirements.txt && pytest",
      "description": "Install dependencies and run tests"
    }
  }
}
```

**Go Project:**
```json
{
  "hooks": {
    "PostToolUse": {
      "tool": "Write",
      "pattern": "**/*.go",
      "command": "gofmt -w ${file} && go vet ${file}",
      "description": "Format and vet Go files"
    },
    "SessionStart": {
      "command": "go mod download && go test ./...",
      "description": "Download modules and run tests"
    }
  }
}
```

**Rust Project:**
```json
{
  "hooks": {
    "PostToolUse": {
      "tool": "Write",
      "pattern": "**/*.rs",
      "command": "cargo fmt -- ${file} && cargo clippy -- -D warnings",
      "description": "Format and lint Rust files"
    },
    "SessionStart": {
      "command": "cargo build",
      "description": "Build project on session start"
    }
  }
}
```

### Step 5: Implement Security Hooks for Team Protection

Use PreToolUse hooks to prevent common security mistakes:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "tool": "Write",
        "pattern": "**/.env*",
        "command": "echo 'ERROR: Cannot modify .env files through Claude' && exit 1",
        "description": "Block .env file modifications"
      },
      {
        "tool": "Write",
        "pattern": "**/secrets/**",
        "command": "echo 'ERROR: Cannot write to secrets directory' && exit 1",
        "description": "Protect secrets directory"
      },
      {
        "tool": "Bash",
        "command": ".*rm -rf.*",
        "commandPattern": true,
        "command": "echo 'WARNING: Dangerous rm -rf command blocked' && exit 1",
        "description": "Block dangerous deletion commands"
      }
    ]
  }
}
```

This ensures that no team member accidentally allows Claude to modify sensitive files or run dangerous commands.

### Step 6: Set Up Environment Variables for Hooks

SessionStart hooks have access to a special environment variable `CLAUDE_ENV_FILE`. You can use this to persist environment variables for subsequent bash commands:

```json
{
  "hooks": {
    "SessionStart": {
      "command": "echo 'export PROJECT_ROOT=/workspace' >> $CLAUDE_ENV_FILE && echo 'export NODE_ENV=development' >> $CLAUDE_ENV_FILE",
      "description": "Set up environment variables for team"
    }
  }
}
```

This ensures all team members have consistent environment variables during their Claude Code sessions.

### Step 7: Create Team Context Documentation

Create a `.claude/team-context.md` file that your SessionStart hook can load:

```markdown
# Team Development Context

## Current Sprint (Sprint 42)
- Focus: User authentication refactor
- Due: November 30, 2025

## Architecture Guidelines
- All API calls go through `src/services/api.ts`
- Use functional components with hooks (no class components)
- Follow atomic design principles for components

## Active Conventions
- Branch naming: feature/TICKET-description
- Commit format: [TICKET] Description
- PR reviews require 2 approvals

## Recent Changes
- Migrated to Vitest from Jest (Oct 2025)
- Updated to React 19 (Nov 2025)
- New ESLint rules enforced (see .eslintrc.json)

## Common Issues
- Remember to run `npm run build` after dependency changes
- Database migrations require manual review
- E2E tests run in CI only, not locally
```

Reference this in your SessionStart hook:

```json
{
  "hooks": {
    "SessionStart": {
      "command": "cat .claude/team-context.md",
      "hookSpecificOutput": {
        "additionalContext": true
      }
    }
  }
}
```

### Step 8: Commit Hooks to Version Control

Add the hooks configuration to Git:

```bash
git add .claude/settings.json
```

If you created team context documentation:

```bash
git add .claude/team-context.md
```

Commit with a descriptive message:

```bash
git commit -m "Add Claude Code hooks for team consistency

- SessionStart hook loads team context and runs tests
- PostToolUse hooks auto-format JS/Python/Go files
- PreToolUse hooks protect sensitive files
- Ensures consistent development environment across team"
```

Push to your repository:

```bash
git push origin main
```

### Step 9: Document Hooks for Your Team

Create documentation explaining the hooks to your team. Add this to your project's README.md or CONTRIBUTING.md:

```markdown
## Claude Code Hooks

This project uses Claude Code hooks to maintain code quality and consistency:

### Automatic Formatting
- JavaScript/TypeScript files are auto-formatted with Prettier
- Python files are formatted with Black
- Go files are formatted with gofmt

### Session Initialization
- Dependencies are automatically installed on session start
- Tests run to ensure clean baseline
- Team context and conventions are loaded

### Security Protections
- `.env` files cannot be modified
- `secrets/` directory is write-protected
- Dangerous bash commands are blocked

### Requirements
- Ensure formatters are installed: `npm install -D prettier eslint`
- Python projects need: `pip install black pylint isort`
- Go projects need: Go toolchain with `gofmt` and `go vet`

To skip a hook temporarily (not recommended), use `.claude/settings.local.json`
```

### Step 10: Enable Team Members to Use Shared Hooks

When team members clone the repository or pull the latest changes, the hooks will automatically be available in their Claude Code Web sessions.

**Team member checklist:**
1. Pull latest changes: `git pull origin main`
2. Verify `.claude/settings.json` exists: `ls -la .claude/`
3. Start a new Claude Code session
4. Hooks will execute automatically based on their triggers

**For local overrides**, team members can create `.claude/settings.local.json`:

```json
{
  "hooks": {
    "PostToolUse": {
      "enabled": false
    }
  }
}
```

Make sure `.claude/settings.local.json` is in `.gitignore`:

```bash
echo ".claude/settings.local.json" >> .gitignore
git add .gitignore
git commit -m "Add .claude/settings.local.json to gitignore"
```

### Step 11: Use Claude Code Plugins (Advanced)

As of October 2025, Claude Code supports **plugins** - custom collections of slash commands, agents, MCP servers, and hooks that install with a single command.

To create a plugin for your team hooks:

1. Create a plugin manifest file `my-team-plugin.json`:

```json
{
  "name": "my-team-dev-hooks",
  "version": "1.0.0",
  "description": "Team development hooks for consistent code quality",
  "hooks": {
    "SessionStart": {
      "command": "npm install && npm test",
      "description": "Install and test on session start"
    },
    "PostToolUse": [
      {
        "tool": "Write",
        "pattern": "**/*.{js,jsx,ts,tsx}",
        "command": "npx prettier --write ${file}",
        "description": "Auto-format JS/TS files"
      }
    ]
  },
  "commands": {
    "/team-status": {
      "description": "Show current team sprint status",
      "prompt": "cat .claude/team-context.md"
    }
  }
}
```

2. Share the plugin with your team via a Git repository or internal package registry

3. Team members install with: `/plugin install my-team-dev-hooks`

Plugins are particularly useful for:
- Sharing configurations across multiple repositories
- Creating reusable hook sets for different project types
- Bundling hooks with custom slash commands and agents
- Distributing organization-wide standards

### Step 12: Monitor and Iterate on Hook Performance

After deploying hooks to your team:

1. **Gather feedback**: Ask team members about hook execution time and any false positives
2. **Monitor failures**: Check if hooks are failing due to missing dependencies or environment issues
3. **Optimize performance**: Long-running hooks can slow down workflows
4. **Refine patterns**: Adjust file patterns if hooks are triggering too broadly or too narrowly

Example of optimizing a slow hook:

**Before (slow):**
```json
{
  "PostToolUse": {
    "tool": "Write",
    "pattern": "**/*.js",
    "command": "npm test",
    "description": "Run all tests after any JS change"
  }
}
```

**After (optimized):**
```json
{
  "PostToolUse": {
    "tool": "Write",
    "pattern": "**/*.test.js",
    "command": "npm test -- ${file}",
    "description": "Run only the affected test file"
  }
}
```

## Expected Results

After successfully sharing hooks across your development team, you should observe:

1. **Consistent Code Formatting**: All code changes made through Claude Code are automatically formatted according to team standards
2. **Automatic Quality Checks**: Linters and type checkers run automatically, catching issues before they reach code review
3. **Standardized Environment**: All team members start sessions with the same dependencies, environment variables, and context
4. **Enhanced Security**: Sensitive files and directories are protected from accidental modifications
5. **Faster Onboarding**: New team members automatically get the correct development setup
6. **Reduced Code Review Friction**: Style and formatting issues are handled automatically

**Verification steps:**
- Ask Claude to modify a file and verify formatters run automatically
- Start a new session and check that SessionStart hooks execute
- Try to modify a protected file and verify the PreToolUse hook blocks it
- Review git history to confirm hooks are being committed and pulled by team

## Troubleshooting

### Common Issue 1
**Problem**: Hooks are not executing for team members after pulling changes
**Solution**:
- Verify the hooks are in `.claude/settings.json` (not `.local`)
- Ensure team members restart their Claude Code session after pulling
- Check that required tools (prettier, eslint, black, etc.) are installed
- Verify JSON syntax is valid: `python3 -m json.tool .claude/settings.json`
- Check file permissions: `ls -la .claude/settings.json`

### Common Issue 2
**Problem**: PostToolUse hooks are too slow and blocking workflows
**Solution**:
- Optimize commands to run only on affected files using `${file}` variable
- Remove expensive operations like full test suites from PostToolUse
- Move slow checks to PreCommit hooks instead of PostToolUse
- Use background execution if the tool supports it: `${command} &`
- Consider using faster alternatives (e.g., `ruff` instead of `pylint` for Python)

Example optimization:
```json
{
  "PostToolUse": {
    "tool": "Write",
    "pattern": "**/*.py",
    "command": "black ${file}",
    "description": "Fast format only (removed slow pylint)"
  }
}
```

### Common Issue 3
**Problem**: SessionStart hook fails due to missing dependencies
**Solution**:
- Make SessionStart hooks defensive with conditional checks:
```json
{
  "hooks": {
    "SessionStart": {
      "command": "[ -f package.json ] && npm install || echo 'No package.json found'; [ -f requirements.txt ] && pip install -r requirements.txt || echo 'No requirements.txt found'",
      "description": "Install dependencies if files exist"
    }
  }
}
```
- Document required global tools in README
- Use version managers (nvm, pyenv) to ensure consistent tool versions
- Add error handling to prevent session failures

### Common Issue 4
**Problem**: Team member's local overrides in `.claude/settings.local.json` are accidentally committed
**Solution**:
- Ensure `.claude/settings.local.json` is in `.gitignore`
- Run: `git rm --cached .claude/settings.local.json` if already tracked
- Add pre-commit hook to prevent committing local settings:
```bash
#!/bin/bash
if git diff --cached --name-only | grep -q ".claude/settings.local.json"; then
  echo "ERROR: Cannot commit .claude/settings.local.json"
  exit 1
fi
```

### Common Issue 5
**Problem**: Hooks work locally but fail in Claude Code Web's sandboxed environment
**Solution**:
- Verify required tools are available in the Claude Code Web sandbox
- Check `/toolchains` to see preinstalled tools
- Some system commands may not be available - use container-safe alternatives
- Test hooks in a fresh Claude Code Web session before rolling out to team
- Consider using Docker-based hooks if you need specific tool versions

### Common Issue 6
**Problem**: Hook patterns are matching too many or too few files
**Solution**:
- Test glob patterns before deploying: `echo src/**/*.{js,ts}` or use an online glob tester
- Common pattern syntax:
  - `**/*.js` - All JS files in any directory
  - `src/**/*.js` - All JS files under src/
  - `**/*.{js,ts}` - All JS and TS files
  - `**/test/**` - Anything in a test directory
- Use more specific patterns to avoid unnecessary hook execution
- Add negative patterns if your hook system supports them

## Additional Tips

- **Start Simple**: Begin with one or two essential hooks (like a formatter) and add more as your team adapts
- **Performance First**: Keep PostToolUse hooks fast (<2 seconds) to avoid disrupting workflows
- **Document Everything**: Maintain clear documentation about what each hook does and why it exists
- **Version Your Hooks**: Track changes to hooks in git commit messages for team visibility
- **Test Before Deploying**: Test hook configurations in a separate branch before merging to main
- **Provide Escape Hatches**: Allow team members to disable hooks locally when needed (`.claude/settings.local.json`)
- **Regular Audits**: Review hook configurations quarterly to remove unused hooks and optimize performance
- **Combine with CI/CD**: Use hooks for fast feedback, but don't replace CI/CD checks - they serve different purposes
- **Security Reviews**: Have security-focused hooks reviewed by your security team
- **Share Hook Libraries**: Build a collection of reusable hooks for different project types (React, Python, Go, etc.)
- **Use Plugins for Multi-Repo**: If managing multiple repositories, create plugins to share hooks across all of them
- **Monitor Adoption**: Check with team members regularly to ensure hooks are helping, not hindering
- **Context is King**: SessionStart hooks that load team context dramatically improve Claude's understanding of your codebase

**Hook naming conventions:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "description": "Format TypeScript - Prettier",
        "tool": "Write",
        "pattern": "**/*.ts",
        "command": "prettier --write ${file}"
      },
      {
        "description": "Lint TypeScript - ESLint",
        "tool": "Edit",
        "pattern": "**/*.ts",
        "command": "eslint --fix ${file}"
      }
    ]
  }
}
```

Always include descriptive names so team members understand what each hook does.

## Related Articles
- KB-043: How to configure project-specific settings in .claude/settings.json
- KB-042: How to set environment variables in Claude Code Web
- KB-044: How to use the /config command for interactive configuration
- KB-056: How to perform code reviews with Claude Code Web
- KB-054: How to write unit tests with Claude Code Web
- KB-031: How to create a new branch for a specific task

## Sources
- Automate Your AI Workflows with Claude Code Hooks - GitButler Blog - https://blog.gitbutler.com/automate-your-ai-workflows-with-claude-code-hooks (Accessed: November 16, 2025)
- A complete guide to hooks in Claude Code: Automating your development workflow - eesel AI - https://www.eesel.ai/blog/hooks-in-claude-code (Accessed: November 16, 2025)
- Customize Claude Code with plugins - Anthropic - https://claude.com/blog/claude-code-plugins (Accessed: November 16, 2025)
- Claude Code's Hooks Feature: Unleashes New AI Programming Horizons - Medium - https://medium.com/@CherryZhouTech/claude-codes-hooks-feature-unleashes-new-ai-programming-horizons-9d21d07e05fc (Accessed: November 16, 2025)
- GitHub - disler/claude-code-hooks-mastery - https://github.com/disler/claude-code-hooks-mastery (Accessed: November 16, 2025)
- Google Search: "Claude Code Web SessionStart hook team configuration 2025"

---
*This article is part of the Claude Code Web knowledge base*
