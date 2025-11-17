# How to configure agent behavior and permissions

**Article ID**: KB-087
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview
Configuring agent behavior and permissions in Claude Code Web allows you to control how autonomously Claude operates, which tools require approval, and what security boundaries are enforced. By customizing permission modes, settings files, and allowed-tools configurations, you can balance productivity with safety, reducing approval fatigue while maintaining control over critical operations. This advanced guide covers the complete permission system architecture and configuration strategies.

## Prerequisites
- Active Claude Pro or Max subscription with Claude Code Web access
- At least one repository connected to Claude Code Web
- Basic understanding of JSON file format
- Familiarity with filesystem and command-line concepts
- Recommended: KB-043 (Configure project-specific settings)
- Recommended: KB-048 (Review security permissions)

## Steps

### Step 1: Understand Permission Modes

Claude Code Web offers multiple permission modes that control agent behavior:

**Available Permission Modes:**

1. **default** - Standard permission checking
   - Prompts for file modifications and command execution
   - Safe operations (read, glob, grep) run automatically
   - Balanced approach for typical development

2. **acceptEdits** - Auto-approve most file edits
   - Automatically approves file writes and edits
   - Reduces interruptions during active development
   - Still prompts for potentially dangerous operations
   - Ideal for trusted, well-scoped tasks

3. **plan** - Planning mode with restricted actions
   - Claude creates execution plans without making changes
   - Review proposed approach before implementation
   - Useful for complex or high-risk tasks
   - Enables informed decision-making

4. **dangerously-skip-permissions** - Bypass all permission checks
   - The "nuclear option" for completely uninterrupted sessions
   - Use only for trusted code in isolated environments
   - Maximum productivity but minimal safety guardrails
   - Not recommended for production code or sensitive projects

**Switching Permission Modes:**
- During a session: Press `Shift+Tab` to cycle through modes (CLI version)
- In settings: Configure default mode in `settings.json`
- Per task: Specify mode when starting tasks

### Step 2: Configure Settings Hierarchy

Claude Code uses a three-tier hierarchical settings system:

**Settings File Locations:**

1. **User Settings** - `~/.claude/settings.json`
   - Applies to all projects globally
   - Personal defaults across your entire system
   - Lowest priority in hierarchy

2. **Project Settings** - `.claude/settings.json`
   - Repository-specific configuration
   - Shared with team via version control
   - Overrides user settings

3. **Local Project Settings** - `.claude/settings.local.json`
   - Personal preferences for specific project
   - Not committed to version control (add to `.gitignore`)
   - Highest priority, overrides all other settings

**Priority Order:**
```
Local Project (.claude/settings.local.json)
    ↓ overrides
Project (.claude/settings.json)
    ↓ overrides
User (~/.claude/settings.json)
```

### Step 3: Access Interactive Configuration Interface

The `/config` command provides a user-friendly interface for managing settings:

**Using the Config Command:**

1. In Claude Code Web, type `/config` in the chat
2. A tabbed Settings interface opens
3. Navigate through available tabs:
   - **Permissions** - Tool and command access control
   - **Environment** - Variables and runtime configuration
   - **Behavior** - Agent operation preferences
   - **Network** - Domain allowlists and restrictions
4. Modify settings using form controls
5. Changes are automatically saved to the appropriate settings file

**Benefits:**
- Discover available configuration options
- Avoid JSON syntax errors
- Real-time validation and feedback
- Visual representation of current settings
- Guided configuration experience

### Step 4: Configure Permission Rules

Define which tools and operations require approval:

**Permission Categories:**

Create or edit `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep"
    ],
    "ask": [
      "Write",
      "Edit",
      "Bash"
    ],
    "deny": [
      "**/.env",
      "**/.env.*",
      "**/secrets/**",
      "**/*.key",
      "**/*.pem"
    ]
  }
}
```

**Permission Rule Types:**

- **allow** - Execute automatically without prompting
- **ask** - Require user approval before execution
- **deny** - Block completely, files become invisible to Claude

**File Pattern Syntax:**
- Use glob patterns: `**/*.js`, `src/**`, `*.test.ts`
- Wildcards match across directories: `**/secrets/**`
- Specific files: `config/production.yml`
- Extensions: `**/*.key`, `**/*.pem`

### Step 5: Configure Allowed-Tools with Scoped Permissions

Fine-grained control using tool-specific permissions with wildcards:

**Scoped Permission Syntax:**

```json
{
  "allowed-tools": "Bash(git:*),Bash(npm:install),Bash(npm:test),Read,Write(src/**),Edit(src/**)"
}
```

**Tool Scoping Examples:**

**Bash Command Scoping:**
```json
"Bash(git:*)"           // All git commands allowed
"Bash(npm:install)"     // Only npm install
"Bash(npm:test)"        // Only npm test
"Bash(docker:ps)"       // Only docker ps
```

**File Operation Scoping:**
```json
"Write(src/**)"         // Write only in src/ directory
"Edit(tests/**)"        // Edit only in tests/ directory
"Read(**/*.md)"         // Read only markdown files
```

**Complete Configuration Example:**

```json
{
  "permissions": {
    "mode": "default"
  },
  "allowed-tools": "Bash(git:status),Bash(git:diff),Bash(git:log),Bash(npm:*),Read,Glob,Grep,Write(src/**),Edit(src/**),Write(tests/**),Edit(tests/**)"
}
```

**Benefits of Scoped Permissions:**
- Allow specific commands without broad tool access
- Prevent dangerous operations (e.g., `rm`, `dd`)
- Restrict file operations to specific directories
- Create safe boundaries for autonomous operation
- Reduce approval prompts for trusted operations

### Step 6: Set Default Permission Mode

Configure the default mode for new sessions:

**In settings.json:**

```json
{
  "permissions": {
    "mode": "acceptEdits",
    "ask": ["Bash"],
    "deny": ["**/.env", "**/*.key"]
  },
  "defaultPermissionMode": "acceptEdits"
}
```

**Mode Selection Guidelines:**

**Use `default` when:**
- Working on unfamiliar codebases
- Making structural changes
- Modifying critical infrastructure code
- Learning Claude Code's behavior

**Use `acceptEdits` when:**
- Actively developing well-understood features
- Iterating quickly on implementation
- Working in sandboxed development environment
- Trust the task scope and boundaries

**Use `plan` when:**
- Performing complex refactoring
- Making security-critical changes
- Need to review approach before execution
- Want to batch approval decisions
- Working on production code

**Use `dangerously-skip-permissions` when:**
- Fully trusted, isolated environment
- Maximum speed required (demos, experiments)
- Complete understanding of task scope
- Not working with sensitive data

### Step 7: Configure Agent Behavior Preferences

Customize how Claude operates and communicates:

```json
{
  "spinnerTipsEnabled": false,
  "autoSave": true,
  "confirmBeforeExit": true,
  "verboseMode": false,
  "showToolUsage": true,
  "compactOutput": false
}
```

**Behavior Options:**

- **spinnerTipsEnabled** - Show helpful tips during processing
- **autoSave** - Automatically save file changes
- **confirmBeforeExit** - Prompt before closing sessions
- **verboseMode** - Detailed explanations of actions
- **showToolUsage** - Display which tools Claude is using
- **compactOutput** - Minimize output verbosity

**Communication Style Configuration:**

```json
{
  "agentBehavior": {
    "explainDecisions": true,
    "askBeforeDestructive": true,
    "suggestAlternatives": true,
    "provideContext": true
  }
}
```

### Step 8: Implement Security-Focused Permissions

For sensitive projects, implement strict security boundaries:

**Security-First Configuration:**

```json
{
  "permissions": {
    "mode": "default",
    "allow": [
      "Read",
      "Glob",
      "Grep"
    ],
    "ask": [
      "Write",
      "Edit",
      "Bash",
      "NotebookEdit"
    ],
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
      "**/token*",
      "**/.git/config",
      "**/aws/**",
      "**/gcp/**"
    ]
  },
  "allowed-tools": "Bash(git:status),Bash(git:diff),Bash(git:log),Read,Glob,Grep",
  "networkAccess": "restricted"
}
```

**What This Protects:**
- Environment variables and secrets files
- Private keys and certificates
- Credential files and tokens
- Cloud provider configurations
- Git credentials
- SSH keys

### Step 9: Create Environment-Specific Configurations

Different environments require different permission strategies:

**Development Environment (.claude/settings.json):**

```json
{
  "permissions": {
    "mode": "acceptEdits"
  },
  "allowed-tools": "Bash(git:*),Bash(npm:*),Bash(python:*),Read,Write(src/**),Edit(src/**),Write(tests/**),Edit(tests/**)",
  "environmentVariables": {
    "NODE_ENV": "development",
    "DEBUG": "true"
  }
}
```

**Production Review Environment:**

```json
{
  "permissions": {
    "mode": "plan"
  },
  "allowed-tools": "Read,Glob,Grep,Bash(git:status),Bash(git:diff)",
  "networkAccess": "none",
  "environmentVariables": {
    "NODE_ENV": "production"
  }
}
```

**Testing/CI Environment:**

```json
{
  "permissions": {
    "mode": "default"
  },
  "allowed-tools": "Bash(npm:test),Bash(npm:run),Bash(pytest),Read,Write(coverage/**)",
  "networkAccess": "trusted"
}
```

### Step 10: Monitor and Audit Agent Behavior

Track and review how Claude operates under your configuration:

**Enable Audit Logging:**

```json
{
  "logging": {
    "enabled": true,
    "level": "info",
    "logToolUsage": true,
    "logPermissionDecisions": true,
    "logPath": ".claude/logs/audit.log"
  }
}
```

**Review Permission Decisions:**

Check `.claude/logs/audit.log` for:
- Which permissions were requested
- What was approved/denied
- Tool usage patterns
- Unusual or unexpected access attempts

**Monitoring During Sessions:**

1. Pay attention to permission prompts
2. Note which operations require frequent approval
3. Identify patterns suggesting configuration adjustments
4. Watch for unexpected tool usage
5. Review post-session summaries

**Iterative Improvement:**

Based on monitoring:
- Add frequently-approved operations to `allow`
- Move rarely-used permissions to `ask`
- Identify new patterns for `deny` rules
- Adjust scoped permissions for better balance
- Refine environment-specific configurations

## Expected Results

After configuring agent behavior and permissions, you should experience:

**Productivity Improvements:**
- Up to 84% fewer permission prompts (with proper sandboxing)
- Faster task execution with appropriate auto-approvals
- Reduced interruptions during development flow
- More autonomous agent operation within safe boundaries

**Security Enhancements:**
- Sensitive files completely invisible to Claude
- Critical operations require explicit approval
- Clear audit trail of all agent actions
- Predictable behavior within defined boundaries
- Protection against prompt injection attacks

**Operational Benefits:**
- Consistent behavior across team members (via `.claude/settings.json`)
- Personal customization (via `.claude/settings.local.json`)
- Environment-specific configurations for different contexts
- Fine-grained control over tool and command access
- Balanced autonomy and oversight

**Behavioral Expectations:**
- Claude respects allow/ask/deny rules consistently
- Permission mode affects approval frequency appropriately
- Scoped permissions limit operations to specified contexts
- Configuration changes apply in new sessions
- Settings hierarchy works as expected (local > project > user)

## Troubleshooting

### Issue 1: Permission Settings Not Taking Effect

**Problem**: Changed settings in `.claude/settings.json` but Claude still behaves the old way

**Solution**:
- Restart your Claude Code Web session entirely
- Settings load at session start, not dynamically
- Verify the settings file is in the correct location
- Check JSON syntax with: `python3 -m json.tool .claude/settings.json`
- Ensure no `.claude/settings.local.json` is overriding your changes
- Clear browser cache and reload if using web interface

### Issue 2: Too Many Permission Prompts Despite Configuration

**Problem**: Still receiving excessive approval requests after configuring permissions

**Solution**:
- Verify `allowed-tools` configuration includes frequently-used tools
- Consider switching to `acceptEdits` mode for trusted tasks
- Use scoped permissions: `Bash(git:*)` instead of requiring approval for each git command
- Check that `permissions.allow` includes basic read operations
- Review if `permissions.ask` has too many entries that should be in `allow`
- Enable sandbox mode with predefined network and filesystem boundaries

### Issue 3: Scoped Permissions Not Working as Expected

**Problem**: Tool scoping like `Bash(git:*)` doesn't seem to work

**Solution**:
- Verify syntax: `allowed-tools` is a comma-separated string, not an array
- Correct: `"allowed-tools": "Bash(git:*),Read,Write"`
- Incorrect: `"allowed-tools": ["Bash(git:*)", "Read"]`
- Check for typos in command names (case-sensitive)
- Ensure wildcard `*` is used correctly (e.g., `npm:*` for all npm commands)
- Restart session after making changes
- Use `/config` to verify current active configuration

### Issue 4: Agent Behavior Too Restrictive or Too Permissive

**Problem**: Claude either asks for too many approvals or operates too autonomously

**Solution**:

**If too restrictive:**
- Switch from `default` to `acceptEdits` mode
- Move operations from `ask` to `allow` in permissions
- Add more tools to `allowed-tools`
- Use broader scoped permissions (e.g., `Write(src/**)` instead of `Write(src/components/*)`)
- Consider task-specific settings in `.claude/settings.local.json`

**If too permissive:**
- Switch from `acceptEdits` or `dangerously-skip-permissions` to `default` mode
- Add sensitive patterns to `permissions.deny`
- Move auto-approved operations from `allow` to `ask`
- Use narrower scoped permissions
- Enable `plan` mode for high-risk tasks

### Issue 5: Team Settings Conflict with Personal Preferences

**Problem**: Project settings in `.claude/settings.json` don't match your workflow

**Solution**:
- Create `.claude/settings.local.json` with personal overrides
- Local settings have highest priority and override project settings
- Add `.claude/settings.local.json` to `.gitignore` to prevent committing
- Example override:
```json
{
  "permissions": {
    "mode": "acceptEdits"
  },
  "allowed-tools": "Bash(*),Read,Write,Edit"
}
```
- Communicate with team about project settings if too restrictive
- Suggest changes to shared `.claude/settings.json` for team benefit

### Issue 6: JSON Syntax Errors in Settings Files

**Problem**: Settings file has syntax errors preventing loading

**Solution**:
- Use `/config` command instead of manual editing (prevents syntax errors)
- Validate JSON with: `python3 -m json.tool .claude/settings.json`
- Common mistakes:
  - Trailing commas: `{"key": "value",}` ❌ → `{"key": "value"}` ✅
  - Unquoted keys: `{key: "value"}` ❌ → `{"key": "value"}` ✅
  - Missing commas: `{"a": 1 "b": 2}` ❌ → `{"a": 1, "b": 2}` ✅
  - Comments in JSON: Not supported, remove all comments
- Use online JSON validators for complex configurations
- Start with minimal configuration and add complexity incrementally

## Additional Tips

**Start Conservative, Expand Gradually:**
- Begin with restrictive settings (`default` mode, minimal `allow` list)
- Monitor which operations you frequently approve
- Gradually move trusted operations to `allow`
- Iterate based on actual usage patterns

**Environment-Specific Strategies:**
- Development: More permissive, faster iteration
- Staging: Moderate restrictions, resembles production
- Production Review: Highly restrictive, use `plan` mode
- Security Audit: `default` mode with minimal auto-approvals

**Leverage Scoped Permissions:**
- Instead of allowing all Bash commands, scope to specific tools
- `Bash(git:*)` safer than unrestricted `Bash`
- `Write(src/**)` limits file modifications to source directory
- Scoped permissions reduce risk while maintaining productivity

**Document Your Configuration:**
- Add README section explaining project permission choices
- Comment on why specific patterns are in `deny` list
- Share configuration rationale with team
- Document any unusual or project-specific settings

**Security Best Practices:**
- Never commit `.claude/settings.local.json` (add to `.gitignore`)
- Regularly audit `permissions.deny` patterns for completeness
- Review allowed network domains weekly, remove unused
- Use most restrictive mode appropriate for task risk level
- Test configurations on non-sensitive tasks first

**Team Coordination:**
- Establish team-wide baselines in `.claude/settings.json`
- Use local settings for personal workflow preferences
- Periodically review and update shared configurations
- Share learnings from security incidents or near-misses
- Document required permissions for common project tasks

**Performance Optimization:**
- Reduce approval fatigue by properly scoping permissions
- Use `acceptEdits` for well-bounded, trusted tasks
- Configure sandbox boundaries upfront rather than approving individually
- Batch related operations in single approval with `plan` mode
- Balance security with developer productivity

**Regular Maintenance:**
- Weekly: Review allowed network domains
- Monthly: Audit permission configurations
- Per-task: Verify settings match task risk level
- After incidents: Update configurations based on learnings
- Quarterly: Review team settings with stakeholders

## Related Articles
- KB-043: How to configure project-specific settings in .claude/settings.json
- KB-048: How to review security permissions before starting a task
- KB-038: How to access sandboxed environment settings
- KB-049: How to configure network restrictions for sensitive projects
- KB-044: How to use the /config command
- KB-062: How to configure hooks to auto-format code after edits
- KB-063: How to setup hooks that require approval for bash commands

## Sources
- Claude Code settings - Claude Docs - https://docs.claude.com/en/docs/claude-code/settings (Accessed: November 16, 2025)
- Handling Permissions - Claude Docs - https://docs.claude.com/en/api/agent-sdk/permissions (Accessed: November 16, 2025)
- Claude Code for web—a new asynchronous coding agent from Anthropic - https://simonwillison.net/2025/Oct/20/claude-code-for-web/ (Accessed: November 16, 2025)
- A complete guide to Claude Code permissions - eesel AI - https://www.eesel.ai/blog/claude-code-permissions (Accessed: November 16, 2025)
- Claude Code Frameworks & Sub-Agents: The Complete 2025 Developer's Guide - https://medianeth.dev/blog/claude-code-frameworks-subagents-2025 (Accessed: November 16, 2025)
- Are Claude Skills Secure? Threat Model, Permissions & Best Practices (2025) - https://skywork.ai/blog/ai-agent/claude-skills-security-threat-model-permissions-best-practices-2025/ (Accessed: November 16, 2025)
- Making Claude Code more secure and autonomous with sandboxing - https://www.anthropic.com/engineering/claude-code-sandboxing (Accessed: November 16, 2025)
- Google Search: "Claude Code Web permission modes allowed-tools configuration 2025"

---
*This article is part of the Claude Code Web knowledge base*
