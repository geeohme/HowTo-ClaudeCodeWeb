# How to configure network restrictions for sensitive projects

**Article ID**: KB-049
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Network restrictions in Claude Code Web provide critical security controls for protecting sensitive projects from unauthorized data exfiltration or malware downloads. By configuring network environments and domain allowlists, you can precisely control which external resources Claude Code can access during task execution, ensuring that confidential code and credentials remain secure within isolated sandbox environments.

## Prerequisites
- Active Claude Pro or Max subscription with access to Claude Code Web
- A GitHub repository connected to Claude Code Web
- Basic understanding of network security concepts
- Familiarity with creating tasks in Claude Code Web

## Steps

### Step 1: Understand Network Environment Options

Claude Code Web offers three network environment configurations when creating tasks:

**No Network Access**
- Completely blocks all external network connections
- Ideal for reviewing highly confidential code or sensitive projects
- Prevents any data exfiltration or external downloads
- Best for security-critical code reviews

**Trusted Network Access**
- Permits access only to pre-approved domains
- Allows essential package repositories (npm, PyPI, Maven, etc.)
- Includes dozens of trusted domains for dependency installation
- Suitable for projects requiring standard development dependencies

**Custom Network Access**
- Allows you to define your own domain allowlist
- Can include wildcard "*" for unrestricted access if needed
- Provides maximum flexibility for specific project requirements
- Recommended for projects with known external dependencies

### Step 2: Select Network Environment When Creating a Task

1. Navigate to https://claude.com/code
2. Select your connected GitHub repository
3. Before entering your task prompt, choose the appropriate network environment from the dropdown menu
4. Consider your project's security requirements:
   - For sensitive/confidential projects → **No Network Access**
   - For standard development tasks → **Trusted Network Access**
   - For projects with specific external APIs → **Custom Network Access**

### Step 3: Configure Custom Network Environments (Optional)

For projects requiring specific external resources:

1. **Create a Custom Environment**
   - In the task creation interface, select "Custom" network environment
   - Click "Configure custom environment" or "Manage environments"

2. **Define Domain Allowlist**
   - Add specific domains Claude Code should access
   - Examples:
     - `api.stripe.com` - For payment integration testing
     - `docs.example.com` - For documentation fetching
     - `cdn.example.com` - For asset downloads
   - Use `*` for unrestricted access (only for non-sensitive projects)

3. **Name and Save Environment**
   - Give your environment a descriptive name (e.g., "Production APIs")
   - Save the configuration for reuse across multiple tasks
   - Review the allowlist to ensure no unnecessary domains are included

### Step 4: Understand the Network Isolation Architecture

Claude Code Web enforces network restrictions through a sophisticated proxy system:

**Proxy Server Architecture**
- All internet access flows through a unix domain socket connected to a proxy server
- The proxy server runs outside the sandbox for security
- Domain restrictions are enforced at the proxy level

**Request Validation Process**
1. Claude Code attempts to access a network resource
2. Request is intercepted by the proxy server
3. Domain is checked against the allowlist
4. If not allowed, request is blocked
5. If domain is new, user confirmation may be required

**Security Benefits**
- Prevents prompt-injection attacks from exfiltrating data
- Blocks malware downloads even if malicious code executes
- Protects SSH keys and credentials that remain outside the sandbox
- Provides audit trail of all network access attempts

### Step 5: Monitor and Approve Network Requests

During task execution, you may receive notifications for network access:

1. **Review Network Request Notifications**
   - Claude Code will notify you if it attempts to access a new domain
   - Review the domain name and purpose
   - Consider whether access is necessary for the task

2. **Grant or Deny Access**
   - Click "Allow" to permit one-time or permanent access
   - Click "Deny" to block the request
   - Choose "Allow for this session" for temporary access
   - Select "Always allow this domain" to add to your allowlist

3. **Update Allowlist as Needed**
   - Regularly review approved domains
   - Remove domains no longer required
   - Keep the allowlist minimal for security

### Step 6: Implement Security Best Practices

For maximum protection of sensitive projects:

1. **Default to No Network Access**
   - Start with the most restrictive setting
   - Only relax restrictions when absolutely necessary
   - Document why network access is required

2. **Use Minimal Allowlists**
   - Only include domains essential for the task
   - Avoid wildcard "*" for sensitive projects
   - Review and audit allowlists regularly

3. **Separate Environments by Sensitivity**
   - Create different environments for different project types
   - Use "No Network" for security reviews
   - Use "Trusted Network" for standard development
   - Use "Custom" only when specific APIs are required

4. **Protect Sensitive Files**
   - Note that `.env` files are automatically blocked
   - Ensure credentials are not committed to repositories
   - Use environment variables for secrets (they stay outside the sandbox)

## Expected Results

When properly configured:

- Tasks execute in isolated sandboxes with enforced network restrictions
- Unauthorized network access is blocked automatically
- You receive notifications for any unexpected network requests
- Sensitive data cannot be exfiltrated even if prompt injection occurs
- External malware cannot be downloaded into the sandbox environment
- You maintain full audit visibility of network activity
- Projects can still access approved dependencies and resources

**Success Indicators:**
- Task completes without unexpected network permission prompts
- All necessary package installations succeed
- No unauthorized domains are accessed
- Sandbox remains isolated from local network and sensitive files

## Troubleshooting

### Common Issue 1: Package Installation Failures
**Problem**: npm install or pip install fails with network errors

**Solution**:
- Verify you selected "Trusted Network Access" or "Custom" environment
- If using Custom, add these common package repositories to your allowlist:
  - `registry.npmjs.org` (npm)
  - `pypi.org` and `files.pythonhosted.org` (pip)
  - `repo.maven.apache.org` (Maven)
- Re-run the task with updated network settings

### Common Issue 2: API Access Blocked
**Problem**: Task cannot access required external APIs

**Solution**:
- Switch from "Trusted Network Access" to "Custom" environment
- Add the specific API domain to your allowlist (e.g., `api.github.com`)
- Ensure you're adding the base domain, not full URLs
- Verify the domain is spelled correctly without http:// or https://

### Common Issue 3: Too Many Permission Prompts
**Problem**: Constant notifications asking to approve network requests

**Solution**:
- Create a custom environment with pre-approved domains
- Use the "Always allow this domain" option for trusted resources
- For CLI version, use `/permissions` command to manage allowlists
- Review if "Trusted Network Access" would be more appropriate

### Common Issue 4: Unable to Clone or Access Repository
**Problem**: Task cannot access GitHub repository or fetch code

**Solution**:
- This should not happen as Git operations use a secure proxy
- Verify repository permissions in GitHub settings
- Check that the repository is properly connected to Claude Code Web
- Ensure you have appropriate access rights to the repository
- Try disconnecting and reconnecting the repository

## Additional Tips

- **Environment Reuse**: Create named environments for different project types to save configuration time
- **Audit Logs**: Review network access logs periodically to identify unusual patterns or unauthorized attempts
- **Testing First**: Test network configuration with a non-sensitive task before applying to sensitive projects
- **Documentation**: Document your network environment choices in project README files for team awareness
- **Progressive Restriction**: Start with more permissive settings during development, then tighten for production code reviews
- **CLI Alternative**: For local development, Claude Code CLI provides more granular control via settings.json configuration
- **Corporate Proxies**: Claude Code respects HTTP_PROXY and HTTPS_PROXY environment variables for corporate networks
- **No SOCKS Support**: Note that SOCKS proxies are not currently supported in Claude Code Web

## Related Articles
- KB-047: How to handle sensitive data and credentials in Claude Code Web
- KB-048: How to configure repository access permissions
- KB-050: How to use sandbox mode for secure code execution
- KB-051: How to audit and review Claude Code activity logs

## Sources
- Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Claude Code Security Documentation - https://docs.claude.com/en/docs/claude-code/security (Accessed: November 16, 2025)
- Anthropic Adds Sandboxing and Web Access to Claude Code - https://www.infoq.com/news/2025/11/anthropic-claude-code-sandbox/ (Accessed: November 16, 2025)
- A deep dive into security for Claude Code in 2025 - https://www.eesel.ai/blog/security-claude-code (Accessed: November 16, 2025)
- Claude Code Security Best Practices - https://www.backslash.security/blog/claude-code-security-best-practices (Accessed: November 16, 2025)
- Understanding Claude Code Permissions and Security Settings - https://www.petefreitag.com/blog/claude-code-permissions/ (Accessed: November 16, 2025)
- Corporate proxy configuration - https://docs.anthropic.com/en/docs/claude-code/corporate-proxy (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
