# How to configure project-specific settings in .claude/settings.json

**Article ID**: KB-043
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Project-specific settings in Claude Code Web allow you to configure behavior, permissions, and preferences that apply to a specific repository or project. By using `.claude/settings.json`, you can share team-wide configurations through version control while maintaining personal overrides with `.claude/settings.local.json`. This ensures consistent development environments across your team while allowing individual customization.

## Prerequisites
- Claude Code Web installed and configured
- A project/repository initialized with Claude Code
- Basic understanding of JSON file format
- Access to edit files in your project directory

## Steps

### Step 1: Understand the Settings Hierarchy

Claude Code uses a three-tier configuration hierarchy:

1. **User settings** (`~/.claude/settings.json`) - Applies to all projects globally
2. **Project settings** (`.claude/settings.json`) - Shared with team, checked into git
3. **Local project settings** (`.claude/settings.local.json`) - Personal preferences, not checked into git

Settings in more specific levels override those in broader levels. For example, project settings override user settings, and local project settings override both.

### Step 2: Create the Project Settings Directory

Navigate to your project root and create the `.claude` directory if it doesn't exist:

```bash
mkdir -p .claude
```

**Note**: The `.claude` directory is typically created automatically when you first use Claude Code in a project, but creating it manually ensures it exists before adding configuration files.

### Step 3: Create or Edit .claude/settings.json

Create the project settings file:

```bash
touch .claude/settings.json
```

Open the file in your editor and add your configuration as valid JSON. Here's a basic template:

```json
{
  "permissions": {
    "allow": [],
    "ask": [],
    "deny": []
  },
  "environmentVariables": {},
  "hooks": {},
  "spinnerTipsEnabled": true
}
```

### Step 4: Configure Permissions

The permissions object controls which tools and commands Claude Code can execute:

- **allow**: Commands that run automatically without prompting
- **ask**: Commands that require user approval before execution
- **deny**: Commands that are blocked entirely

Example configuration:

```json
{
  "permissions": {
    "allow": [
      "read",
      "glob",
      "grep"
    ],
    "ask": [
      "write",
      "edit",
      "bash"
    ],
    "deny": [
      "rm",
      "mv"
    ]
  }
}
```

**Security Tip**: To prevent Claude Code from accessing sensitive files, add patterns to the deny list:

```json
{
  "permissions": {
    "deny": [
      ".env",
      ".env.*",
      "credentials.json",
      "secrets/**",
      "**/*.key",
      "**/*.pem"
    ]
  }
}
```

### Step 5: Set Environment Variables

Configure environment variables that will be automatically set for each Claude Code session:

```json
{
  "environmentVariables": {
    "NODE_ENV": "development",
    "DEBUG": "true",
    "API_BASE_URL": "https://staging.example.com"
  }
}
```

This is particularly useful for:
- Setting consistent development environments across your team
- Configuring non-sensitive project-specific variables
- Defining default behavior for tools and scripts

**Important**: Never store sensitive values (API keys, passwords) in `.claude/settings.json` as it's checked into version control. Use `.claude/settings.local.json` for sensitive local configuration.

### Step 6: Configure Hooks (Optional)

Hooks allow you to run custom commands before or after Claude Code executes tools. For example, to automatically format Python files after Claude modifies them:

```json
{
  "hooks": {
    "afterWrite": {
      "pattern": "**/*.py",
      "command": "black ${file}"
    },
    "afterEdit": {
      "pattern": "**/*.py",
      "command": "black ${file}"
    }
  }
}
```

Common hook use cases:
- Running formatters after code changes
- Executing linters before commits
- Updating documentation after file modifications
- Running tests after specific file changes

### Step 7: Add Project-Specific UI Preferences

Configure UI and interaction preferences:

```json
{
  "spinnerTipsEnabled": false,
  "autoSave": true,
  "confirmBeforeExit": true
}
```

### Step 8: Use Interactive Configuration (Alternative Method)

Instead of manually editing JSON files, you can use the interactive configuration interface:

1. In Claude Code Web, run the `/config` command
2. A tabbed Settings interface will open
3. Navigate through tabs to view and modify settings
4. Changes are automatically saved to the appropriate settings file

This method is helpful for:
- Discovering available configuration options
- Avoiding JSON syntax errors
- Real-time validation of settings

### Step 9: Create Local Overrides (Optional)

For personal preferences that shouldn't be shared with the team, create `.claude/settings.local.json`:

```json
{
  "environmentVariables": {
    "MY_PERSONAL_TOKEN": "abc123"
  },
  "permissions": {
    "allow": [
      "write",
      "edit"
    ]
  }
}
```

Add this file to your `.gitignore`:

```bash
echo ".claude/settings.local.json" >> .gitignore
```

### Step 10: Commit and Share Project Settings

Add the project settings to version control:

```bash
git add .claude/settings.json
git commit -m "Add Claude Code project settings"
git push
```

Your team members will now use the same base configuration when they work on the project.

## Expected Results

After configuring project-specific settings, you should observe:

1. **Consistent Permissions**: Claude Code respects the allow/ask/deny rules you defined
2. **Environment Variables**: Configured variables are available in all Claude Code sessions
3. **Hook Execution**: Custom commands run automatically when specified conditions are met
4. **Team Consistency**: All team members using the repository have the same base configuration
5. **Personal Flexibility**: Local overrides work without affecting team settings

You can verify settings are loaded by:
- Running `/config` to view current active settings
- Checking that environment variables are set with `echo $VARIABLE_NAME`
- Testing that denied commands are blocked

## Troubleshooting

### Common Issue 1
**Problem**: Settings don't seem to take effect after editing `.claude/settings.json`
**Solution**: Restart your Claude Code session. Settings are loaded when Claude Code starts, not dynamically. Exit and restart Claude Code Web to apply changes.

### Common Issue 2
**Problem**: JSON syntax error when editing settings file
**Solution**: Validate your JSON using a linter or online JSON validator. Common issues include:
- Missing commas between properties
- Trailing commas after the last property
- Unquoted keys or string values
- Mismatched brackets or braces

Use this command to validate JSON syntax:
```bash
python3 -m json.tool .claude/settings.json
```

### Common Issue 3
**Problem**: Project settings are being ignored, user settings seem to take precedence
**Solution**: Check that your `.claude/settings.json` is in the correct location (project root). Verify with:
```bash
ls -la .claude/settings.json
```

Remember that local settings (`.claude/settings.local.json`) override project settings, so check if local overrides are interfering.

### Common Issue 4
**Problem**: Sensitive information was accidentally committed in settings.json
**Solution**:
1. Immediately remove the sensitive data from the file
2. Commit the cleaned version
3. Rotate any exposed credentials
4. Use `git filter-branch` or `git filter-repo` to remove from history if needed
5. Move sensitive values to `.claude/settings.local.json`

### Common Issue 5
**Problem**: Environment variables aren't available in bash commands
**Solution**: Ensure the environment variables are properly formatted in the settings file. Environment variables set in `settings.json` should be available, but verify they're being set:
```bash
env | grep YOUR_VARIABLE_NAME
```

## Additional Tips

- **Start Minimal**: Begin with a simple configuration and add complexity as needed. Over-restrictive permissions can hinder productivity.

- **Document Your Settings**: Add a comment block at the top of your settings file (as a JSON string key) explaining the configuration choices for your team.

- **Version Control Strategy**: Always commit `.claude/settings.json` but add `.claude/settings.local.json` to `.gitignore`.

- **Security First**: Never commit sensitive information. Use deny permissions for files containing secrets, API keys, or credentials.

- **Test Before Committing**: Test your settings configuration locally before pushing to ensure it doesn't break workflows for other team members.

- **Use Patterns Wisely**: File patterns in permissions and hooks support glob syntax (`**/*.js`, `src/**`, etc.) for flexible matching.

- **Regular Reviews**: Periodically review your project settings to ensure they still align with team needs and security requirements.

- **Combine with MCP**: Project settings work alongside `.mcp.json` configuration for Model Context Protocol servers, providing comprehensive project setup.

## Related Articles
- KB-044: Managing environment variables in Claude Code Web
- KB-045: Setting up custom hooks and automation
- KB-046: Configuring permissions and security settings
- KB-042: Understanding the .claude directory structure

## Sources
- Claude Code settings - Anthropic - https://docs.claude.com/en/docs/claude-code/settings (Accessed: November 16, 2025)
- A developer's guide to settings.json in Claude Code (2025) - eesel AI - https://www.eesel.ai/blog/settings-json-claude-code (Accessed: November 16, 2025)
- Claude Code Configuration Guide - ClaudeLog - https://claudelog.com/configuration/ (Accessed: November 16, 2025)
- Shipyard Claude Code CLI Cheatsheet - https://shipyard.build/blog/claude-code-cheat-sheet/ (Accessed: November 16, 2025)
- Google Search: "Claude Code Web .claude/settings.json configuration 2025"

---
*This article is part of the Claude Code Web knowledge base*
