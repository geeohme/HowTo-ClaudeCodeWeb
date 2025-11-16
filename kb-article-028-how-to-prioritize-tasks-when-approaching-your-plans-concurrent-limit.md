# How to prioritize tasks when approaching your plan's concurrent limit

**Article ID**: KB-028
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Claude Code Web supports running multiple tasks concurrently, but each subscription plan has limits: Pro plans allow 3 concurrent tasks while Max plans support 10+ concurrent operations. When approaching your plan's concurrent limit, you need to prioritize which tasks run immediately and which should wait. This guide covers strategies for monitoring task slots, prioritizing work, and optimizing your concurrent task usage.

## Prerequisites
- Active Pro or Max subscription with Claude Code Web access
- Understanding of basic session management (see KB-016, KB-017)
- Familiarity with task monitoring (see KB-008, KB-009)
- Knowledge of how to pause/resume sessions (see KB-018)

## Steps

### Step 1: Understand Your Plan's Concurrent Limits

Before managing task priorities, verify your current plan limits:

**Pro Plan ($20/month):**
- 3 concurrent task slots
- Suitable for solo developers and small teams
- Typical workflow: 1 active PR review + 1 background test run + 1 exploratory analysis

**Max Plan ($100/month):**
- 10+ concurrent task slots
- Designed for teams running 20+ PR reviews daily
- Supports programmatic integration for CI/CD pipelines

**Important**: The internal parallelism level is capped at 10 tasks executing simultaneously. Additional tasks beyond this limit are automatically queued and start as existing tasks complete.

### Step 2: Monitor Your Current Task Usage

Track how many task slots you're currently using:

1. **Navigate to the Sessions View**
   - Click on the "Sessions" tab in the Claude Code Web interface
   - View all active sessions across repositories

2. **Count Active Sessions**
   - Each active session consumes one concurrent task slot
   - Sessions marked "Running" or "In Progress" count toward your limit
   - Paused sessions may still hold a slot depending on their state

3. **Check for Queued Tasks**
   - If you've exceeded your concurrent limit, tasks will queue automatically
   - Queued tasks appear with a "Waiting" or "Queued" status
   - The queue processes in FIFO order (First In, First Out) by default

### Step 3: Categorize Tasks by Priority

Organize your pending and active tasks into priority levels:

**High Priority (Run Immediately):**
- Critical bug fixes affecting production
- Urgent PR reviews with tight deadlines
- Security vulnerability patches
- Tasks blocking other team members

**Medium Priority (Can Wait Short Term):**
- Feature development for current sprint
- Code refactoring with moderate impact
- Documentation updates
- Non-critical test runs

**Low Priority (Can Be Deferred):**
- Exploratory code analysis
- Long-running comprehensive test suites
- Code style improvements
- Dependency updates without urgency

**Background Tasks (Best for Queuing):**
- Large-scale refactoring across multiple files
- Full codebase linting or formatting
- Comprehensive security scans
- Repository-wide search and analysis

### Step 4: Identify Task Types for Optimal Scheduling

Claude Code's scheduling system prioritizes tasks differently based on type:

**CPU-Bound Tasks (Higher Priority Scheduling):**
- Code compilation
- Test execution
- Build processes
- Linting and static analysis

These tasks receive priority scheduling from the system automatically.

**I/O-Bound Tasks (Background Thread Execution):**
- Git clone operations
- Package downloads (npm, PyPI, etc.)
- File system operations
- Network requests

These tasks run in background threads and have less impact on your concurrent limit.

### Step 5: Pause Low-Priority Active Tasks

When you need to free up task slots for higher-priority work:

1. **Identify Pausable Tasks**
   - Navigate to your active sessions
   - Look for tasks that can be safely interrupted
   - Avoid pausing tasks during critical operations (e.g., mid-git-commit)

2. **Pause the Session**
   - Click on the session you want to pause
   - Use the pause button or command
   - The h2A async dual-buffer queue supports pause/resume without requiring full restart

   **Note**: Paused sessions preserve their context and can resume exactly where they stopped, thanks to Claude Code's pause/resume architecture.

3. **Verify Slot Availability**
   - Check that the paused session has released its concurrent slot
   - You should now have an available slot for higher-priority tasks

### Step 6: Start High-Priority Tasks

With freed task slots, launch your prioritized work:

1. **Start New Session for High-Priority Task**
   - Create a new session or resume a queued high-priority task
   - Provide clear, specific instructions (see KB-007)
   - The task will begin immediately in the available slot

2. **Monitor Progress**
   - Use real-time progress tracking (see KB-009)
   - Intervene if Claude needs guidance (see KB-022)
   - Approve changes as the task progresses (see KB-023)

### Step 7: Resume Paused Tasks When Slots Become Available

After high-priority tasks complete:

1. **Check for Available Slots**
   - Monitor when tasks complete and free up slots
   - Review your paused sessions list

2. **Resume Lower-Priority Tasks**
   - Navigate to paused sessions
   - Resume tasks in order of priority
   - The session continues from where it was paused

### Step 8: Optimize Task Batching

For maximum efficiency with limited concurrent slots:

**Serial Task Strategy (1-2 Concurrent Slots):**
- Run one critical task at a time
- Queue related subtasks to execute sequentially
- Best when tasks have dependencies

**Parallel Task Strategy (3+ Concurrent Slots):**
- Run independent tasks simultaneously
- Examples:
  - Repository A: PR review
  - Repository B: Test execution
  - Repository C: Documentation update
- Best when tasks have no dependencies

**Hybrid Strategy (Mix of Serial and Parallel):**
- 1-2 slots for immediate-priority work
- Remaining slots for background/exploratory tasks
- Pause background tasks if urgent work arrives

### Step 9: Leverage Task Queuing for Overflow

When you have more tasks than concurrent slots:

1. **Submit All Tasks**
   - Don't wait for slots to free up
   - Submit high-priority tasks first
   - Submit lower-priority tasks after

2. **Let the Queue Manage Execution**
   - The system automatically queues tasks beyond your concurrent limit
   - Tasks execute in batches as slots become available
   - Parallelism cap of 10 ensures optimal resource usage

3. **Reorder Queue if Needed**
   - While the default is FIFO, you can manually pause less important running tasks
   - This effectively moves queued high-priority tasks forward
   - Resume the paused tasks after urgent work completes

### Step 10: Monitor Rate Limits

Remember that concurrent task slots are separate from rate limits:

**Rate Limit Considerations:**
- Cloud-based sessions share rate limits with all other Claude Code usage
- Pro plan: ~45 messages or 10-40 prompts every five hours
- Max 5x plan: ~225 messages or 50-200 prompts every five hours

**Impact on Task Prioritization:**
- If you hit rate limits, even with available concurrent slots, new tasks cannot start
- Prioritize tasks that make efficient use of rate limits
- Consider pausing chatty tasks that use many messages but produce limited value

## Expected Results

After implementing these prioritization strategies:

- **Optimal Slot Utilization**: Your concurrent task slots are always allocated to the highest-value work
- **Minimal Waiting Time**: Critical tasks start immediately rather than queuing behind low-priority work
- **Efficient Resource Usage**: CPU-bound and I/O-bound tasks are balanced appropriately
- **Reduced Context Switching**: Better organization means fewer interruptions to your workflow
- **Maximum Throughput**: You complete more high-value work within your plan's limits

You should see:
- Fewer instances of critical work waiting in queue
- Better visibility into what's running and what's waiting
- More strategic use of pause/resume functionality
- Improved coordination when working across multiple repositories

## Troubleshooting

### Common Issue 1: Tasks Not Starting Despite Available Slots
**Problem**: You have fewer than 3 active sessions (Pro) or 10+ sessions (Max), but new tasks won't start.
**Solution**:
- Check if you've hit your rate limit (messages per 5-hour window)
- Review session status - "paused" sessions might still hold slots
- Verify network connectivity to Claude Code Web
- Try canceling stuck sessions and restarting (see KB-010)

### Common Issue 2: Paused Tasks Not Resuming
**Problem**: When you try to resume a paused task, it stays in paused state or errors out.
**Solution**:
- Check if you have available concurrent slots (pause another task if needed)
- Verify you haven't exceeded rate limits
- If the session is old (24+ hours), context might be lost - start a new session
- Check the session logs for specific error messages (see KB-020)

### Common Issue 3: Unable to Determine Which Tasks Are Using Slots
**Problem**: The interface doesn't clearly show which sessions are consuming concurrent slots.
**Solution**:
- Look for sessions with "Running" or "In Progress" status
- Count active sessions across all repositories (switch views using KB-011)
- Paused sessions may still hold slots - verify by trying to start a new task
- Contact support if slot allocation seems incorrect

### Common Issue 4: Queue Not Processing as Expected
**Problem**: Queued tasks don't start when slots become available.
**Solution**:
- Verify that previous tasks have fully completed (not just paused)
- Check rate limits - queue won't process if limits are exceeded
- Ensure queued tasks haven't encountered errors during initialization
- Try manually starting the task if it remains queued after 5+ minutes

## Additional Tips

- **Plan Ahead**: If you know you'll need multiple concurrent slots for a sprint, schedule upgrade to Max plan in advance
- **Use Background Tasks Wisely**: I/O-bound operations like git clones and package downloads have less impact - use them for exploration
- **Communicate with Team**: If sharing a Team/Enterprise account, coordinate concurrent task usage with colleagues
- **Time-Shift Low-Priority Work**: Run exploratory analysis or comprehensive test suites during off-hours when you're not actively coding
- **Monitor Patterns**: Track which types of tasks take longest to complete and plan your concurrent usage accordingly
- **Leverage Parallelism Cap**: The 10-task parallelism cap is automatic - you can queue more than your plan limit and let the system manage execution
- **Strategic Pausing**: Use pause functionality liberally - it's designed for interruption-free resumption via the h2A queue architecture
- **Repository Organization**: Spread independent tasks across different repositories to maximize true parallelism
- **Context Preservation**: Paused tasks maintain full context, so don't hesitate to pause long-running exploratory tasks when urgent work arrives

## Related Articles
- KB-005: Understand Pro and Max limits
- KB-008: Monitor running task
- KB-009: View task progress
- KB-010: Cancel running task
- KB-016: Start new session
- KB-017: View active sessions
- KB-018: Pause and resume session
- KB-020: Access session history and logs
- KB-022: Intervene and guide Claude

## Sources
1. Claude Code on the Web - Official Announcement - https://www.anthropic.com/news/claude-code-on-the-web (Accessed: November 16, 2025)
2. Claude Code Limits Explained (2025 Edition) - https://www.truefoundry.com/blog/claude-code-limits-explained (Accessed: November 16, 2025)
3. Claude Code Usage Limits and Subscription Plans Explained (Aug 2025) - https://www.geeky-gadgets.com/claude-code-usage-limits-pricing-plans-guide-sept-2025/ (Accessed: November 16, 2025)
4. Claude Code: Subagent Deep Dive - https://cuong.io/blog/2025/06/24-claude-code-subagent-deep-dive (Accessed: November 16, 2025)
5. How Claude Code Background Tasks Are Revolutionizing Developer Workflows in 2025 - https://apidog.com/blog/claude-code-background-tasks/ (Accessed: November 16, 2025)

Google Search Suggestions for Latest Updates:
- `Claude Code Web concurrent task limits 2025`
- `Anthropic Claude Code task prioritization strategies`
- `Claude Code session management best practices`

---
*This article is part of the Claude Code Web knowledge base*
