# How to use the /config command to open settings interface

**Article ID**: KB-044
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5 minutes

## Overview
Claude Code Web provides a settings interface that allows you to configure various aspects of your coding environment, including tool permissions, network access, environment variables, and other preferences. Accessing these settings enables you to customize how Claude Code interacts with your repositories and external services.

## Prerequisites
- Active Claude Pro or Max subscription
- Access to Claude Code Web at `https://claude.com/code`
- At least one GitHub repository connected (recommended)
- Basic understanding of Claude Code Web interface (KB-001, KB-004)

## Steps

### Step 1: Access Claude Code Web

Navigate to Claude Code Web and ensure you're logged in:

```
https://claude.com/code
```

You should see your Claude Code Web dashboard with any connected repositories and active or previous sessions.

### Step 2: Locate the Settings Interface

There are two primary ways to access the settings interface in Claude Code Web:

**Method 1: Using the Settings Icon (GUI)**
1. Look for the gear icon (⚙️) in the sidebar or navigation area
2. Click the gear icon to open the settings panel
3. The settings interface will open, displaying available configuration options

**Method 2: Using the Chat Interface (if available)**
1. In an active coding session, type `/config` in the chat input
2. Press Enter to execute the command
3. This should open the tabbed settings interface

**Note**: The primary method for accessing settings in Claude Code Web is through the gear icon in the interface. The `/config` command is more commonly associated with the CLI version of Claude Code.

### Step 3: Navigate the Settings Tabs

Once the settings interface is open, you'll see multiple configuration categories:

**Tool Settings**
- Enable or disable specific tools Claude Code can use
- Configure permissions for file operations, web access, and terminal commands
- Set up tool-specific preferences

**Environment Configuration**
- Add new environments for different project needs
- Specify environment names
- Configure network access levels
- Define environment variables

**Network Access Settings**
- Configure network restrictions (fully locked down, restricted, or custom)
- Create allow-lists of domains Claude Code can access
- Set up custom domain configurations for API access or external services

**Permissions**
- Review and modify permission settings for different operations
- Set allow/ask/deny rules for specific actions
- Configure sandbox restrictions

### Step 4: Configure Your Preferred Settings

To modify a setting:

1. **Select the appropriate tab** - Choose the category you want to configure
2. **Review current settings** - Check existing configurations before making changes
3. **Make changes** - Toggle switches, enter values, or select options as needed
4. **Understand the impact** - Note that some changes may affect ongoing or future sessions

**Common Settings to Configure:**

**Tool Permissions:**
```
Enable only the tools you need:
- File read/write operations
- Terminal/bash commands
- Web fetch capabilities
- Git operations
```

**Environment Variables:**
```
Add environment-specific variables:
- API keys (use with caution)
- Configuration values
- Project-specific settings
```

**Network Configuration:**
```
Specify allowed domains:
- api.example.com
- cdn.example.com
- Custom service endpoints
```

### Step 5: Save Your Settings

After making changes:

1. Look for a "Save" or "Apply" button (if present)
2. Some settings may save automatically
3. Close the settings panel when finished
4. Your preferences will be applied to new sessions

### Step 6: Verify Settings Are Applied

To confirm your settings are working:

1. Start a new coding task or session
2. Observe Claude's behavior with respect to your configured settings
3. Check that tool restrictions are being enforced
4. Verify network access is limited to allowed domains
5. Confirm environment variables are available when needed

## Expected Results

After successfully accessing and configuring settings, you should:
- See the settings interface with multiple configuration tabs
- Be able to toggle tool permissions on/off
- Have the ability to add custom environments with specific variables
- Control network access and domain restrictions
- See your settings persist across sessions
- Notice Claude respecting your configured permissions and restrictions

## Troubleshooting

### Common Issue 1
**Problem**: Cannot find the settings icon or /config command doesn't work
**Solution**:
- Look carefully for a gear icon (⚙️) in the sidebar, top navigation, or user menu area
- Try clicking your profile or account area to see if settings are nested there
- If using /config in chat, ensure you're in an active coding session
- The web interface primarily uses GUI controls rather than slash commands
- Refresh the page if the interface isn't loading properly

### Common Issue 2
**Problem**: Settings panel is empty or won't load
**Solution**:
- Refresh the browser page and try accessing settings again
- Clear your browser cache and cookies for claude.com
- Try using a different browser (Chrome, Firefox, Safari recommended)
- Ensure you have an active Pro or Max subscription
- Check your internet connection
- Try logging out and logging back in

### Common Issue 3
**Problem**: Changes to settings don't seem to take effect
**Solution**:
- Ensure you clicked "Save" or "Apply" if such a button exists
- Close the settings panel and reopen it to verify changes were saved
- Start a new coding session rather than continuing an existing one
- Some settings may only apply to newly created sessions, not ongoing ones
- Try refreshing the entire page after saving settings

### Common Issue 4
**Problem**: Don't see all the settings options mentioned
**Solution**:
- Settings availability may vary by subscription tier (Pro vs Max)
- Some settings might be in beta or rolling out gradually
- Scroll down in the settings panel as options may extend below the fold
- Look for tabs or categories that organize different setting types
- Check if you need to enable a feature flag or experimental feature

### Common Issue 5
**Problem**: Network or environment settings not working as expected
**Solution**:
- Verify your domain allow-list syntax is correct (no typos)
- Environment variables may need specific formatting
- Network restrictions apply per session - create a new session to test
- Check that the environment you configured is selected for your current task
- Some network operations may be restricted regardless of settings for security

## Additional Tips

### Security Best Practices
- **Minimize tool permissions** - Only enable tools Claude actually needs for your projects
- **Be careful with API keys** - Avoid storing sensitive credentials in environment variables when possible
- **Use network restrictions** - Limit network access to only required domains
- **Review regularly** - Periodically audit your settings to ensure they're still appropriate

### Efficiency Tips
- **Create multiple environments** - Set up different configurations for different project types (e.g., frontend, backend, testing)
- **Document your settings** - Keep notes about why you configured specific settings
- **Test with simple tasks** - After changing settings, run a simple task to verify everything works
- **Start restrictive** - Begin with more restrictive settings and relax them as needed

### Understanding Settings Hierarchy
Claude Code may have different levels of settings:
- **User settings** - Apply globally to all your projects
- **Project settings** - Specific to individual repositories (if using CLI with `.claude/settings.json`)
- **Session settings** - May be configurable per individual coding session

The web interface primarily manages user-level settings, while project-specific configurations are typically managed through repository files.

### Common Use Cases

**For Public API Development:**
```
Enable: File operations, terminal commands, web fetch
Network: Specific API domains only
Environment: API documentation URLs
```

**For Private/Secure Projects:**
```
Enable: File operations only
Network: Fully locked down
Environment: Minimal or no variables
```

**For Testing/Experimentation:**
```
Enable: All tools
Network: Restricted allow-list
Environment: Test-specific variables
```

## Related Articles
- KB-001: How to access Claude Code Web
- KB-004: How to connect your first GitHub repository to Claude Code Web
- KB-006: How to create your first coding task in Claude Code Web
- KB-021: How to provide additional context to Claude during a task

## Sources
- Claude Code Web Settings and Configuration - Search: "Claude Code Web settings interface gear icon 2025" (Accessed: November 16, 2025)
- Claude Code on the Web Documentation - Search: "Claude Code on the web interface features settings October 2025" (Accessed: November 16, 2025)
- Claude Code Environment Configuration - Search: "Claude Code Web environment configuration network access 2025" (Accessed: November 16, 2025)
- Claude Code Tools and Permissions - Search: "Claude Code Web tool settings preferences configure 2025" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
