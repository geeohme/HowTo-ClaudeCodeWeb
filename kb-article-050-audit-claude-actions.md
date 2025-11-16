# How to audit what actions Claude performed in the isolated VM

**Article ID**: KB-050
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Every Claude Code Web session runs in an isolated, sandboxed virtual machine. Understanding and auditing what actions Claude performed—including commands executed, files modified, packages installed, and network requests made—is essential for security, compliance, and debugging. This guide provides a comprehensive approach to auditing Claude's actions across multiple tools and interfaces.

## Prerequisites
- Active Claude Pro or Max subscription
- Completed at least one Claude Code session
- Basic understanding of session logs (KB-020)
- Familiarity with viewing file changes (KB-015)
- Terminal/command line knowledge (for advanced techniques)

## Understanding the Isolated VM Environment

### What Runs in the Isolated VM?

**Every Claude Code Web session operates in:**
- Isolated, Anthropic-managed virtual machine
- Sandboxed environment with network restrictions
- Filesystem limitations (read/write permissions)
- Your repository cloned into the VM
- Pre-installed development toolchains (Node.js, Python, etc.)

**All Claude actions within this VM are logged:**
- Bash commands executed
- Files read, created, modified, or deleted
- Git operations (branch creation, commits, pushes)
- Package installations (npm, pip, etc.)
- Test executions
- Build processes
- Network requests (if permitted)
- Tool calls to Anthropic's API

### Why Audit VM Actions?

**Security & Compliance:**
- Verify no unauthorized access attempts
- Ensure sensitive data wasn't exposed
- Track credential usage
- Maintain compliance audit trails
- Review network activity

**Debugging & Learning:**
- Understand why tasks failed
- Learn Claude's problem-solving approaches
- Identify performance bottlenecks
- Reproduce issues locally

**Quality Assurance:**
- Verify all requested changes were made
- Ensure no unintended modifications
- Review command safety
- Check test coverage

## Steps to Audit Claude's Actions

### Step 1: Review the Session Activity Log (Web UI)

**Access via Claude Code Web interface:**

**Navigate to completed session:**
1. Go to `https://claude.com/code`
2. Find your session in Sessions or History
3. Click to open detailed view
4. Scroll through activity log

**What you'll see:**
```
[2:30 PM] Session started
          Repository: my-project
          Branch: main

[2:30 PM] Initializing sandbox environment
          VM ID: vm-abc123def456
          Region: us-east-1

[2:31 PM] Reading repository structure
          Scanned: 247 files across 45 directories

[2:31 PM] Executing: npm install
          Installing dependencies...

[2:32 PM] Command output:
          added 156 packages in 8s

[2:32 PM] Reading src/components/Header.js

[2:33 PM] Executing: git checkout -b feature/dark-mode

[2:33 PM] Editing src/components/Header.js
          Modified lines 23-45

[2:34 PM] Executing: npm test

[2:35 PM] Test output:
          PASS tests/Header.test.js
          ✓ renders header (45ms)
          ✓ toggles dark mode (32ms)
```

**Key action types to review:**
- **Executing:** - All bash commands run
- **Reading** - Files accessed
- **Editing/Creating/Deleting** - File modifications
- **Command output:** - Results from executed commands
- **Git operations** - Branch, commit, push actions

### Step 2: Access Conversation History JSONL Files (CLI Method)

**For deeper analysis, Claude stores detailed logs locally:**

**Location of conversation logs:**
```
~/.claude/projects/<project-name>/<session-id>.jsonl
```

**Each session creates a JSONL file containing:**
- User messages and instructions
- Claude's reasoning and planning
- Every tool call made to Anthropic's API
- Tool responses and outputs
- Complete conversation context
- Timestamps for all events

**View raw JSONL:**
```bash
# Navigate to projects directory
cd ~/.claude/projects

# List all projects
ls -la

# View a specific session
cat <project-name>/<session-id>.jsonl | jq .
```

**Understanding JSONL structure:**
Each line is a JSON object representing an event:
```json
{
  "type": "tool_use",
  "tool": "Bash",
  "parameters": {
    "command": "npm install react-icons",
    "description": "Install icon package"
  },
  "timestamp": "2025-11-16T14:32:15Z"
}
```

**Common event types:**
- `user_message` - Your instructions
- `assistant_message` - Claude's responses
- `tool_use` - Tool invocations (Bash, Edit, Read, etc.)
- `tool_result` - Tool outputs and results

### Step 3: Use Third-Party Analysis Tools

**Several community tools parse and visualize Claude's actions:**

#### Claude Code History Viewer

**GitHub:** `jhlee0409/claude-code-history-viewer`

**Features:**
- Browse all conversation history
- Visualize tool outputs
- Search across sessions
- Filter by project or date
- Syntax-highlighted code blocks
- Readable terminal output formatting

**Installation:**
```bash
# Clone repository
git clone https://github.com/jhlee0409/claude-code-history-viewer.git
cd claude-code-history-viewer

# Install and run (follow repo README)
npm install
npm start
```

**Use cases:**
- Visual inspection of command history
- Searching for specific tool calls
- Understanding action sequences
- Comparing across sessions

#### Claude JSONL Browser

**GitHub:** `withLinda/claude-JSONL-browser`

**Features:**
- Web-based JSONL viewer
- Converts logs to readable Markdown
- File explorer for multiple sessions
- Export functionality

**Perfect for:**
- Non-technical stakeholders reviewing actions
- Generating audit reports
- Sharing session summaries

#### Claude Code Log

**GitHub:** `daaain/claude-code-log`

**Features:**
- Python CLI tool
- Converts JSONL to HTML
- Beautiful formatting
- Timeline visualization

**Usage:**
```bash
pip install claude-code-log

# Convert session to HTML
claude-code-log ~/.claude/projects/myproject/session-123.jsonl output.html
```

#### Claude Trace (Advanced)

**For deep inspection:**

**GitHub:** `badlogic/claude-trace`

**Capabilities:**
- Intercepts all API requests/responses
- Records complete system prompts
- Captures tool definitions
- Logs every request-response pair
- Shows what happens behind the scenes

**Use when:**
- Investigating unexpected behavior
- Understanding tool selection logic
- Debugging complex issues
- Studying Claude's reasoning process

### Step 4: Review Git History and Artifacts

**Git provides its own audit trail:**

**View commit history:**
```bash
# Clone the repository locally
git clone <your-repo-url>
cd <your-repo>

# Fetch Claude's branch
git fetch origin
git checkout claude/feature-branch

# View complete commit log
git log --oneline --graph --all

# Detailed view of specific commit
git show <commit-hash>

# See all files changed in branch
git diff main...claude/feature-branch --stat

# View specific file changes
git diff main...claude/feature-branch -- path/to/file.js
```

**Audit git operations:**
- Commits made (what and when)
- Files touched in each commit
- Commit messages (understand intent)
- Branch creation/deletion
- Push operations

**Review on GitHub:**
1. Navigate to repository
2. Go to "Commits" tab
3. Filter by author (if Claude commits under specific user)
4. Click commits to see diffs
5. Review PR descriptions (if Claude created PR)

### Step 5: Examine File Changes in Detail

**Beyond viewing diffs, audit the nature of changes:**

**Security-focused review:**

**Check for sensitive data:**
- API keys or credentials hardcoded
- Database connection strings
- Private keys or certificates
- Internal URLs or endpoints
- PII (Personally Identifiable Information)

```bash
# Search for potential secrets in changes
git diff main...claude/feature-branch | grep -i "api_key\|password\|secret\|token"
```

**Verify file permissions:**
```bash
# Check if any files became executable
git diff main...claude/feature-branch --summary | grep "mode change"
```

**Review package changes:**
```bash
# See what dependencies were added
git diff main...claude/feature-branch -- package.json
git diff main...claude/feature-branch -- requirements.txt
```

**Quality checks:**
- Unexpected files modified
- Generated files committed (build artifacts)
- Large binary files added
- Configuration changes
- .gitignore modifications

### Step 6: Analyze Command Execution History

**Review all bash commands Claude executed:**

**From session log (Web UI):**
Look for entries marked "Executing:" or "Running:"
- `npm install <package>`
- `pip install <package>`
- `pytest tests/`
- `npm run build`
- `curl <url>` (network requests)
- `chmod`, `chown` (permission changes)
- Custom scripts

**Commands to pay special attention to:**

**Installation commands:**
```bash
npm install <package>
pip install <package>
apt-get install <package>  # If permitted
```
**Audit:** Verify packages are from trusted sources

**Network commands:**
```bash
curl <url>
wget <url>
git clone <url>
```
**Audit:** Check destination URLs for safety

**File operations:**
```bash
mv src/old.js src/new.js  # Rename/move
rm -rf directory/         # Deletions
chmod +x script.sh        # Permission changes
```
**Audit:** Ensure intentional and safe

**Build/execution:**
```bash
npm run build
python manage.py migrate
docker build -t app .
```
**Audit:** Understand what these commands do in your project

### Step 7: Check Network Activity (If Applicable)

**Claude Code Web has network restrictions, but allowed requests are logged:**

**Review allowed network operations:**
- Package registry access (npm, PyPI)
- Git operations to GitHub
- External API calls (if configured)
- CDN requests

**In session logs, look for:**
```
[2:34 PM] Network request: https://registry.npmjs.org/react-icons
          Status: 200 OK

[2:35 PM] Pushing to https://github.com/user/repo.git
          Branch: claude/feature-branch
```

**Audit checklist:**
- Were all network requests expected?
- Are destination URLs appropriate?
- Was data sent to external services?
- Were credentials used properly?

### Step 8: Review Tool Usage Patterns

**Understand which tools Claude used and why:**

**Common tools in isolated VM:**

**File operations:**
- `Read` - Analyzed which files
- `Edit` - Modified existing files
- `Write` - Created new files
- `Glob` - Searched for files by pattern

**Code execution:**
- `Bash` - Ran commands in terminal
- `NotebookEdit` - Modified Jupyter notebooks (if applicable)

**Search:**
- `Grep` - Searched file contents
- Pattern matching across codebase

**Analysis questions:**
- Did Claude read appropriate files?
- Were edits targeted and minimal?
- Did Bash commands make sense?
- Were tool choices efficient?

**In JSONL logs:**
```json
{
  "type": "tool_use",
  "tool": "Read",
  "parameters": {
    "file_path": "/workspace/src/config.js"
  }
}

{
  "type": "tool_result",
  "tool": "Read",
  "output": "... file contents ..."
}
```

### Step 9: Generate Audit Reports

**Create comprehensive audit documentation:**

**Manual report structure:**
```markdown
# Audit Report: Session [Session ID]
**Date:** November 16, 2025
**Repository:** my-project
**Task:** Add dark mode toggle feature

## Summary
- Duration: 8 minutes
- Files modified: 5
- Commands executed: 7
- Git commits: 2
- Network requests: 3

## Commands Executed
1. `npm install react-icons` - Install icon library
2. `git checkout -b feature/dark-mode` - Create feature branch
3. `npm test` - Run test suite (PASSED)
4. `npm run lint` - Check code style (PASSED)
5. `git add .` - Stage changes
6. `git commit -m "Add dark mode toggle"` - Commit changes
7. `git push origin feature/dark-mode` - Push to remote

## Files Modified
1. src/components/Header.js (+45, -12)
   - Added dark mode toggle button
   - Integrated useDarkMode hook
2. src/hooks/useDarkMode.js (+67, -0)
   - NEW: Custom hook for dark mode state
3. src/styles/theme.css (+34, -8)
   - Added CSS variables for themes
4. tests/Header.test.js (+28, -0)
   - NEW: Tests for toggle functionality
5. package.json (+1, -0)
   - Added react-icons dependency

## Security Review
- ✓ No credentials exposed
- ✓ No suspicious network requests
- ✓ All dependencies from official npm registry
- ✓ No unexpected file deletions
- ✓ Appropriate file permissions

## Recommendations
- Review react-icons package for vulnerabilities
- Consider adding dark mode to additional components
- Update documentation with new feature

## Approval Status
☐ Approved for merge
☐ Requires changes
☐ Rejected
```

**Automated report generation:**

Using Python to parse JSONL:
```python
import json

def generate_audit_report(jsonl_path):
    tools_used = []
    files_modified = []
    commands = []

    with open(jsonl_path, 'r') as f:
        for line in f:
            event = json.loads(line)
            if event['type'] == 'tool_use':
                tools_used.append(event['tool'])
                if event['tool'] == 'Bash':
                    commands.append(event['parameters']['command'])
                elif event['tool'] in ['Edit', 'Write']:
                    files_modified.append(event['parameters']['file_path'])

    print(f"Tools used: {set(tools_used)}")
    print(f"Commands executed: {len(commands)}")
    print(f"Files modified: {len(set(files_modified))}")
    print("\nCommands:")
    for cmd in commands:
        print(f"  - {cmd}")

generate_audit_report('~/.claude/projects/myproject/session-123.jsonl')
```

### Step 10: Enterprise Audit Features (For Enterprise Users)

**If using Claude Enterprise:**

**Access Admin Console:**
1. Navigate to Claude Admin Console
2. Go to "Audit Logs" section
3. Filter by Claude Code usage

**Enterprise audit capabilities:**
- 30-day log retention (default)
- Export to JSON or CSV
- Push logs to SIEM platforms:
  - Splunk
  - Datadog
  - Elastic
  - Others via API
- Track model usage across organization
- Monitor data flows
- SOC 2 Type II compliant logging

**Audit log information includes:**
- User who initiated session
- Timestamp of activity
- Repository accessed
- Actions performed
- Duration of session
- Resources consumed

## Expected Results

After completing this audit process, you should:
- Have complete visibility into all VM actions
- Understand every command Claude executed
- Know which files were read, modified, or created
- Identify any security concerns or anomalies
- Possess documentation for compliance purposes
- Be able to reproduce Claude's work locally
- Have confidence in approving or rejecting changes
- Maintain proper audit trails for your organization

## Troubleshooting

### Common Issue 1
**Problem**: Can't find JSONL conversation logs
**Solution**:
- Logs are only available for CLI version of Claude Code
- Claude Code Web doesn't store local JSONL files
- You can only access web UI activity logs
- For local JSONL access, use Claude Code CLI
- Consider using browser dev tools to inspect network requests
- Export session data from web UI if available

### Common Issue 2
**Problem**: Session logs don't show command outputs
**Solution**:
- Some outputs may be truncated for brevity
- Click "Show more" or "Expand" if available
- Very long outputs (>1000 lines) may be summarized
- Check git commits for actual file changes
- Pull branch locally and inspect directly
- Use third-party history viewer for full outputs

### Common Issue 3
**Problem**: Suspicious command executed
**Solution**:
- Don't merge the changes immediately
- Review what the command does
- Check if it's necessary for the task
- Look for command output to see what happened
- Test the branch in isolated local environment
- Ask Claude why it ran that command
- Report to Anthropic if truly malicious

### Common Issue 4
**Problem**: Can't determine if network request was safe
**Solution**:
- Check destination URL and domain
- Verify it's a legitimate service (npm, PyPI, etc.)
- Look for HTTPS (not HTTP)
- Review what data was sent
- Check if credentials were used
- Test the endpoint separately
- When in doubt, reject and ask Claude to explain

### Common Issue 5
**Problem**: Too much information to review manually
**Solution**:
- Focus on high-risk actions first (commands, network, secrets)
- Use automated scanning tools
- Parse JSONL with scripts to flag concerns
- Review git diff instead of full logs
- Spot-check random actions for quality
- Use filtering in third-party viewers
- Create automated audit checklist

## Additional Tips

### Audit Prioritization

**High priority items:**
1. Commands with `sudo`, `rm -rf`, `chmod`, `curl`
2. New dependencies added
3. Configuration file changes
4. Environment variable modifications
5. Network requests to external services
6. Files in `.git` or `.github` directories

**Medium priority:**
1. Test file modifications
2. Documentation updates
3. Code refactoring
4. Style/linting changes

**Low priority:**
1. Comment additions
2. Whitespace changes
3. Import reorganization

### Creating Audit Workflows

**For teams, establish process:**

**Pre-merge checklist:**
- [ ] Review session activity log
- [ ] Examine all files changed
- [ ] Verify commands executed were safe
- [ ] Check for secrets in code
- [ ] Scan dependencies for vulnerabilities
- [ ] Run tests locally
- [ ] Get second reviewer approval

**Automated checks:**
```bash
#!/bin/bash
# audit-claude-session.sh

BRANCH="$1"

echo "Auditing branch: $BRANCH"

# Check for potential secrets
echo "\n[1/5] Scanning for secrets..."
git diff main...$BRANCH | grep -i "api_key\|password\|secret\|token" && echo "⚠ Potential secrets found!" || echo "✓ No secrets detected"

# Check for suspicious commands in commits
echo "\n[2/5] Checking commit messages..."
git log main...$BRANCH --oneline | grep -i "sudo\|rm -rf\|curl" && echo "⚠ Suspicious commands mentioned" || echo "✓ No suspicious commands"

# List all files changed
echo "\n[3/5] Files changed:"
git diff main...$BRANCH --name-only

# Check package changes
echo "\n[4/5] Dependency changes:"
git diff main...$BRANCH -- package.json requirements.txt 2>/dev/null | grep "^+.*" | grep -v "^+++"

# Summary
echo "\n[5/5] Summary:"
echo "Commits: $(git rev-list --count main...$BRANCH)"
echo "Files changed: $(git diff main...$BRANCH --name-only | wc -l)"
echo "Lines added: $(git diff main...$BRANCH --shortstat | awk '{print $4}')"
echo "Lines removed: $(git diff main...$BRANCH --shortstat | awk '{print $6}')"
```

### Best Practices for Ongoing Auditing

**Regular reviews:**
- Audit all sessions, not just suspicious ones
- Establish consistent review process
- Document patterns and learnings
- Share findings with team
- Update task descriptions to prevent issues

**Knowledge building:**
- Keep notes on common Claude patterns
- Understand typical tool usage
- Learn normal vs. abnormal behaviors
- Build intuition for red flags

**Security hygiene:**
- Never commit secrets (even in Claude's code)
- Review .env and config files carefully
- Scan dependencies regularly
- Keep audit logs for required retention period
- Report security concerns to Anthropic

### Integration with Development Workflow

**Incorporate auditing into CI/CD:**

**GitHub Actions example:**
```yaml
name: Audit Claude Changes

on:
  pull_request:
    branches: [main]

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Check for secrets
        run: |
          git diff origin/main...HEAD | grep -i "api_key\|password\|secret" && exit 1 || exit 0

      - name: Scan dependencies
        run: npm audit --audit-level=moderate

      - name: Generate audit report
        run: |
          echo "## Audit Report" >> $GITHUB_STEP_SUMMARY
          echo "Files changed: $(git diff origin/main...HEAD --name-only | wc -l)" >> $GITHUB_STEP_SUMMARY
          echo "Commands in commits: $(git log origin/main..HEAD --grep='Executing:' --oneline | wc -l)" >> $GITHUB_STEP_SUMMARY
```

### Learning From Audit Data

**Analyze trends over time:**
- Most commonly executed commands
- Typical session durations
- Files frequently modified
- Tools Claude prefers
- Success vs. failure patterns

**Use insights to:**
- Improve task descriptions
- Adjust repository structure
- Configure better CI/CD
- Train team on best practices
- Optimize development workflow

## Related Articles
- KB-020: How to access session history and logs
- KB-015: How to view changes made by Claude Code in your repository
- KB-009: How to view the progress of your task execution
- KB-008: How to monitor a running task in real-time
- KB-047: How to understand credential access and permissions
- KB-049: How to configure network restrictions and access

## Sources
1. Claude Code Web Security - https://code.claude.com/docs/en/security (Accessed: November 16, 2025)
2. How to Access Audit Logs | Claude Help Center - https://support.claude.com/en/articles/9970975-how-to-access-audit-logs (Accessed: November 16, 2025)
3. Claude Code History Viewer - https://github.com/jhlee0409/claude-code-history-viewer (Accessed: November 16, 2025)
4. Claude JSONL Browser - https://github.com/withLinda/claude-JSONL-browser (Accessed: November 16, 2025)
5. Claude Code Log - https://github.com/daaain/claude-code-log (Accessed: November 16, 2025)
6. Analyzing Claude Code Interaction Logs with DuckDB - https://liambx.com/blog/claude-code-log-analysis-with-duckdb (Accessed: November 16, 2025)
7. cchistory: Tracking Claude Code System Prompt and Tool Changes - https://mariozechner.at/posts/2025-08-03-cchistory/ (Accessed: November 16, 2025)
8. Google Search: "Claude Code Web isolated VM audit security compliance" (Accessed: November 16, 2025)
9. Google Search: "JSONL conversation logs ~/.claude/projects audit" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
