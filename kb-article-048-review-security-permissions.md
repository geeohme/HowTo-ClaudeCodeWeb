# How to review security permissions before starting a task

**Article ID**: KB-048
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10 minutes

## Overview
Before delegating coding tasks to Claude Code Web, it's essential to review and configure security permissions to protect sensitive files, credentials, and system resources. This guide walks you through the process of auditing your security settings, understanding Claude's access boundaries, and configuring permissions to ensure safe task execution. By reviewing permissions upfront, you can prevent unauthorized access, avoid approval fatigue, and maintain control over critical operations.

## Prerequisites
- Active Claude Pro or Max subscription with Claude Code Web enabled
- At least one repository connected to Claude Code Web
- Basic understanding of filesystem permissions and network security
- Recommended: KB-038 (Access sandboxed environment settings)
- Recommended: KB-047 (Understand credential access)

## Steps

### Step 1: Understand the Default Permission Model

Before starting any task, familiarize yourself with Claude Code's security architecture:

**Permission-Based Approach**
- Claude Code operates as **read-only by default**
- Explicit approval required for modifications and command execution
- Safe operations like `echo` and `cat` are auto-allowed
- Most file writes and bash commands require permission

**Dual-Boundary Protection**
- **Filesystem Isolation**: Access restricted to current working directory
- **Network Isolation**: All connections go through a secure proxy with domain allowlisting

**Key Security Principle**: Sensitive credentials (git tokens, SSH keys, signing keys) never enter the sandbox with Claude Code.

### Step 2: Review Your Current Environment Settings

Access your environment configuration to understand existing permissions:

1. Navigate to `https://claude.com/code`
2. Open your project or repository
3. Click on the **environment name** in the top navigation
4. Select the **settings/gear icon** next to your active environment
5. Review the current configuration

**What to Check:**
```
Network Access Section:
- Which domains are currently allowed?
- Are there any unnecessary or unknown domains?
- Is network isolation enabled?

Environment Variables Section:
- What variables are exposed to Claude?
- Are any sensitive credentials stored here?
- Should any be moved to local-only settings?

Filesystem Access:
- Current working directory access (default)
- Any custom deny patterns configured?
```

### Step 3: Configure Sensitive File Protection

Create or update `.claude/settings.json` to deny Claude access to sensitive files:

```json
{
  "permissions": {
    "deny": [
      "**/.env",
      "**/.env.*",
      "**/.env.local",
      "**/.env.production",
      "**/secrets/**",
      "**/*credentials*",
      "**/*.key",
      "**/*.pem",
      "**/*.p12",
      "**/*.pfx",
      "**/config/secrets.yml",
      "**/.ssh/**",
      "**/id_rsa*",
      "**/private/**",
      "**/*secret*",
      "**/token*"
    ]
  }
}
```

**How It Works:**
- Files matching these patterns become completely invisible to Claude
- Prevents accidental exposure during code review or file operations
- Glob patterns (`**/*`) match across all subdirectories
- Works in addition to sandbox filesystem boundaries

**Best Practice**: Use `.claude/settings.local.json` for machine-specific sensitive patterns that shouldn't be committed to version control.

### Step 4: Audit Network Access Requirements

Review which network domains your task will need:

**Common Development Domains:**
```
Package Registries:
- npm.org
- registry.npmjs.org
- pypi.org
- rubygems.org

Code Hosting:
- github.com
- gitlab.com

API Services:
- api.stripe.com (if testing payments)
- api.sendgrid.com (if testing emails)
```

**Security Checklist:**
- ✅ Only include domains essential for your task
- ✅ Remove domains from previous sessions no longer needed
- ✅ Verify each domain is trusted and necessary
- ✅ Use specific subdomains (e.g., `api.example.com`) rather than wildcards
- ❌ Avoid allowing entire TLDs or overly broad patterns

**To Add/Remove Domains:**
1. Open environment settings
2. Navigate to **Network Access** section
3. Add required domains one per line
4. Remove any unnecessary entries
5. Click **Save**

### Step 5: Verify Credential Access Boundaries

Confirm what credentials Claude can and cannot access:

**Claude CAN Access:**
- ✅ Files within the current working directory
- ✅ Environment variables defined in session settings
- ✅ Git repository contents (via secure proxy)
- ✅ Approved network domains

**Claude CANNOT Access:**
- ❌ Git credentials or signing keys (outside sandbox)
- ❌ SSH keys in `~/.ssh/`
- ❌ System keychains and credential stores
- ❌ GitHub Secrets or deploy keys
- ❌ Files outside working directory (in web version)
- ❌ Files matching `permissions.deny` patterns
- ❌ Non-approved network domains

**Verification Commands:**
```bash
# Check your settings
cat .claude/settings.json
cat .claude/settings.local.json

# Verify git proxy is active (web version handles automatically)
# Your actual git credentials never enter the sandbox
```

### Step 6: Review Task Scope and Permission Requirements

Before starting your task, analyze what permissions it will likely need:

**Low-Risk Tasks** (minimal permissions needed):
- Code review and analysis
- Documentation writing
- Test case generation
- Code formatting and linting
- Read-only exploration

**Medium-Risk Tasks** (standard permissions):
- Adding new features to existing code
- Refactoring within established boundaries
- Running tests
- Installing standard packages
- Creating branches and commits

**High-Risk Tasks** (requires careful review):
- Database schema modifications
- Deleting files or directories
- Modifying authentication/authorization code
- Installing unfamiliar packages
- Making breaking API changes
- Accessing production resources

**Permission Planning:**
```
For your task, determine:
1. Will Claude need to install packages? Which ones?
2. Will Claude modify security-critical files?
3. Does the task require network access? To where?
4. Will Claude delete or rename files?
5. Are there any irreversible operations?
```

### Step 7: Use Plan Mode for High-Risk Tasks

For tasks that involve significant changes, start in plan mode:

**Enable Planning Mode:**
Type in your task description with explicit instructions:
```
"Create a plan for implementing user authentication, but don't
make any changes yet. Show me your proposed approach, which
files you'll modify, and what packages you'll need. Wait for
my approval before implementing."
```

**Benefits of Plan Mode:**
- Review Claude's approach before execution
- Identify permission needs upfront
- Catch potential security issues early
- Approve or modify the plan before changes occur
- Reduces approval fatigue by batching decisions

**Review the Plan:**
```
Claude's response will include:
- Files to be modified
- New files to be created
- Packages to be installed
- Commands to be executed
- Network resources to be accessed

Review each item for security implications before approving.
```

### Step 8: Set Up Approval Preferences

Configure which operations require explicit approval:

**Operations That Should Always Require Approval:**
- File deletions
- Database schema changes
- Modifying `.env` or configuration files
- Installing new packages
- Changing security/authentication code
- Running destructive commands
- Accessing production APIs or databases

**Operations Safe to Auto-Approve:**
- Code formatting
- Adding comments
- Creating test files
- Non-breaking refactors
- Git commits (but not force pushes)

**Communicate Preferences to Claude:**
```
"For this task, always ask for my approval before:
- Deleting any files
- Installing new packages
- Modifying files in the /auth directory
- Making network requests

You can proceed without asking for:
- Creating new test files
- Adding comments to existing code
- Running lint/format commands"
```

### Step 9: Perform a Pre-Task Security Audit

Complete this checklist before starting your task:

```
Security Audit Checklist:

Sensitive Files:
[ ] .env files are in permissions.deny
[ ] API keys and secrets excluded from Claude's access
[ ] SSH keys and certificates protected
[ ] Database credentials not exposed

Network Security:
[ ] Only necessary domains allowed
[ ] No overly broad network patterns
[ ] Production APIs not accessible (unless specifically needed)
[ ] Audit previous session's allowed domains

Environment:
[ ] Sandbox environment settings reviewed
[ ] Environment variables don't contain secrets
[ ] Local-only settings in .claude/settings.local.json
[ ] Team settings in .claude/settings.json

Git and Credentials:
[ ] Understand git operations use secure proxy
[ ] Real credentials never enter sandbox
[ ] Repository permissions appropriate for task
[ ] No sensitive commits in recent history

Task Scope:
[ ] Understand what Claude will access
[ ] Identified high-risk operations
[ ] Plan mode enabled for complex tasks
[ ] Approval requirements communicated
```

### Step 10: Monitor Permissions During Task Execution

After starting your task, actively monitor permission requests:

**Watch For:**
```
Permission Prompts:
[System] Claude is requesting permission to:
- Write to file: src/auth/login.js
- Execute command: npm install axios
- Access domain: api.stripe.com

[Approve] [Reject] [Details]
```

**Best Practices:**
- Don't develop "approval fatigue" - review each prompt carefully
- If you see unexpected permission requests, pause and investigate
- Ask Claude to explain why a permission is needed
- Reject and ask for alternatives if uncomfortable
- Use the "Details" option to understand the full context

**Red Flags:**
```
🚨 Warning Signs:
- Requests to access files in permissions.deny
- Attempts to access parent directories (in non-web versions)
- Unknown network domain requests
- Multiple rapid permission requests
- Requests that don't align with the task description
```

## Expected Results

After reviewing security permissions before starting a task, you should have:

- **Clear understanding** of what Claude can and cannot access
- **Configured deny patterns** protecting sensitive files
- **Reviewed network access** with only necessary domains allowed
- **Verified credential boundaries** ensuring secrets stay protected
- **Established approval workflow** for high-risk operations
- **Pre-task security audit** completed
- **Confidence** to delegate the task safely
- **84% fewer permission prompts** when properly configured (based on Anthropic's internal testing)

**During Task Execution:**
- Permission requests align with expectations
- Sensitive files remain inaccessible
- Network requests only to approved domains
- Git operations work via secure proxy
- No unexpected security prompts

## Troubleshooting

### Common Issue 1
**Problem**: Too many permission prompts interrupting work
**Solution**:
- Use sandboxing to define boundaries upfront instead of approving each action
- Configure allowed domains in environment settings before starting
- Enable plan mode to batch approval decisions
- Set clear approval preferences in task description
- Review environment settings and expand safe boundaries appropriately
- Consider using `.claude/settings.json` to auto-allow common operations

### Common Issue 2
**Problem**: Claude appears to access files it shouldn't
**Solution**:
- Verify `permissions.deny` patterns in `.claude/settings.json` are correctly formatted
- Check glob patterns use `**/*` for subdirectory matching
- Restart Claude Code session to apply new settings
- Verify settings file is in the correct location (`.claude/settings.json` in project root)
- For web version, filesystem access is automatically limited to working directory
- Test by asking Claude to list or read a denied file - it should report file not found

### Common Issue 3
**Problem**: Unsure which permissions to grant for a specific task
**Solution**:
- Start with plan mode to see what Claude proposes
- Ask Claude: "What permissions will you need for this task?"
- Begin with minimal permissions and expand as needed based on prompts
- Research the specific tools/packages involved to understand their requirements
- Err on the side of caution - reject first, then approve after investigation
- Consult similar tasks or documentation for permission guidance

### Common Issue 4
**Problem**: Network requests failing despite adding domains
**Solution**:
- Verify exact domain format (use `api.example.com`, not `https://api.example.com`)
- Check for subdomain requirements - may need both `example.com` and `api.example.com`
- Wait 30-60 seconds after saving settings for changes to propagate
- Restart the current task or create new session
- Review console/activity log for the exact blocked domain name
- Ensure no typos in domain names in allowed list

### Common Issue 5
**Problem**: Concerned about accidentally exposing credentials
**Solution**:
- Use `.claude/settings.local.json` for local secrets (never commit this file)
- Add `.claude/settings.local.json` to `.gitignore`
- Store truly sensitive credentials in external secret management (not environment variables)
- Regularly audit `permissions.deny` to ensure coverage
- In web version, sandbox provides automatic credential isolation
- Remember: git credentials and SSH keys never enter the sandbox in Claude Code Web
- When in doubt, use external secrets manager and provide only necessary access tokens

## Additional Tips

**Before Every Task:**
- Perform a quick 2-minute security review of current settings
- Ask yourself: "What's the worst that could happen if Claude were compromised?"
- Ensure your answer is acceptable given the configured boundaries

**Incremental Permissions:**
- Start restrictive and expand as needed
- Better to approve additional permissions than to clean up after over-permissioning
- Use permission prompts as learning opportunities

**Documentation:**
- Document your permission decisions in task descriptions
- Note any security considerations for future reference
- Share security configurations with team members

**Environment Templates:**
- Create different environments for different risk levels:
  - **Development**: Broader permissions for rapid iteration
  - **Staging**: Moderate restrictions mimicking production constraints
  - **Production**: Highly restrictive, require approval for everything

**Regular Audits:**
- Weekly: Review allowed network domains, remove unused ones
- Monthly: Audit `permissions.deny` patterns for completeness
- Per-task: Quick checklist before starting new work
- After incidents: Review and tighten permissions based on learnings

**Team Coordination:**
- Establish team-wide security baselines in `.claude/settings.json`
- Use `.claude/settings.local.json` for personal preferences
- Document required permissions in README for common tasks
- Share security incidents and learnings with the team

**Sandboxing Benefits:**
- Reduces "approval fatigue" that leads to rubber-stamping permissions
- Creates clear security boundaries decided upfront
- Isolates potential prompt injection attacks
- Enables more autonomous operation within safe limits
- Based on OS-level primitives (macOS Seatbelt, Linux bubblewrap)

## Related Articles
- KB-038: How to access sandboxed environment settings
- KB-047: How to understand what credentials Claude can and cannot access
- KB-039: How to configure network access in Claude Code Web
- KB-045: How to safely provide API keys to Claude
- KB-023: How to approve or reject Claude's suggested changes
- KB-046: How to use the secure proxy for git operations

## Sources
- Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- A deep dive into security for Claude Code in 2025 - https://www.eesel.ai/blog/security-claude-code (Accessed: November 16, 2025)
- Permission Model in Claude Code - https://skywork.ai/blog/permission-model-claude-code-vs-code-jetbrains-cli/ (Accessed: November 16, 2025)
- Anthropic Adds Sandboxing and Web Access to Claude Code - https://www.infoq.com/news/2025/11/anthropic-claude-code-sandbox/ (Accessed: November 16, 2025)
- Google Search: "Claude Code permission review before task security best practices" (November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
