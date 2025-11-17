# How to use Stop hooks for cleanup operations

**Article ID**: KB-065
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
Stop hooks in Claude Code Web enable you to automate cleanup operations, logging, and resource management when Claude finishes tasks or sessions end. This article covers both the Stop hook (which fires when Claude finishes responding) and the SessionEnd hook (specifically designed for session cleanup), helping you maintain a clean development environment and implement automated workflows.

## Prerequisites
- Claude Code Web account with active session
- Access to your local `.claude` directory for configuration
- Basic understanding of JSON configuration files
- Familiarity with bash or Python scripting for hook implementation
- Related articles: KB-016 (Start new session), KB-020 (Access session history logs)

## Steps

### Step 1: Understand Hook Types for Cleanup

Claude Code provides two primary hooks for cleanup operations:

**Stop Hook**: Fires when the main Claude Code agent finishes responding (not when interrupted by user). Use for:
- Validation checks after task completion
- Logging task statistics
- Triggering notifications
- Conditional cleanup based on response quality

**SessionEnd Hook**: Fires when a Claude Code session terminates. Use for:
- Deleting temporary files and directories
- Stopping background processes
- Archiving session transcripts
- Saving session state to persistent storage

**SubagentStop Hook**: Similar to Stop but fires when subagents (Task tool calls) complete. Use for nested cleanup operations.

**Important**: Stop hooks can block Claude from finishing (exit code 2), while SessionEnd hooks cannot block session termination.

### Step 2: Create Your Settings Configuration File

Navigate to your Claude Code settings directory and create or edit the settings file:

```bash
# Open or create settings file
nano ~/.claude/settings.json
```

Add the hooks configuration structure:

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/stop_hook.sh"
          }
        ]
      }
    ],
    "SessionEnd": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/cleanup.py"
          }
        ]
      }
    ]
  }
}
```

**Configuration Notes**:
- `matcher`: Optional regex pattern to filter when hook runs (empty matches all)
- `type`: Always "command" for executing scripts
- `command`: Absolute path to your hook script

### Step 3: Create a Stop Hook for Task Logging

Create a Stop hook script that logs task completion and performs light cleanup:

```bash
#!/bin/bash
# File: ~/.claude/hooks/stop_hook.sh

# Read JSON input from stdin
INPUT=$(cat)

# Extract fields using jq
SESSION_ID=$(echo "$INPUT" | jq -r '.session_id')
TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')

# Log completion
LOG_FILE="$HOME/.claude/task_log.txt"
echo "[$TIMESTAMP] Task completed in session: $SESSION_ID" >> "$LOG_FILE"

# Optional: Send notification
if command -v notify-send &> /dev/null; then
    notify-send "Claude Code" "Task completed successfully"
fi

# Exit code 0: Allow Claude to stop normally
# Exit code 2: Block stopping and force continuation
exit 0
```

Make the script executable:

```bash
chmod +x ~/.claude/hooks/stop_hook.sh
```

### Step 4: Create a SessionEnd Hook for Comprehensive Cleanup

Create a Python script for robust session cleanup:

```python
#!/usr/bin/env python3
# File: ~/.claude/hooks/session_cleanup.py

import sys
import json
import os
import shutil
from datetime import datetime
from pathlib import Path

def main():
    # Read JSON input from stdin
    input_data = json.loads(sys.stdin.read())

    session_id = input_data.get("session_id", "unknown")
    transcript_path = input_data.get("transcript_path", "")
    reason = input_data.get("reason", "unknown")
    cwd = input_data.get("cwd", "")

    log_file = Path.home() / ".claude" / "session_cleanup.log"

    # Log session end
    with open(log_file, "a") as f:
        f.write(f"[{datetime.now()}] Session {session_id} ended. Reason: {reason}\n")

    # Clean up temporary files
    temp_dir = Path("/tmp") / f"claude-temp-{session_id}"
    if temp_dir.exists():
        try:
            shutil.rmtree(temp_dir)
            print(f"Cleaned up temporary directory: {temp_dir}")
        except Exception as e:
            print(f"Error cleaning temp directory: {e}", file=sys.stderr)

    # Archive transcript if path provided
    if transcript_path and os.path.exists(transcript_path):
        archive_dir = Path.home() / ".claude" / "archives"
        archive_dir.mkdir(parents=True, exist_ok=True)

        archive_name = f"session_{session_id}_{datetime.now().strftime('%Y%m%d_%H%M%S')}.md"
        archive_path = archive_dir / archive_name

        try:
            shutil.copy2(transcript_path, archive_path)
            print(f"Archived transcript to: {archive_path}")
        except Exception as e:
            print(f"Error archiving transcript: {e}", file=sys.stderr)

    # Kill background processes associated with session
    pid_file = Path("/tmp") / f"claude-session-{session_id}.pid"
    if pid_file.exists():
        try:
            with open(pid_file, "r") as f:
                pid = f.read().strip()
            os.system(f"kill {pid} 2>/dev/null")
            pid_file.unlink()
            print(f"Stopped background process: {pid}")
        except Exception as e:
            print(f"Error stopping background process: {e}", file=sys.stderr)

    # Return success
    sys.exit(0)

if __name__ == "__main__":
    main()
```

Make the script executable:

```bash
chmod +x ~/.claude/hooks/session_cleanup.py
```

### Step 5: Understand Hook Input Data Format

Both Stop and SessionEnd hooks receive JSON input via stdin with the following structure:

**Stop Hook Input**:
```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.md",
  "hook_event_name": "Stop",
  "cwd": "/current/working/directory",
  "permission_mode": "ask"
}
```

**SessionEnd Hook Input**:
```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.md",
  "hook_event_name": "SessionEnd",
  "cwd": "/current/working/directory",
  "permission_mode": "ask",
  "reason": "clear"
}
```

**SessionEnd Reason Values**:
- `"clear"`: Session cleared with /clear command
- `"logout"`: User logged out
- `"prompt_input_exit"`: User exited during prompt input
- `"other"`: Other exit reasons

### Step 6: Implement Advanced JSON Response Control

Stop hooks can return JSON to stdout for sophisticated control:

```bash
#!/bin/bash
# Advanced Stop hook with conditional blocking

INPUT=$(cat)
SESSION_ID=$(echo "$INPUT" | jq -r '.session_id')

# Check if tests passed (example logic)
if [ -f "/tmp/tests_failed_${SESSION_ID}" ]; then
    # Block stopping and provide guidance
    cat <<EOF
{
  "decision": "block",
  "reason": "Tests failed. Please fix failing tests before completing.",
  "systemMessage": "Test suite validation required",
  "suppressOutput": false
}
EOF
    exit 2
else
    # Allow normal stop
    cat <<EOF
{
  "decision": "continue",
  "suppressOutput": true
}
EOF
    exit 0
fi
```

**JSON Response Fields**:
- `decision`: "continue" (allow stop) or "block" (prevent stop)
- `reason`: Message shown when blocking
- `systemMessage`: Optional warning message to Claude
- `suppressOutput`: Hide stdout from transcript (true/false)

### Step 7: Test Your Hooks

Test the Stop hook by having Claude complete a task:

```bash
# Start a Claude Code session and run a simple task
# The Stop hook should trigger when Claude finishes

# Check the log file
cat ~/.claude/task_log.txt
```

Test the SessionEnd hook by ending a session:

```bash
# End your Claude Code session (Ctrl+D or /clear)
# Check the cleanup log
cat ~/.claude/session_cleanup.log

# Verify temp files were removed
ls /tmp/claude-temp-* 2>/dev/null

# Check archived transcripts
ls -l ~/.claude/archives/
```

### Step 8: Implement Common Cleanup Patterns

**Pattern 1: Resource Cleanup**
```python
# Clean up Docker containers created during session
import subprocess

container_prefix = f"claude-{session_id}"
result = subprocess.run(
    ["docker", "ps", "-a", "--filter", f"name={container_prefix}", "-q"],
    capture_output=True, text=True
)
container_ids = result.stdout.strip().split('\n')
for cid in container_ids:
    if cid:
        subprocess.run(["docker", "rm", "-f", cid])
```

**Pattern 2: State Persistence**
```python
# Save session state to database
import sqlite3

db_path = Path.home() / ".claude" / "sessions.db"
conn = sqlite3.connect(db_path)
cursor = conn.cursor()
cursor.execute("""
    INSERT INTO sessions (session_id, end_time, reason, transcript_path)
    VALUES (?, ?, ?, ?)
""", (session_id, datetime.now(), reason, transcript_path))
conn.commit()
conn.close()
```

**Pattern 3: Conditional Cleanup**
```bash
# Only archive if session lasted more than 5 minutes
START_TIME=$(cat "/tmp/session_${SESSION_ID}_start" 2>/dev/null || echo "0")
END_TIME=$(date +%s)
DURATION=$((END_TIME - START_TIME))

if [ $DURATION -gt 300 ]; then
    # Archive long sessions
    cp "$TRANSCRIPT_PATH" "$ARCHIVE_DIR/important_session_${SESSION_ID}.md"
fi
```

## Expected Results

After implementing Stop hooks for cleanup operations, you should observe:

1. **Automated Cleanup**: Temporary files, directories, and processes are automatically removed when sessions end
2. **Session Logging**: Complete audit trail of all sessions with timestamps, reasons, and durations
3. **Transcript Archiving**: Important conversation transcripts automatically saved to designated locations
4. **Resource Management**: Docker containers, background processes, and other resources properly terminated
5. **Quality Gates**: Stop hooks can enforce quality standards before allowing task completion
6. **Notifications**: Desktop or system notifications when tasks complete or sessions end
7. **Clean Environment**: No orphaned files or processes after Claude Code sessions

The hooks should execute silently in the background without interrupting your workflow.

## Troubleshooting

### Common Issue 1: Hook Script Not Executing
**Problem**: Hook configured but script never runs
**Solution**:
- Verify script path is absolute (not relative) in settings.json
- Ensure script has executable permissions: `chmod +x /path/to/script.sh`
- Check script shebang line (#!/bin/bash or #!/usr/bin/env python3)
- Review Claude Code logs for hook errors

### Common Issue 2: "Stop hook error" Message Appears Repeatedly
**Problem**: Persistent error messages after every operation
**Solution**:
- Check that your script exits with code 0 (success) or 2 (block)
- Ensure script doesn't hang or timeout (add timeout handling)
- Verify all dependencies (jq, python modules) are installed
- Add error handling to catch and log exceptions
- Check that stdin is being read correctly

### Common Issue 3: SessionEnd Hook Not Firing
**Problem**: SessionEnd hook doesn't execute when session ends
**Solution**:
- Verify Claude Code version is 1.0.85 or later (when SessionEnd was added)
- Check that the hook name is exactly "SessionEnd" (case-sensitive)
- Test with different exit methods: /clear, Ctrl+D, logout
- Review settings.json for syntax errors (validate JSON)
- Ensure no blocking errors in other hooks preventing execution

### Common Issue 4: Unable to Block Stopping with Exit Code 2
**Problem**: Stop hook returns exit code 2 but Claude continues anyway
**Solution**:
- Note that blocking behavior may have changed in recent versions (July 2025 reports indicated blocking was not working as expected)
- Use JSON response format with "decision": "block" instead of relying solely on exit codes
- Verify you're using Stop hook, not SessionEnd (SessionEnd cannot block)
- Check Claude Code version for known blocking issues
- Consider using PreToolUse hook for validation instead

### Common Issue 5: Cleanup Script Fails to Access Files
**Problem**: Script cannot find or delete temporary files
**Solution**:
- Use absolute paths instead of relative paths in cleanup logic
- Verify file permissions allow deletion
- Check that session_id matches the actual temp file naming convention
- Add existence checks before operations: `if os.path.exists(path)`
- Log file paths to debug: `print(f"Attempting to delete: {temp_dir}")`

## Additional Tips

- **Start Simple**: Begin with basic logging hooks before implementing complex cleanup logic
- **Use Blocking Carefully**: Only use exit code 2 (blocking) in Stop hooks when absolutely necessary, as it can create infinite loops if not handled properly
- **Set Default Blocking to False**: Stop hooks use `blocking: false` by default to prevent infinite loops - this is recommended
- **Test Incrementally**: Test each cleanup operation independently before combining them
- **Log Everything**: Add comprehensive logging to debug hook execution and verify cleanup operations
- **Handle Errors Gracefully**: Always include try-catch blocks to prevent hook failures from breaking sessions
- **Archive Selectively**: Don't archive every transcript - use conditional logic based on session duration or content
- **Monitor Resource Usage**: Track disk space usage if archiving transcripts regularly
- **Use Session IDs**: Leverage session_id to uniquely identify and clean up session-specific resources
- **Combine with SessionStart**: Pair cleanup hooks with SessionStart hooks to create complete lifecycle management
- **Version Control Hooks**: Store hook scripts in git repositories for team sharing and version history
- **Environment Variables**: Set environment-specific paths using ENV variables for portability
- **Timeout Protection**: Add timeouts to cleanup operations to prevent hanging sessions
- **Idempotent Operations**: Design cleanup scripts to be safely re-runnable without side effects

## Related Articles

- KB-016: How to start a new session
- KB-018: How to pause and resume sessions
- KB-020: How to access session history and logs
- KB-064: How to use SessionStart hooks for initialization (if available)

## Sources

- Hooks Reference - Claude Docs - https://docs.claude.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
- Hooks Reference - Claude Code Docs - https://code.claude.com/docs/en/hooks (Accessed: November 16, 2025)
- GitHub Issue #6306: Document the SessionEnd Hook - https://github.com/anthropics/claude-code/issues/6306 (Accessed: November 16, 2025)
- GitHub Repository: claude-code-hooks-mastery - https://github.com/disler/claude-code-hooks-mastery (Accessed: November 16, 2025)
- Claude Code Hooks: The Secret Sauce for Bulletproof Dev Automation - https://garysvenson09.medium.com/claude-code-hooks-the-secret-sauce-for-bulletproof-dev-automation-e18eadb09ad6 (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code hooks types list Stop SessionEnd 2025"
- Google Search Suggestion: "Claude Code SessionEnd hook example bash python cleanup temporary files"

---
*This article is part of the Claude Code Web knowledge base*
