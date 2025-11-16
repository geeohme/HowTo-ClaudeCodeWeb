# How to access the sandboxed environment settings

**Article ID**: KB-038
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Claude Code Web provides sandboxed environments with configurable security boundaries for filesystem and network access. This guide explains how to access and modify your sandboxed environment settings, including network permissions, environment variables, and isolation controls. Understanding these settings allows you to balance security with functionality while delegating coding tasks to Claude.

## Prerequisites
- Active Claude Pro or Max subscription (or Team/Enterprise with premium seats)
- Access to Claude Code Web enabled on your account
- At least one repository connected to Claude Code Web
- Basic understanding of environment variables and network domains
- Recommended: KB-001 (Getting started with Claude Code Web)

## Steps

### Step 1: Access Claude Code Web
Navigate to one of the following URLs to access Claude Code Web:
- Direct link: `https://claude.com/code`
- Via main site: Go to `https://claude.ai` and click the **Code** tab

Log in with your Anthropic account credentials if not already authenticated.

### Step 2: Open Your Project Environment
Once you've connected a repository and opened a project:
1. Look for the **current environment name** displayed in the interface (typically shown in the top navigation area)
2. Click on the environment name to open the **environment selector dropdown**
3. You'll see a list of existing environments associated with this project

### Step 3: Access Environment Settings
To modify an existing environment's settings:
1. From the environment selector dropdown, locate the environment you want to configure
2. Click the **settings button** (gear icon) next to the environment name
3. A settings dialog will open displaying the current configuration

Alternatively, to create a new environment with custom settings:
1. Open the environment selector dropdown
2. Click **"Add environment"** at the bottom of the list
3. The new environment dialog will appear

### Step 4: Configure Network Access
In the environment settings dialog, locate the **Network Access** section:

**Network Isolation Options:**
- **Restricted (Default)**: Blocks all network access except approved domains
- **Custom Domains**: Specify allowed domains for the sandbox to access
- **Proxy Configuration**: Advanced users can configure custom proxy settings

To add allowed domains:
```
npm.org
registry.npmjs.org
github.com
api.your-service.com
```

**Best Practice**: Only allow domains that are necessary for your development workflow, such as package registries, API endpoints, or testing services.

### Step 5: Set Environment Variables
In the same settings dialog, locate the **Environment Variables** section:

1. Click **"Add Variable"** to create a new environment variable
2. Enter the variable name (e.g., `NODE_ENV`, `API_KEY`, `DATABASE_URL`)
3. Enter the variable value
4. Repeat for additional variables as needed

**Security Note**: Environment variables set here are stored securely but remain accessible within the sandbox. Avoid storing highly sensitive credentials if possible.

### Step 6: Configure Filesystem Access
While the default filesystem isolation provides read/write access to the current working directory and read access to most system files, you can review these settings:

**Default Filesystem Boundaries:**
- ✅ Read/Write: Current working directory and subdirectories
- ✅ Read-only: Most system files and parent directories
- ❌ Blocked: Sensitive system locations (user home directories, system configs)

**Note**: Filesystem permissions are enforced at the OS level using platform-specific security primitives (macOS Seatbelt, Linux bubblewrap) and cannot be customized through the UI in the standard web interface.

### Step 7: Name and Save Your Environment
1. Enter a descriptive **Environment Name** (e.g., "Production", "Development", "Testing")
2. Review all settings to ensure they match your requirements
3. Click **"Save"** or **"Create"** to apply the configuration

The environment is now active and ready to use with your configured security boundaries.

### Step 8: Manage Repository Access (Optional)
For broader account-level settings:
1. Navigate to `https://claude.ai/settings`
2. Click **"Manage Repositories"**
3. Add or remove repository authorizations as needed

This controls which repositories Claude Code can access at the account level, complementing the per-environment settings.

## Expected Results
After completing these steps, you should see:
- Your custom environment listed in the environment selector
- The environment name displayed as the active environment
- Network requests to allowed domains succeeding without permission prompts
- Environment variables accessible to code running in the sandbox
- Filesystem operations respecting the configured boundaries
- Reduced permission prompts (up to 84% fewer in typical usage)

When Claude Code runs bash commands or executes code, it will operate within these defined boundaries, automatically blocking unauthorized network requests and filesystem modifications.

## Troubleshooting

### Issue 1: Cannot Find Settings Button
**Problem**: The settings/gear icon doesn't appear next to environment names
**Solution**:
- Ensure you're clicking on the environment selector first (the environment name itself)
- Verify you have proper permissions (account owner or admin role required)
- Check that you're using a supported browser (Chrome, Firefox, Safari latest versions)
- Try refreshing the page and logging out/in again

### Issue 2: Network Requests Still Blocked
**Problem**: Network requests fail even after adding domains to allowed list
**Solution**:
- Verify the exact domain format (use `registry.npmjs.org`, not `https://registry.npmjs.org`)
- Check for subdomains - you may need to add both `example.com` and `api.example.com`
- Wait 30-60 seconds after saving for settings to propagate
- Restart the current task or create a new session
- Review console logs for specific blocked domain names

### Issue 3: Environment Variables Not Accessible
**Problem**: Code cannot read environment variables set in settings
**Solution**:
- Confirm variable names use valid format (no spaces, start with letter or underscore)
- Restart the sandbox environment after adding new variables
- Verify variables in code using appropriate syntax: `process.env.VAR_NAME` (Node.js) or `os.environ['VAR_NAME']` (Python)
- Check that you saved the environment settings before running code

### Issue 4: Settings Access Unavailable
**Problem**: Cannot access environment settings or the option is grayed out
**Solution**:
- Verify your subscription tier (Pro, Max, or Team/Enterprise with premium seats required)
- Confirm Claude Code Web is enabled at `claude.ai/settings`
- Check account admin hasn't disabled this feature for your organization
- Ensure you're not in a read-only or demo project

## Additional Tips
- **Start Restrictive**: Begin with minimal network access and add domains as needed based on actual requirements
- **Environment Templates**: Create template environments for common scenarios (development, testing, production) to speed up project setup
- **Security First**: Regularly audit your allowed domains list and remove unused entries
- **Version Control**: Document your environment settings in your project's README so team members can replicate the configuration
- **Monitor Permissions**: Pay attention to permission prompts - they indicate when Claude needs access outside configured boundaries
- **Enterprise Users**: Consult your organization's security team before allowing external network domains
- **Local Secrets**: For highly sensitive credentials, consider using external secret management instead of environment variables

## Related Articles
- KB-001: Getting started with Claude Code Web
- KB-015: How to connect a GitHub repository
- KB-025: How to start a new coding session
- KB-037: How to use the /sandbox command

## Sources
- Anthropic Engineering Blog: Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Claude Code Sandboxing Documentation - https://docs.claude.com/en/docs/claude-code/sandboxing (Accessed: November 16, 2025)
- Claude Code Settings Documentation - https://docs.claude.com/en/docs/claude-code/settings (Accessed: November 16, 2025)
- Claude Code on the Web Official Announcement - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
- InfoQ: Anthropic Adds Sandboxing and Web Access to Claude Code - https://www.infoq.com/news/2025/11/anthropic-claude-code-sandbox/ (Accessed: November 16, 2025)
- Google Search: "Claude Code Web sandbox settings UI interface" (November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
