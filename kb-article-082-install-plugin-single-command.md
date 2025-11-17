# How to install a plugin with a single command

**Article ID**: KB-082
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Claude Code plugins are pre-packaged bundles of custom slash commands, agents, hooks, and MCP servers that extend functionality with a single installation command. Instead of manually configuring each component, plugins allow you to instantly add complete workflows, tools, and capabilities to your Claude Code environment, making it easy to share and standardize development tools across your team.

## Prerequisites
- Access to Claude Code Web or Claude Code CLI
- Basic understanding of Claude Code slash commands
- A GitHub account (for accessing plugin marketplaces)
- Familiarity with the Claude Code chat interface

## Steps

### Step 1: Add a Plugin Marketplace (First-Time Setup)

Before installing plugins, you need to add at least one plugin marketplace. Marketplaces are GitHub repositories that catalog available plugins.

**Add the official Anthropic marketplace**:

```
/plugin marketplace add anthropics/claude-code
```

**Add popular community marketplaces**:

```
/plugin marketplace add jeremylongshore/claude-code-plugins
```

```
/plugin marketplace add ananddtyagi/claude-code-marketplace
```

You only need to add each marketplace once. After that, you can install any plugin from that marketplace.

### Step 2: Browse Available Plugins

View all available plugins from your added marketplaces:

```
/plugin
```

This opens an interactive menu where you can:
- Browse plugins by category
- View plugin descriptions and features
- See installation commands
- Read plugin documentation

Alternatively, use `/help` to see installed plugins and their commands.

### Step 3: Install a Plugin with a Single Command

The basic syntax for installing a plugin is:

```
/plugin install plugin-name@marketplace-name
```

**Example - Install a DevOps automation pack**:

```
/plugin install devops-automation-pack@claude-code-plugins-plus
```

**Example - Install a code review plugin**:

```
/plugin install accessibility-expert@claude-code-marketplace
```

**Example - Install from Repomix marketplace**:

```
/plugin install repomix-mcp@repomix
```

The `@marketplace-name` specifies which marketplace the plugin comes from. This is required if you have multiple marketplaces with plugins of the same name.

### Step 4: Verify Installation

After installation, confirm the plugin is installed:

```
/help
```

Your new plugin's commands and agents should appear in the help menu. Look for entries like:

- `/new-command` - Description from the plugin
- Agents listed in the agents section
- MCP servers listed in available tools

### Step 5: Use the Installed Plugin

Once installed, you can immediately use any commands, agents, or features provided by the plugin.

**Example - Using a command from the plugin**:

```
/lyra analyze this codebase for performance issues
```

**Example - Invoking an agent from the plugin**:

Ask Claude to use the specialized agent: "Use the accessibility expert agent to review this component."

### Step 6: Restart Claude Code (If Needed)

Most plugins activate immediately, but some may require a restart to fully initialize:

- **Claude Code Web**: Refresh your browser tab or start a new session
- **Claude Code CLI**: Exit and restart the Claude Code CLI

After restarting, run `/help` again to verify all plugin features are available.

## Expected Results

After successfully installing a plugin:

1. **Plugin appears in help menu**: Running `/help` shows the plugin's commands and agents
2. **Commands are immediately usable**: You can invoke slash commands from the plugin
3. **Agents are available**: Specialized agents from the plugin can be referenced in conversations
4. **MCP servers are connected**: If the plugin includes MCP servers, they appear in your MCP server list
5. **Hooks are active**: SessionStart, PostToolUse, or other hooks from the plugin execute automatically

Example successful installation:
```
You: /plugin install project-health-auditor@claude-code-plugins-plus
Claude: Successfully installed project-health-auditor. Run /help to see available commands.

You: /help
[Shows new commands like /audit-dependencies, /security-scan, etc.]
```

## Troubleshooting

### Plugin Not Found Error

**Problem**: "Plugin not found" or "Could not locate plugin in marketplace"
**Solution**:
- Verify you've added the correct marketplace first with `/plugin marketplace add`
- Check the plugin name spelling (it's case-sensitive)
- Ensure you're using the correct marketplace identifier (the part after `@`)
- Browse available plugins with `/plugin` to confirm the exact name
- Try refreshing the marketplace: remove and re-add it

### Commands Not Appearing After Installation

**Problem**: Plugin installed but commands don't show up in `/help`
**Solution**:
- Restart Claude Code Web (refresh browser) or Claude Code CLI
- Wait a few seconds for the plugin to fully initialize
- Check if there are naming conflicts with existing commands
- Try using the namespaced version: `/marketplace:command-name`
- Verify the plugin actually includes commands (some plugins only include agents or hooks)

### Marketplace Already Exists Error

**Problem**: "Marketplace already added" when trying to add a marketplace
**Solution**:
- This is expected behavior - you can skip this step and proceed to install plugins
- To update a marketplace, first remove it: `/plugin marketplace remove marketplace-name`
- Then re-add it to refresh the plugin catalog

### Permission or Authentication Errors

**Problem**: "Permission denied" or authentication errors during installation
**Solution**:
- Ensure you're authenticated with GitHub in Claude Code
- Check that the marketplace repository is public and accessible
- Verify your network connection allows access to GitHub
- For private marketplaces, ensure you have the necessary access permissions

### Plugin Installation Hangs or Times Out

**Problem**: Installation command doesn't complete or times out
**Solution**:
- Check your internet connection
- Try canceling and retrying the installation
- For large plugins with many MCP servers, be patient (can take 30-60 seconds)
- Check Claude Code logs for specific error messages
- Try installing plugins one at a time rather than multiple simultaneously

## Additional Tips

- **Start with trusted marketplaces**: Use Anthropic's official marketplace and well-known community marketplaces first
- **Review before installing**: Use `/plugin` to browse and understand what a plugin does before installing
- **Organize by purpose**: Install plugins relevant to your current project needs rather than everything at once
- **Share with your team**: Project-scoped plugins can be configured in repository settings so teammates get them automatically
- **Update regularly**: Reinstall plugins periodically to get the latest features and bug fixes
- **Uninstall unused plugins**: Keep your environment clean with `/plugin uninstall plugin-name@marketplace` for plugins you no longer need
- **Combine with custom commands**: Plugins work alongside your custom `.claude/commands/` slash commands
- **Check plugin documentation**: Many plugins have README files or documentation with usage examples
- **Export configuration**: Use plugin configurations in your project's `.claude/` directory for team consistency
- **Test in isolation**: When troubleshooting, try installing one plugin at a time to identify conflicts

## Related Articles
- KB-068: How to create your first custom slash command
- KB-069: Write slash commands with markdown files
- KB-075: How to install and configure an MCP server
- KB-076: Connect Claude Code to external MCP servers
- KB-061: Create SessionStart hook for repository initialization

## Sources
- Plugins - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/plugins (Accessed: November 16, 2025)
- Customize Claude Code with plugins - https://www.anthropic.com/news/claude-code-plugins (Accessed: November 16, 2025)
- Claude Code Plugins Plus Repository - https://github.com/jeremylongshore/claude-code-plugins-plus (Accessed: November 16, 2025)
- Claude Code Marketplace Repository - https://github.com/ananddtyagi/claude-code-marketplace (Accessed: November 16, 2025)
- Plugins in the SDK - Claude Docs - https://docs.claude.com/en/docs/agent-sdk/plugins (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
