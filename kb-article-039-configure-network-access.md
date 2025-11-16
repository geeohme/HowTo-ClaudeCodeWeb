# How to configure network access (no internet, limited, or full)

**Article ID**: KB-039
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Claude Code Web provides three network access modes to balance functionality with security: no internet, limited (default), or full access. This configuration controls which external domains Claude Code can connect to from its sandboxed environment, allowing you to customize network permissions based on your security requirements and workflow needs.

## Prerequisites
- Access to Claude Code on the web (claude.ai)
- Basic understanding of Claude Code environments
- For related topics, see KB articles on environment setup and security best practices

## Steps

### Step 1: Access Environment Settings

**For a new environment:**
1. Navigate to Claude Code on the web at claude.ai
2. Click to create a new environment
3. A dialog will appear prompting you to configure the environment

**For an existing environment:**
1. Select the environment you want to modify
2. Click the settings button (gear icon) to the right of the environment name
3. The environment settings dialog will open

### Step 2: Choose Your Network Access Level

In the environment settings dialog, you'll see a network access dropdown with three options:

**Limited (Default - Recommended)**
```
Network Access: Limited
```
- Allows access only to pre-approved package repositories
- Default allowlisted domains include:
  - npm registry (npmjs.org)
  - PyPI (pypi.org)
  - Other common package repositories
  - Dozens of vetted dependency sources
- Best for most development workflows
- Balances functionality with security

**No Internet**
```
Network Access: No Internet
```
- Completely blocks all external network connections
- Maximum security for sensitive work
- Use when working with confidential code
- Ideal for air-gapped or highly restricted environments
- Note: Cannot install packages or dependencies

**Full Internet**
```
Network Access: Full Internet
```
- Unrestricted access to all domains
- Required for projects that need external API access
- Use when Claude needs to fetch data from custom sources
- Less secure - use only when necessary

### Step 3: Configure Environment Variables (Optional)

While in the settings dialog, you can also set network-related environment variables:

**For corporate networks:**
```
HTTPS_PROXY=http://your-proxy-server:port
HTTP_PROXY=http://your-proxy-server:port
```

**For custom configurations:**
- Add any additional environment variables needed for your network setup
- These variables persist across sessions in this environment

### Step 4: Save Your Configuration

1. After selecting your preferred network access level
2. Click "Save" or "Create Environment" to apply the settings
3. The environment will now use your configured network access rules

### Step 5: Verify Network Configuration

To confirm your network settings are working:

1. Start a Claude Code session in the configured environment
2. For **Limited** mode: Try installing a package from an allowed repository:
   ```bash
   npm install express
   ```
   This should succeed as npm is allowlisted

3. For **No Internet** mode: Any network request should fail:
   ```bash
   curl https://example.com
   ```
   Expected: Connection refused or network error

4. For **Full Internet** mode: Any network request should succeed:
   ```bash
   curl https://api.github.com
   ```
   Expected: Successful response

## Expected Results

**After configuring network access, you should see:**
- The environment displays your chosen network access level in its settings
- Network requests behave according to your selected mode
- In Limited mode, Claude can install packages from trusted repositories
- In No Internet mode, all external connections are blocked
- In Full Internet mode, all network requests are allowed

**Security indicators:**
- All traffic routes through a secure proxy server
- The proxy enforces your configured domain restrictions
- You may see confirmation prompts when Claude requests access to new domains (in Limited mode)

## Troubleshooting

### Common Issue 1: Package Installation Fails in Limited Mode
**Problem**: Cannot install packages even with Limited network access enabled
**Solution**:
- Verify the package source is in the default allowlist
- Check if your corporate firewall is blocking the connection
- Configure HTTPS_PROXY environment variable if behind a corporate proxy
- Temporarily switch to Full Internet mode if the package source is legitimate but not allowlisted

### Common Issue 2: Cannot Connect to Custom API
**Problem**: Need to access a custom API but Limited mode blocks it
**Solution**:
- Switch to Full Internet mode for this environment
- Alternatively, if security is important, create a separate environment with Full access specifically for API-dependent tasks
- Consider requesting the domain be added to the allowlist if it's a common development tool

### Common Issue 3: Proxy Configuration Not Working
**Problem**: Set HTTPS_PROXY but still cannot connect through corporate proxy
**Solution**:
- Ensure the proxy URL format is correct: `http://proxy-server:port`
- Verify both HTTP_PROXY and HTTPS_PROXY are set
- Check with your IT department for the correct proxy settings
- Some proxies require authentication - include credentials: `http://username:password@proxy-server:port`

### Common Issue 4: Accidentally Selected No Internet Mode
**Problem**: Environment has no network access and cannot install dependencies
**Solution**:
- Go back to environment settings (click settings button next to environment name)
- Change network access from "No Internet" to "Limited" or "Full Internet"
- Save the settings and retry your operation

## Additional Tips

- **Default to Limited**: Start with Limited access for most projects - it provides good security while allowing package installation
- **Separate Environments**: Create different environments for different security needs (e.g., one with No Internet for sensitive work, one with Full access for API development)
- **Network Access is Per-Environment**: Each environment has its own network configuration, allowing you to maintain different security profiles
- **Proxy Caching**: The proxy server may cache some requests for performance
- **Domain Confirmation**: In Limited mode with custom allowlists, you may be prompted to confirm new domain requests - review these carefully
- **Corporate Firewalls**: If working in a corporate environment, coordinate with IT to ensure Claude Code's proxy can reach necessary resources
- **Security Best Practice**: Use the most restrictive network access that still allows your work to proceed
- **Custom Allowlists**: For advanced users, you can create custom allowlists including allowing "*" for everything (equivalent to Full access)

## Related Articles
- KB-001: Getting started with Claude Code Web
- KB-002: Creating and managing environments
- KB-015: Configuring environment variables
- KB-020: Security best practices for Claude Code Web
- KB-025: Working with package managers and dependencies

## Sources
- Claude Code on the web announcement - https://www.anthropic.com/news/claude-code-on-the-web (Accessed: November 16, 2025)
- Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Claude Code Documentation - https://docs.claude.com/en/docs/claude-code/claude-code-on-the-web (Accessed: November 16, 2025)
- Google Search: "Claude Code Web network access configuration 2025"
- Google Search: "Claude Code Web environment settings network access configure"

---
*This article is part of the Claude Code Web knowledge base*
