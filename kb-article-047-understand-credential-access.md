# How to understand what credentials Claude can and cannot access

**Article ID**: KB-047
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10 minutes

## Overview
Understanding what credentials and sensitive information Claude Code can and cannot access is critical for maintaining security when using the platform. Claude Code Web implements a sophisticated sandboxing architecture with dual isolation boundaries (filesystem and network) to protect your credentials. This guide explains the security model, what Claude can see, and how to further restrict access to sensitive data.

## Prerequisites
- Active Claude Code Web session
- Basic understanding of environment variables and filesystem permissions
- Familiarity with git authentication concepts

## Steps

### Step 1: Understand the Sandbox Security Architecture

Claude Code Web uses a dual-boundary protection system to isolate credentials:

**Filesystem Isolation**
- Claude has access only to the current working directory
- The system blocks modification of any files outside the working directory
- This prevents access to sensitive system files even during prompt injection attacks

**Network Isolation**
- All internet access runs through a Unix domain socket connected to a proxy server outside the sandbox
- The proxy enforces restrictions on which domains processes can connect to
- User confirmation is required for new domain requests

**Key Security Principle**: Sensitive credentials (such as git credentials or signing keys) are never inside the sandbox with Claude Code.

### Step 2: Learn What Claude CAN Access

Claude Code Web has access to:

**Files and Directories**
- All files within the current working directory
- Files that your user account has permission to read
- Project files and source code in the repository

**Environment Variables**
- Environment variables set in the session
- Variables defined in `.claude/settings.json` or `.claude/settings.local.json`
- The `ANTHROPIC_API_KEY` environment variable (if set)

**Git Operations (Through Proxy)**
- Reading repository contents
- Creating branches
- Committing changes
- Commenting on issues and pull requests

**Network Access**
- Approved domains only (requires user confirmation for new domains)
- All requests pass through the secure proxy server

### Step 3: Learn What Claude CANNOT Access

Claude Code is explicitly prevented from accessing:

**Credentials and Keys**
- Git credentials or signing keys (never inside the sandbox)
- SSH keys stored in `~/.ssh/`
- Authentication tokens stored in system keychains
- GitHub Secrets, deploy keys, or repository settings

**Filesystem Restrictions**
- Files outside the current working directory (in web version)
- Files matching patterns in `permissions.deny` settings
- System files and directories

**Network Restrictions**
- Domains not explicitly approved by the user
- Direct network connections (all must go through proxy)

### Step 4: Configure Protection for Sensitive Files

Create or edit your `.claude/settings.json` file to deny access to sensitive files:

```json
{
  "permissions": {
    "deny": [
      "**/.env",
      "**/.env.*",
      "**/secrets/**",
      "**/*credentials*",
      "**/*.key",
      "**/*.pem",
      "**/config/secrets.yml"
    ]
  }
}
```

This configuration makes these files completely invisible to Claude Code, preventing accidental exposure.

### Step 5: Understand Git Authentication via Proxy

Claude Code Web uses a custom proxy service for git operations:

**How It Works**
1. The git client inside the sandbox authenticates using a custom-built scoped credential
2. The proxy server (outside the sandbox) verifies the credential and git interaction
3. The proxy attaches the appropriate authentication token
4. The request is sent to GitHub with proper credentials

**Security Benefits**
- Real credentials never enter the sandbox
- Scoped credentials have limited permissions
- Even if sandbox is compromised, attackers cannot steal authentication tokens

### Step 6: Manage Environment Variables Safely

**Best Practices for API Keys**

Use `.claude/settings.local.json` for local-only credentials (not checked into source control):

```json
{
  "env": {
    "API_KEY": "${SECRET_API_KEY}"
  }
}
```

**Priority Order**
- `ANTHROPIC_API_KEY` environment variable takes priority over authenticated subscriptions
- Setting this variable will result in API usage charges instead of using subscription credits
- Only set this if you specifically want to use an API key for billing

**Team Deployment**
- Use `.claude/settings.json` for team-wide environment variables
- Store sensitive values in external secret management systems
- Use environment variable expansion in `.mcp.json` files for MCP server configurations

### Step 7: Verify Your Security Configuration

Check what Claude can access by examining permissions:

```bash
# View your current settings
cat ~/.claude/settings.json

# Check project-specific settings
cat .claude/settings.json
cat .claude/settings.local.json
```

**Test Access Restrictions**
1. Try asking Claude to read a file in your `permissions.deny` list
2. Verify Claude cannot access files outside the working directory
3. Confirm new network domain requests require your approval

## Expected Results

After implementing these security measures, you should observe:

- Claude Code can only access files in the current working directory
- Files matching `permissions.deny` patterns are invisible to Claude
- Git operations work seamlessly without exposing credentials
- Network requests to new domains require explicit approval
- Sensitive credentials remain protected outside the sandbox

## Troubleshooting

### Common Issue 1
**Problem**: Claude appears to have access to files it shouldn't see
**Solution**:
- Verify your `permissions.deny` patterns are correctly formatted
- Check that the settings file is in the correct location (`.claude/settings.json`)
- Restart your Claude Code session to apply new settings
- Use glob patterns like `**/.env` to match files in any subdirectory

### Common Issue 2
**Problem**: Git operations fail with authentication errors
**Solution**:
- Ensure you've authorized Claude Code to access your GitHub account
- Check that the proxy service is functioning (this is handled automatically)
- Verify you have the necessary permissions on the repository
- Note that Claude Code has limited permissions by design (cannot access GitHub Secrets or deploy keys)

### Common Issue 3
**Problem**: Environment variables aren't available to Claude
**Solution**:
- Verify variables are defined in `.claude/settings.json` or `.claude/settings.local.json`
- Check the JSON syntax is valid
- Restart the Claude Code session after modifying settings
- Remember that `ANTHROPIC_API_KEY` will override subscription authentication

### Common Issue 4
**Problem**: Concerned about Claude accessing sensitive directories
**Solution**:
- In Claude Code Web, filesystem access is automatically limited to the working directory
- For desktop/CLI versions, use `permissions.deny` to block sensitive paths like `~/.ssh/`, `~/Secrets/`
- For maximum security, consider running Claude Code in a Docker container with restricted filesystem access
- Create a dedicated user account with limited permissions for running Claude Code

## Additional Tips

- **Regularly Review Permissions**: Periodically audit your `permissions.deny` settings to ensure all sensitive files are protected
- **Use Project-Specific Settings**: Keep team settings in `.claude/settings.json` and personal/sensitive settings in `.claude/settings.local.json`
- **Understand the Difference**: Claude Code Web has stricter isolation than desktop/CLI versions due to sandboxing
- **Credential Rotation**: Even with protections in place, regularly rotate API keys and tokens as a security best practice
- **Monitor Git Activity**: Review the git operations Claude performs to ensure they align with your expectations
- **Network Domain Awareness**: Pay attention to network domain approval requests—only approve domains you recognize and trust
- **Sandbox Benefits**: The dual-isolation approach ensures even a successful prompt injection is fully isolated and cannot impact overall user security

## Related Articles
- KB-001: Getting started with Claude Code Web
- KB-010: Understanding sandboxed environments
- KB-020: Working with git repositories in Claude Code Web

## Sources
- Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Claude Code on the web - https://www.anthropic.com/news/claude-code-on-the-web (Accessed: November 16, 2025)
- Managing API Key Environment Variables in Claude Code - https://support.claude.com/en/articles/12304248-managing-api-key-environment-variables-in-claude-code (Accessed: November 16, 2025)
- A complete guide to Claude Code permissions - https://www.eesel.ai/blog/claude-code-permissions (Accessed: November 16, 2025)
- A complete guide to environment variables in Claude Code - https://www.eesel.ai/blog/environment-variables-claude-code (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
