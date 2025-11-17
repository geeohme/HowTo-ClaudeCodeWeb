# How to set up hooks that require approval for bash commands

**Article ID**: KB-063
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
Claude Code hooks allow you to intercept and control tool execution before it happens. PreToolUse hooks specifically enable you to require user approval for bash commands based on security policies, command patterns, or risk levels. This advanced security feature gives you fine-grained control over which commands execute automatically and which require explicit confirmation, protecting your systems from potentially dangerous operations while maintaining development efficiency.

## Prerequisites
- Active Claude Pro or Max subscription
- Completed at least one Claude Code session (KB-006)
- Basic understanding of JSON configuration files
- Command line/terminal knowledge
- Familiarity with bash commands and shell scripting
- Text editor for editing configuration files

## Understanding PreToolUse Hooks and Permission Decisions

### What are PreToolUse Hooks?

PreToolUse hooks fire **before any tool execution**, intercepting the tool call with access to:
- `tool_name` - The tool being invoked (e.g., "Bash", "Edit", "Read")
- `tool_input` - Complete parameters passed to the tool
- `tool_description` - Claude's description of what the tool will do

### Permission Decision Mechanism

PreToolUse hooks can return structured output controlling execution flow:

**Available permission decisions:**
- `"allow"` - Bypasses the permission system entirely; command executes automatically
- `"deny"` - Prevents tool execution; reason shown to Claude for adjustment
- `"ask"` - **Prompts user for confirmation** in the UI; reason shown to user but not Claude

**Exit code behavior:**
- **Exit code 0** - Success; hook executed normally, default permission flow continues
- **Exit code 2** - Blocking error; stderr message sent to Claude, prevents tool execution
- **Other codes** - Non-blocking errors shown to users

### Hook Configuration Locations

Hooks can be configured in three locations (in order of precedence):

1. **User settings** - `~/.claude/settings.json` (applies to all projects)
2. **Project settings** - `.claude/settings.json` (project-specific, committed to git)
3. **Local project settings** - `.claude/settings.local.json` (local only, not committed)

**Important security note:** Direct edits to hooks in settings files don't take effect immediately and require review in the `/hooks` menu for changes to apply. This prevents malicious hook modifications from affecting your current session.

## Steps

### Step 1: Create or Edit Your Hooks Configuration File

**Choose your configuration scope:**

**For user-wide hooks (all Claude Code sessions):**
```bash
# Create/edit user settings file
mkdir -p ~/.claude
touch ~/.claude/settings.json
```

**For project-specific hooks (recommended for teams):**
```bash
# Navigate to your project root
cd /path/to/your/project

# Create .claude directory
mkdir -p .claude

# Create settings file
touch .claude/settings.json
```

**For local testing (not committed to git):**
```bash
# In project root
touch .claude/settings.local.json

# Add to .gitignore
echo ".claude/settings.local.json" >> .gitignore
```

**Open the file in your text editor:**
```bash
# Using nano
nano ~/.claude/settings.json

# Using vim
vim ~/.claude/settings.json

# Using VS Code
code ~/.claude/settings.json
```

### Step 2: Configure Basic PreToolUse Hook with Approval Required

**Add the following JSON structure to require approval for ALL bash commands:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "echo '{\"hookSpecificOutput\": {\"hookEventName\": \"PreToolUse\", \"permissionDecision\": \"ask\", \"permissionDecisionReason\": \"All bash commands require manual approval\"}}'"
          }
        ]
      }
    ]
  }
}
```

**Configuration breakdown:**
- `"matcher": "Bash"` - Matches only Bash tool invocations
- `"type": "command"` - Executes a shell command for validation
- `hookEventName: "PreToolUse"` - Identifies this as PreToolUse hook output
- `permissionDecision: "ask"` - Requires user approval in UI
- `permissionDecisionReason` - Message shown to user in approval dialog

**Save the file** (Ctrl+O in nano, :wq in vim)

### Step 3: Create Selective Approval Based on Command Patterns

**For more sophisticated control, validate commands based on patterns:**

Create a validation script at `~/.claude/scripts/bash-approval-hook.sh`:

```bash
#!/bin/bash
# bash-approval-hook.sh - Validates bash commands and requires approval for risky operations

# Read the tool input (Claude provides this as JSON via stdin or environment)
COMMAND=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.command // empty')
DESCRIPTION=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.description // "No description"')

# Define dangerous patterns that ALWAYS require approval
DANGEROUS_PATTERNS=(
  "rm -rf"
  "sudo"
  "chmod 777"
  "mkfs"
  "dd if="
  "> /dev/"
  "curl.*|.*sh"
  "wget.*|.*sh"
  "eval"
  ":(){ :|:& };:"  # Fork bomb
)

# Define sensitive directories that require approval
SENSITIVE_PATHS=(
  "/etc/"
  "/sys/"
  "/proc/"
  "/boot/"
  "/root/"
  "~/.ssh"
  "~/.aws"
)

# Check for dangerous patterns
for pattern in "${DANGEROUS_PATTERNS[@]}"; do
  if [[ "$COMMAND" =~ $pattern ]]; then
    echo "{
      \"hookSpecificOutput\": {
        \"hookEventName\": \"PreToolUse\",
        \"permissionDecision\": \"ask\",
        \"permissionDecisionReason\": \"Potentially dangerous command detected: $pattern\\n\\nCommand: $COMMAND\\n\\nDescription: $DESCRIPTION\\n\\nPlease review carefully before approving.\"
      }
    }"
    exit 0
  fi
done

# Check for sensitive path access
for path in "${SENSITIVE_PATHS[@]}"; do
  if [[ "$COMMAND" =~ $path ]]; then
    echo "{
      \"hookSpecificOutput\": {
        \"hookEventName\": \"PreToolUse\",
        \"permissionDecision\": \"ask\",
        \"permissionDecisionReason\": \"Access to sensitive path: $path\\n\\nCommand: $COMMAND\\n\\nRequires approval for security.\"
      }
    }"
    exit 0
  fi
done

# Check for package installation (may want approval for supply chain security)
if [[ "$COMMAND" =~ ^(npm|pip|gem|cargo|go)\ install ]]; then
  PACKAGE=$(echo "$COMMAND" | awk '{print $3}')
  echo "{
    \"hookSpecificOutput\": {
      \"hookEventName\": \"PreToolUse\",
      \"permissionDecision\": \"ask\",
      \"permissionDecisionReason\": \"Package installation requires approval\\n\\nPackage: $PACKAGE\\n\\nCommand: $COMMAND\\n\\nPlease verify package legitimacy.\"
    }
  }"
  exit 0
fi

# Check for network operations
if [[ "$COMMAND" =~ (curl|wget|nc|netcat|telnet) ]]; then
  echo "{
    \"hookSpecificOutput\": {
      \"hookEventName\": \"PreToolUse\",
      \"permissionDecision\": \"ask\",
      \"permissionDecisionReason\": \"Network operation requires approval\\n\\nCommand: $COMMAND\\n\\nVerify destination is safe.\"
    }
  }"
  exit 0
fi

# Allow safe commands automatically
echo "{
  \"hookSpecificOutput\": {
    \"hookEventName\": \"PreToolUse\",
    \"permissionDecision\": \"allow\",
    \"permissionDecisionReason\": \"Command passed security validation\"
  }
}"
exit 0
```

**Make the script executable:**
```bash
chmod +x ~/.claude/scripts/bash-approval-hook.sh
```

**Update your settings.json to use this script:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/bash-approval-hook.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 4: Implement Multi-Tier Approval System

**For enterprise environments, implement different approval levels:**

Create `~/.claude/scripts/tiered-approval-hook.sh`:

```bash
#!/bin/bash
# tiered-approval-hook.sh - Multi-tier approval system

COMMAND=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.command // empty')

# TIER 1: BLOCK immediately (no approval option)
BLOCKED_PATTERNS=(
  "rm -rf /"
  "rm -rf /*"
  "> /dev/sda"
  "mkfs"
  ":(){ :|:& };:"
)

for pattern in "${BLOCKED_PATTERNS[@]}"; do
  if [[ "$COMMAND" == *"$pattern"* ]]; then
    # Use exit code 2 to block and send message to Claude
    echo "SECURITY VIOLATION: Command pattern '$pattern' is strictly forbidden." >&2
    echo "This command would cause catastrophic system damage." >&2
    echo "Please revise your approach without destructive operations." >&2
    exit 2
  fi
done

# TIER 2: REQUIRE APPROVAL (ask user)
APPROVAL_PATTERNS=(
  "sudo"
  "rm -rf"
  "chmod 777"
  "curl.*|.*sh"
  "eval"
  "npm install"
  "pip install"
)

for pattern in "${APPROVAL_PATTERNS[@]}"; do
  if [[ "$COMMAND" =~ $pattern ]]; then
    echo "{
      \"hookSpecificOutput\": {
        \"hookEventName\": \"PreToolUse\",
        \"permissionDecision\": \"ask\",
        \"permissionDecisionReason\": \"⚠️ HIGH RISK COMMAND\\n\\nPattern: $pattern\\nCommand: $COMMAND\\n\\nThis operation requires security review and approval.\"
      }
    }"
    exit 0
  fi
done

# TIER 3: AUTO-ALLOW safe commands
echo "{
  \"hookSpecificOutput\": {
    \"hookEventName\": \"PreToolUse\",
    \"permissionDecision\": \"allow\"
  }
}"
exit 0
```

**Make executable and configure:**
```bash
chmod +x ~/.claude/scripts/tiered-approval-hook.sh
```

**Update settings.json:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/tiered-approval-hook.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 5: Add Logging and Audit Trail

**Enhance your approval hook with comprehensive logging:**

Create `~/.claude/scripts/bash-approval-with-logging.sh`:

```bash
#!/bin/bash
# bash-approval-with-logging.sh - Approval hook with audit logging

COMMAND=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.command // empty')
DESCRIPTION=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.description // "No description"')
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
LOG_FILE="$HOME/.claude/bash-command-audit.log"

# Ensure log directory exists
mkdir -p "$(dirname "$LOG_FILE")"

# Log all bash command attempts
echo "[$TIMESTAMP] COMMAND_ATTEMPT: $COMMAND | DESCRIPTION: $DESCRIPTION" >> "$LOG_FILE"

# Define patterns requiring approval
APPROVAL_REQUIRED=(
  "rm -rf"
  "sudo"
  "chmod 777"
  "curl.*|.*sh"
  "npm install"
  "pip install"
)

# Check if approval needed
NEEDS_APPROVAL=false
MATCHED_PATTERN=""

for pattern in "${APPROVAL_REQUIRED[@]}"; do
  if [[ "$COMMAND" =~ $pattern ]]; then
    NEEDS_APPROVAL=true
    MATCHED_PATTERN="$pattern"
    break
  fi
done

if [ "$NEEDS_APPROVAL" = true ]; then
  # Log approval request
  echo "[$TIMESTAMP] APPROVAL_REQUESTED: Pattern '$MATCHED_PATTERN' matched" >> "$LOG_FILE"

  # Return ask decision
  echo "{
    \"hookSpecificOutput\": {
      \"hookEventName\": \"PreToolUse\",
      \"permissionDecision\": \"ask\",
      \"permissionDecisionReason\": \"Security Review Required\\n\\nCommand: $COMMAND\\n\\nMatched Pattern: $MATCHED_PATTERN\\n\\nThis command has been logged for audit.\"
    }
  }"
else
  # Log auto-approval
  echo "[$TIMESTAMP] AUTO_APPROVED: Safe command" >> "$LOG_FILE"

  # Return allow decision
  echo "{
    \"hookSpecificOutput\": {
      \"hookEventName\": \"PreToolUse\",
      \"permissionDecision\": \"allow\"
    }
  }"
fi

exit 0
```

**Make executable:**
```bash
chmod +x ~/.claude/scripts/bash-approval-with-logging.sh
```

**View your audit log:**
```bash
# View recent command attempts
tail -f ~/.claude/bash-command-audit.log

# Search for approval requests
grep "APPROVAL_REQUESTED" ~/.claude/bash-command-audit.log

# View all commands from today
grep "$(date -u +%Y-%m-%d)" ~/.claude/bash-command-audit.log
```

### Step 6: Test Your Approval Hook

**Start a Claude Code Web session and test the hook behavior:**

**Test 1: Trigger approval for dangerous command**

Ask Claude: "Run `sudo apt-get update` to update packages"

**Expected behavior:**
1. Claude attempts to execute bash command
2. PreToolUse hook intercepts
3. UI shows approval dialog with your custom message
4. You can approve or reject

**Test 2: Verify safe commands auto-approve**

Ask Claude: "List files in the current directory"

**Expected behavior:**
1. Claude runs `ls` command
2. Hook allows it automatically
3. No approval dialog shown
4. Command executes immediately

**Test 3: Check blocking of catastrophic commands**

Ask Claude: "Remove all files with `rm -rf /`"

**Expected behavior:**
1. Hook blocks with exit code 2
2. Error message sent to Claude
3. Claude receives feedback and revises approach
4. No user approval dialog (blocked outright)

### Step 7: Configure Project-Specific Approval Policies

**For different projects with different risk profiles:**

**Low-risk development project** (`.claude/settings.json`):
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "if echo \"$CLAUDE_TOOL_INPUT\" | jq -r '.command' | grep -qE '(sudo|rm -rf /)'; then echo '{\"hookSpecificOutput\": {\"hookEventName\": \"PreToolUse\", \"permissionDecision\": \"ask\", \"permissionDecisionReason\": \"Critical operation requires approval\"}}'; else echo '{\"hookSpecificOutput\": {\"hookEventName\": \"PreToolUse\", \"permissionDecision\": \"allow\"}}'; fi"
          }
        ]
      }
    ]
  }
}
```

**High-security production project** (`.claude/settings.json`):
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/strict-approval-production.sh"
          }
        ]
      }
    ]
  }
}
```

Create `~/.claude/scripts/strict-approval-production.sh`:
```bash
#!/bin/bash
# ALL commands require approval in production projects

COMMAND=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.command // empty')
DESCRIPTION=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.description // "No description"')

echo "{
  \"hookSpecificOutput\": {
    \"hookEventName\": \"PreToolUse\",
    \"permissionDecision\": \"ask\",
    \"permissionDecisionReason\": \"🔒 PRODUCTION ENVIRONMENT\\n\\nALL bash commands require approval\\n\\nCommand: $COMMAND\\n\\nDescription: $DESCRIPTION\"
  }
}"
exit 0
```

### Step 8: Use Prompt-Based Hooks for LLM-Powered Decisions

**For advanced validation using AI reasoning:**

**Configure LLM-based approval hook:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "You are a security validator for bash commands. Analyze this command and determine if it should require user approval.\n\nCommand: {{tool_input.command}}\nDescription: {{tool_input.description}}\n\nConsider:\n1. Is this command potentially destructive? (rm, mkfs, dd, etc.)\n2. Does it modify system files or permissions? (sudo, chmod, chown)\n3. Does it access sensitive data? (~/.ssh, ~/.aws, /etc)\n4. Does it make network requests? (curl, wget)\n5. Does it install packages? (npm, pip, apt)\n\nRespond with JSON:\n{\"hookSpecificOutput\": {\"hookEventName\": \"PreToolUse\", \"permissionDecision\": \"allow|ask|deny\", \"permissionDecisionReason\": \"explanation\"}}"
          }
        ]
      }
    ]
  }
}
```

**This approach:**
- Uses Claude's reasoning to evaluate command safety
- Provides context-aware decisions
- Can adapt to novel attack patterns
- Explains reasoning in approval dialog

### Step 9: Verify and Activate Your Hooks

**Check hook configuration:**

1. **In Claude Code Web, run the `/hooks` command**
2. Review all configured hooks
3. Verify PreToolUse hooks are listed
4. Check matcher patterns are correct

**Expected output:**
```
Configured Hooks:

PreToolUse:
  Matcher: Bash
  Type: command
  Command: ~/.claude/scripts/bash-approval-hook.sh
  Status: Active
```

**Activate hooks if needed:**
- Changes to hooks require review in `/hooks` menu
- This security feature prevents malicious hook injection
- Approve the hook changes when prompted

### Step 10: Monitor and Refine Your Approval Policies

**Review your audit logs regularly:**

```bash
# View approval patterns
grep -E "APPROVAL_REQUESTED|AUTO_APPROVED" ~/.claude/bash-command-audit.log | tail -20

# Count commands requiring approval
grep -c "APPROVAL_REQUESTED" ~/.claude/bash-command-audit.log

# Find most common approval triggers
grep "APPROVAL_REQUESTED" ~/.claude/bash-command-audit.log | awk -F"'" '{print $2}' | sort | uniq -c | sort -rn
```

**Refine patterns based on usage:**
- Too many approvals for safe commands? → Relax patterns
- Dangerous commands slipping through? → Add stricter patterns
- False positives? → Improve pattern specificity

**Share with team:**
```bash
# Commit project hooks to git
git add .claude/settings.json
git commit -m "Add bash command approval hooks for security"
git push
```

## Expected Results

After configuring approval hooks for bash commands, you should experience:

**Security improvements:**
- User confirmation required for dangerous commands (rm -rf, sudo, chmod 777)
- Package installations require review (npm install, pip install)
- Network operations flagged for approval (curl, wget)
- Sensitive path access controlled (~/.ssh, /etc, /root)
- Catastrophic commands blocked entirely (rm -rf /, fork bombs)

**User experience:**
- **Safe commands execute immediately** without interruption (ls, cat, grep, npm test)
- **Risky commands show approval dialog** with clear reasoning and command details
- **Blocked commands** send feedback to Claude, who revises approach automatically
- **Audit trail** of all command attempts for compliance and debugging

**Approval dialog format:**
```
⚠️ Command Approval Required

Pattern Matched: sudo
Command: sudo apt-get update
Description: Update package lists

This operation requires security review and approval.

[Approve]  [Deny]
```

## Troubleshooting

### Common Issue 1
**Problem**: Hooks not activating - all bash commands execute without approval prompts
**Solution**:
- Hooks require review in `/hooks` menu before activation
- Run `/hooks` in Claude Code Web interface
- Look for pending hook changes
- Click "Approve" or "Activate" to enable the hooks
- Verify hook status shows "Active"
- Restart Claude Code session if needed
- Check JSON syntax is valid (use `jq . < ~/.claude/settings.json`)

### Common Issue 2
**Problem**: Hook script returns error or doesn't parse JSON correctly
**Solution**:
- Test your script manually: `echo '{"command":"rm -rf test"}' | CLAUDE_TOOL_INPUT='{"command":"rm -rf test"}' ~/.claude/scripts/bash-approval-hook.sh`
- Verify script has execute permissions: `chmod +x ~/.claude/scripts/bash-approval-hook.sh`
- Check script shebang is correct: `#!/bin/bash`
- Ensure `jq` is installed: `which jq` or `sudo apt-get install jq`
- Validate JSON output: `your-script.sh | jq .`
- Review hook execution logs in Claude Code Web session history
- Add debug logging: `echo "DEBUG: Command=$COMMAND" >> /tmp/hook-debug.log`

### Common Issue 3
**Problem**: Too many false positives - safe commands requiring approval unnecessarily
**Solution**:
- Refine your pattern matching to be more specific
- Use anchors in regex: `^rm -rf /` vs `rm -rf` (latter matches `rm -rf ./temp`)
- Whitelist known-safe command patterns
- Create allow-list for project-specific safe operations
- Review audit logs to identify common false positives
- Consider using LLM-based prompt hooks for context-aware decisions
- Example refinement: Change `"npm install"` to `"npm install.*-g"` to only flag global installs

### Common Issue 4
**Problem**: Hook blocks command but Claude doesn't understand the feedback
**Solution**:
- Use exit code 2 for blocking (sends stderr to Claude)
- Provide clear, actionable feedback in stderr message
- Example: `echo "This command would delete system files. Please use a safer approach like moving files to trash." >&2`
- Don't just say "blocked" - explain WHY and suggest alternatives
- Use `permissionDecisionReason` for approval prompts (shown to user)
- Use stderr messages for denials (shown to Claude)

### Common Issue 5
**Problem**: Hooks work locally but not in Claude Code Web
**Solution**:
- **Claude Code Web has different hook capabilities than CLI**
- Web version may not support all hook types or features
- Ensure your hook script doesn't rely on local file system paths unavailable in web sandbox
- Use project-level hooks (`.claude/settings.json`) instead of user-level
- Check that external tools used in hook (like `jq`) are available in web environment
- Consider using simpler inline bash instead of external scripts for web compatibility
- Test hook in web session specifically, not just CLI

### Common Issue 6
**Problem**: `CLAUDE_TOOL_INPUT` environment variable is empty or undefined
**Solution**:
- Claude Code provides tool input via stdin, not always environment variable
- Read from stdin instead: `TOOL_INPUT=$(cat)` then `COMMAND=$(echo "$TOOL_INPUT" | jq -r '.command')`
- Or use stdin directly: `jq -r '.command' <<< "$CLAUDE_TOOL_INPUT"`
- Check Claude Code version - input method may vary
- Add fallback: `TOOL_INPUT="${CLAUDE_TOOL_INPUT:-$(cat)}"`
- Test both methods in your script

## Additional Tips

### Pattern Matching Best Practices

**Use specific patterns to avoid false positives:**
```bash
# Bad: Too broad
if [[ "$COMMAND" =~ rm ]]; then

# Good: Specific dangerous pattern
if [[ "$COMMAND" =~ rm\ +-rf\ +/ ]]; then

# Better: Multiple specific patterns
if [[ "$COMMAND" =~ ^rm\ +-rf\ +/.*$ ]] || [[ "$COMMAND" =~ ^rm\ +-rf\ +\*.*$ ]]; then
```

**Consider command context:**
- `rm -rf temp/` is usually safe
- `rm -rf /` is catastrophic
- Use path analysis, not just pattern matching

### Security Considerations

**Hook security is critical:**
- **Hooks execute with your user permissions** - they can do anything you can
- **Validate hook scripts carefully** - malicious hooks could compromise security
- **Never download and run hooks from untrusted sources**
- **Review team members' hook changes** before merging to project settings
- **Use `.claude/settings.local.json`** for testing before committing
- **Consider code signing** for hook scripts in enterprise environments

### Performance Optimization

**Keep hooks fast:**
- Heavy validation can slow down Claude's workflow
- Cache expensive operations where possible
- Avoid network calls in hooks
- Keep pattern lists concise
- Profile hook execution: `time ~/.claude/scripts/bash-approval-hook.sh`
- Target <100ms execution time

### Team Collaboration

**Standardize hooks across team:**

**Create a team hook library:**
```bash
# Create team hooks repository
git init claude-code-hooks
cd claude-code-hooks

# Add standard hooks
mkdir hooks
cp ~/.claude/scripts/bash-approval-hook.sh hooks/

# Document usage
cat > README.md << 'EOF'
# Team Claude Code Hooks

## Installation
```bash
cp hooks/* ~/.claude/scripts/
chmod +x ~/.claude/scripts/*
```

## Project Setup
Add to `.claude/settings.json`:
[configuration example]
EOF

git add .
git commit -m "Initial team hook library"
```

**Share and maintain:**
- Document approval policies in team wiki
- Review and update hooks quarterly
- Collect feedback on false positives/negatives
- Version control hook configurations
- Run security audits on hook code

### Advanced Approval Workflows

**Conditional approval based on environment:**

```bash
#!/bin/bash
# Environment-aware approval

COMMAND=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.command // empty')
ENVIRONMENT="${CLAUDE_ENVIRONMENT:-development}"  # Set in project config

if [ "$ENVIRONMENT" = "production" ]; then
  # ALL commands require approval in production
  DECISION="ask"
  REASON="Production environment - all commands require review"
elif [ "$ENVIRONMENT" = "staging" ]; then
  # Only dangerous commands in staging
  if [[ "$COMMAND" =~ (sudo|rm -rf|chmod 777) ]]; then
    DECISION="ask"
    REASON="Dangerous command in staging environment"
  else
    DECISION="allow"
    REASON="Safe command in staging"
  fi
else
  # Development is permissive
  DECISION="allow"
  REASON="Development environment"
fi

echo "{
  \"hookSpecificOutput\": {
    \"hookEventName\": \"PreToolUse\",
    \"permissionDecision\": \"$DECISION\",
    \"permissionDecisionReason\": \"$REASON\"
  }
}"
```

### Integration with External Tools

**Integrate with security scanners:**

```bash
#!/bin/bash
# Use shellcheck for bash validation

COMMAND=$(echo "$CLAUDE_TOOL_INPUT" | jq -r '.command // empty')

# Write command to temp file
TEMP_FILE=$(mktemp)
echo "#!/bin/bash" > "$TEMP_FILE"
echo "$COMMAND" >> "$TEMP_FILE"

# Run shellcheck
if shellcheck -S warning "$TEMP_FILE" 2>/dev/null; then
  DECISION="allow"
  REASON="Command passed shellcheck validation"
else
  DECISION="ask"
  REASON="Command has shellcheck warnings - please review"
fi

rm -f "$TEMP_FILE"

echo "{
  \"hookSpecificOutput\": {
    \"hookEventName\": \"PreToolUse\",
    \"permissionDecision\": \"$DECISION\",
    \"permissionDecisionReason\": \"$REASON\"
  }
}"
```

### Compliance and Auditing

**Generate compliance reports:**

```bash
# Monthly audit report
cat ~/.claude/bash-command-audit.log | \
  grep "$(date -d '1 month ago' +%Y-%m)" | \
  awk '{
    if ($0 ~ /APPROVAL_REQUESTED/) approvals++;
    if ($0 ~ /AUTO_APPROVED/) auto++;
    commands++;
  }
  END {
    print "Month: "$(date +%Y-%m)
    print "Total commands: " commands
    print "Required approval: " approvals
    print "Auto-approved: " auto
    print "Approval rate: " (approvals/commands*100) "%"
  }'
```

## Related Articles
- KB-050: How to audit what actions Claude performed in the isolated VM
- KB-048: How to review security permissions for your projects
- KB-049: How to configure network restrictions and access
- KB-043: How to configure project-specific settings
- KB-044: How to use the config command effectively

## Sources
1. Hooks reference - Claude Docs - https://docs.claude.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
2. Get started with Claude Code hooks - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/hooks-guide (Accessed: November 16, 2025)
3. Claude Code Hooks Mastery - GitHub - https://github.com/disler/claude-code-hooks-mastery (Accessed: November 16, 2025)
4. The Ultimate Claude Code Guide - DEV Community - https://dev.to/holasoymalva/the-ultimate-claude-code-guide-every-hidden-trick-hack-and-power-feature-you-need-to-know-2l45 (Accessed: November 16, 2025)
5. PreToolUse hooks cannot block tool execution - Issue #4362 - https://github.com/anthropics/claude-code/issues/4362 (Accessed: November 16, 2025)
6. Feature Request: Add 'prompt' list to permission rules - Issue #4903 - https://github.com/anthropics/claude-code/issues/4903 (Accessed: November 16, 2025)
7. Google Search: "Claude Code hooks PreToolUse permission decision ask approval bash" (Accessed: November 16, 2025)
8. Google Search: "Claude Code .claude/settings.json hooks configuration security validation" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
