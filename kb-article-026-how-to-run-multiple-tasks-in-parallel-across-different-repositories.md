# How to Run Multiple Tasks in Parallel Across Different Repositories

**Article ID**: KB-026
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Claude Code Web's parallel execution capability allows you to run multiple coding tasks simultaneously across different repositories. This feature transforms Claude from a single-task assistant into a fleet of independent coding agents, enabling you to delegate multiple pieces of work and review results as they complete.

## Prerequisites
- Active Claude Pro or Max subscription
- Understanding of sessions and task creation (KB-016, KB-006)
- At least two connected repositories (KB-004)
- Familiarity with viewing active sessions (KB-017)
- Understanding of concurrent task limits (KB-005)

## Understanding Parallel Task Execution

### What is Parallel Execution?

**Parallel execution means:**
- Multiple sessions running at the same time
- Each session isolated in its own sandbox environment
- Tasks execute independently without interference
- Work distributed across different repositories
- Asynchronous completion—tasks finish when ready

### Key Capabilities

**Claude Code Web enables:**
- Pro users: Up to 3 concurrent tasks simultaneously
- Max users: Up to 10+ concurrent tasks simultaneously
- Cross-repository workflows
- Independent branch creation per task
- Isolated environments for each session
- Parallel PR generation

### Why Run Tasks in Parallel?

**Benefits:**
- **Faster delivery**: Multiple features ship simultaneously
- **Efficient resource use**: Maximize your concurrent slot allocation
- **Batch operations**: Fix multiple bugs across repositories at once
- **Independent workflows**: Frontend and backend work in parallel
- **Reduced waiting**: Don't block on long-running tasks

**Example Scenario:**
```
Instead of: Fix Bug A → Wait → Fix Bug B → Wait → Add Feature C
Parallel:   Fix Bug A + Fix Bug B + Add Feature C (all at once)
Result:     3x faster completion
```

## Steps to Run Multiple Tasks in Parallel

### Step 1: Plan Your Parallel Workflow

**Before starting multiple sessions, identify:**

1. **Which tasks are independent**
   - Tasks that don't depend on each other
   - Work in different files or repositories
   - Can be reviewed separately

2. **Available concurrent slots**
   - Pro: 3 slots maximum
   - Max: 10+ slots available
   - Check current active sessions

3. **Task priorities**
   - Which tasks are most urgent
   - Which can run longer unsupervised
   - Which need closer monitoring

**Example Planning:**
```
Available: 3 concurrent slots (Pro plan)

Task List:
✓ Task A: Fix login bug in main-app repo (Priority: High, Time: ~10 min)
✓ Task B: Add API endpoint in backend-api repo (Priority: Medium, Time: ~20 min)
✓ Task C: Update documentation in docs repo (Priority: Low, Time: ~15 min)

Decision: Run all three in parallel—they're independent and fit within limits
```

### Step 2: Start Your First Task

**Create the first session:**

1. Navigate to `https://claude.com/code`
2. Select your first repository
3. Click "New Task" or "Start Coding"
4. Enter a clear, specific task description:
   ```
   Fix the login validation bug in src/auth/login.js where
   email validation incorrectly rejects valid Gmail addresses
   ```
5. Click "Start Task"
6. Session 1 begins—occupies slot 1 of 3

**What happens:**
- Session initializes in isolated sandbox
- Repository cloned to session environment
- Claude begins analyzing the codebase
- You can immediately start the next task

### Step 3: Start Your Second Task (Different Repository)

**Without waiting for Task 1 to complete:**

1. Return to the main dashboard or repository list
2. Select a different repository
3. Click "New Task" for this repository
4. Enter the task description:
   ```
   Create a new POST endpoint at /api/v1/users/profile that
   accepts name, email, and bio fields. Include input validation
   and error handling.
   ```
5. Click "Start Task"
6. Session 2 begins—occupies slot 2 of 3

**Key point:** You don't need to wait for Session 1 to finish. Each session runs independently.

### Step 4: Start Your Third Task (Another Repository)

**Continue adding parallel tasks:**

1. Navigate to your third repository
2. Create a new task:
   ```
   Update the API documentation in docs/api-reference.md to
   include the new authentication flow with code examples
   ```
3. Start the session
4. Session 3 begins—occupies slot 3 of 3

**Current state:**
```
Active Sessions: 3 of 3
├─ Session 1: main-app (login bug fix) - Running
├─ Session 2: backend-api (new endpoint) - Running
└─ Session 3: docs (API docs update) - Running
```

### Step 5: Monitor All Active Sessions

**View all running tasks:**

1. Go to "Active Sessions" or dashboard view
2. See all three sessions listed with status indicators
3. Each shows:
   - Repository name
   - Task description
   - Current status (Analyzing, Implementing, Testing)
   - Elapsed time
   - Quick actions (View, Cancel)

**Example dashboard view:**
```
╔════════════════════════════════════════════════╗
║ Active Sessions (3 of 3)                       ║
╠════════════════════════════════════════════════╣
║ 🟢 main-app                                    ║
║    Fix login validation bug                    ║
║    Status: Implementing | 5 min elapsed        ║
╠════════════════════════════════════════════════╣
║ 🟢 backend-api                                 ║
║    Create POST /api/v1/users/profile           ║
║    Status: Analyzing | 2 min elapsed           ║
╠════════════════════════════════════════════════╣
║ 🟢 docs                                        ║
║    Update API documentation                    ║
║    Status: Implementing | 3 min elapsed        ║
╚════════════════════════════════════════════════╝
```

### Step 6: Respond to Waiting Sessions

**If a session needs input:**

When Claude needs your guidance, a session shows "Waiting for Input":

```
🟡 backend-api
   Create POST /api/v1/users/profile
   Status: Awaiting Response | 8 min elapsed
```

**To respond:**
1. Click on the waiting session
2. Read Claude's question or proposed approach
3. Provide your input or approval
4. Session continues automatically
5. Return to monitoring other sessions

**Other sessions continue running** while you interact with one.

### Step 7: Review Completed Sessions

**As tasks finish:**

Sessions complete asynchronously. The first to finish might not be the first started.

**Example completion order:**
```
Time    Event
10 min: Session 1 (main-app) completes ✓
15 min: Session 3 (docs) completes ✓
22 min: Session 2 (backend-api) completes ✓
```

**For each completed session:**
1. You'll see a completion notification
2. Session moves to "Completed" status
3. Concurrent slot becomes available
4. You can start a new task in that freed slot

**Review the results:**
1. Click on completed session
2. Review changes made
3. Check the branch and commits
4. Review any generated PR
5. Approve or request modifications

### Step 8: Scale Up with More Repositories

**For Max users with 10+ slots:**

You can run even larger parallel workflows:

**Example enterprise workflow:**
```
Active Sessions: 8 of 10

Frontend Repositories:
├─ Session 1: web-app (feature: user profiles)
├─ Session 2: mobile-app (feature: push notifications)
└─ Session 3: admin-dashboard (bugfix: data table sorting)

Backend Repositories:
├─ Session 4: auth-service (feature: OAuth integration)
├─ Session 5: api-gateway (bugfix: rate limiting)
└─ Session 6: payment-service (feature: refund workflow)

Infrastructure:
├─ Session 7: terraform-configs (update: EKS cluster)
└─ Session 8: monitoring (feature: custom dashboards)

Available: 2 slots for urgent tasks
```

### Step 9: Manage Slot Availability

**Strategic slot management:**

**Keep 1-2 slots free** for urgent work:
```
Pro (3 slots):
- Use 2 for planned work
- Keep 1 free for urgent bugs

Max (10 slots):
- Use 7-8 for planned work
- Keep 2-3 free for urgent requests
```

**Rotate quick tasks:**
When a 5-minute task completes, immediately start another:
```
Time-based rotation:
09:00 - Start Task A (5 min), Task B (20 min), Task C (15 min)
09:05 - Task A done → Start Task D (10 min)
09:15 - Task C done → Start Task E (8 min)
09:20 - Task B done → Start Task F (12 min)
```

### Step 10: Advanced Patterns

**Same Repository, Multiple Tasks:**

You can run multiple sessions in the same repository:

```
Repository: main-application
├─ Session 1: Feature A on branch claude/feature-a
├─ Session 2: Feature B on branch claude/feature-b
└─ Session 3: Bug fix C on branch claude/bugfix-c

Each gets its own branch—no conflicts
```

**Batch Operations:**

Fix the same issue across multiple repositories:

```
Task: "Update all API calls to use the new authentication header"

Parallel sessions:
├─ frontend-web
├─ frontend-mobile
├─ admin-panel
└─ internal-tools

All updated simultaneously with consistent changes
```

**Coordinated Release:**

Prepare multiple repositories for a release:

```
Release v2.0 preparation:
├─ Update version numbers (4 repos)
├─ Update changelogs (4 repos)
├─ Update documentation (2 repos)
└─ Create release tags (coordination after completion)

10 parallel sessions complete the prep work
```

## Expected Results

After successfully running tasks in parallel, you should:

- See multiple sessions active simultaneously in your dashboard
- Have each session showing independent progress
- Receive completion notifications as each task finishes
- Find separate branches created for each task
- Have multiple PRs ready for review (if auto-PR enabled)
- Experience faster overall completion than sequential execution
- Maintain isolated environments with no cross-task interference

**Success indicators:**
```
✓ All planned sessions started successfully
✓ Each session shows active progress
✓ No interference between sessions
✓ Tasks complete independently
✓ Results match expectations for each task
✓ Concurrent slots used efficiently
✓ Time saved compared to sequential execution
```

## Troubleshooting

### Common Issue 1
**Problem**: Cannot start a new parallel task—button disabled
**Solution**:
- You've reached your concurrent task limit
- Pro: 3 sessions max | Max: 10+ sessions max
- Check active sessions count in dashboard
- Wait for a session to complete, or
- Cancel a lower-priority session to free a slot
- Verify your subscription is active and not expired

### Common Issue 2
**Problem**: Started tasks in wrong repositories
**Solution**:
- Cancel the incorrect sessions immediately (KB-010)
- Sessions can be cancelled even while initializing
- Freed slots become available within 30-60 seconds
- Double-check repository selection before starting new sessions
- Use repository names in task descriptions for clarity
- Consider creating a checklist before batch-starting tasks

### Common Issue 3
**Problem**: One session is waiting for input, blocking my workflow
**Solution**:
- Other sessions continue running—this doesn't block them
- Respond to the waiting session when you have time
- Session will pause until you provide input
- No time limit—session waits indefinitely
- You can cancel if you no longer need the task
- Consider providing more context in initial task description to reduce interruptions

### Common Issue 4
**Problem**: Difficult to track which session is doing what
**Solution**:
- Use very specific, descriptive task descriptions
- Include repository name in description if helpful
- Good: "Fix login bug in auth/login.js - email validation"
- Bad: "Fix bug"
- Sort sessions by repository or time started
- Click into sessions individually to see detailed progress
- Keep notes outside Claude for complex parallel workflows

### Common Issue 5
**Problem**: Multiple sessions failed or got stuck
**Solution**:
- Click into each stuck session to see error details
- Common causes: missing dependencies, repository issues, ambiguous tasks
- Cancel stuck sessions to free up slots
- Review session logs to understand what went wrong
- Restart with clearer task descriptions
- Check repository health (builds passing, dependencies installed)
- Contact support if multiple sessions consistently fail

### Common Issue 6
**Problem**: Sessions completing in unexpected order
**Solution**:
- This is normal—tasks complete when done, not in start order
- Task complexity varies
- Some repositories are larger and slower to analyze
- Review completed sessions as they finish
- Don't wait for all to complete to start reviewing
- Use session notifications to stay informed

### Common Issue 7
**Problem**: Running out of concurrent slots too quickly
**Solution**:
- Prioritize tasks—start most important ones first
- Cancel speculative or low-priority sessions
- Upgrade from Pro (3) to Max (10+) for more concurrency
- Use sequential execution for dependent tasks
- Batch similar tasks to complete together
- Review and cancel stuck sessions regularly

## Additional Tips

### Maximizing Parallel Efficiency

**Task Selection:**
- Choose independent, non-blocking tasks
- Avoid tasks that depend on each other's results
- Mix quick and long-running tasks
- Balance monitoring requirements

**Time Management:**
```
Efficient: 3 x 20-minute tasks = 20 minutes total
Sequential: 3 x 20-minute tasks = 60 minutes total
Savings: 40 minutes (67% faster)
```

### Best Practices for Multi-Repository Workflows

**Preparation:**
- Ensure all repositories are in good state
- Up-to-date dependencies
- Passing tests before starting
- Clear task descriptions prepared in advance

**Monitoring Strategy:**
- Check dashboard every 10-15 minutes
- Respond to waiting sessions promptly
- Review completed sessions before starting more
- Keep mental or written notes of what each session is doing

**Slot Allocation Strategy:**

**For Pro (3 slots):**
```
Conservative: 1 experimental + 2 reliable tasks
Balanced: 2 planned + 1 slot kept free
Aggressive: 3 planned tasks (all slots used)
```

**For Max (10+ slots):**
```
Conservative: 5-6 tasks + 4-5 free slots
Balanced: 7-8 tasks + 2-3 free slots
Aggressive: 9-10 tasks + 1 free slot
```

### Communication with Claude

**In parallel workflows, be extra clear:**
- Each session is independent—context doesn't carry over
- Provide complete context in each task description
- Don't reference other sessions ("like in the other task")
- Include repository-specific details
- Mention relevant file paths and specifics

### Organizational Strategies

**Tag your tasks mentally:**
```
[URGENT] Fix production login bug
[FEATURE] Add user profile page
[DOCS] Update API documentation
[REFACTOR] Clean up legacy code
```

**Group by theme:**
```
Security fixes across all repos
Feature development for v2.0 release
Documentation updates
Performance optimizations
```

### When NOT to Use Parallel Execution

**Avoid parallel tasks when:**
- Tasks are dependent (Task B needs Task A's results)
- Working on the same files across sessions
- Resources are constrained (testing/build servers)
- You can't monitor multiple sessions
- Tasks require frequent interaction
- Single complex task is better than multiple simple ones

**Example—Don't parallelize:**
```
❌ Bad:
   Session 1: Design database schema
   Session 2: Implement database models (needs schema from Session 1)

✓ Better:
   Session 1: Design schema and implement models (sequential in one session)
```

### Performance Optimization

**Reduce session interference:**
- Distribute load across different repository sizes
- Mix large repos with small repos
- Stagger start times by 1-2 minutes
- Don't start 10 sessions simultaneously—start in batches

**Example staggered start:**
```
09:00 - Start sessions 1-3
09:02 - Start sessions 4-6
09:04 - Start sessions 7-9
09:06 - Start session 10

Smoother initialization, better performance
```

### Documentation and Tracking

**Keep external records:**
- Spreadsheet or doc tracking which sessions do what
- Note session IDs for important work
- Track which PRs came from which sessions
- Record any issues encountered
- Useful for teams sharing accounts

**Example tracking sheet:**
```
| Session | Repo          | Task           | Status    | PR    |
|---------|---------------|----------------|-----------|-------|
| 1       | web-app       | Login fix      | Complete  | #234  |
| 2       | api           | New endpoint   | Running   | -     |
| 3       | docs          | Update docs    | Complete  | #235  |
```

## Related Articles
- KB-016: How to start a new Claude Code session
- KB-017: How to view all your active sessions
- KB-006: How to create your first coding task in Claude Code Web
- KB-011: How to switch between different connected repositories
- KB-005: How to understand the difference between Pro and Max concurrent limits
- KB-010: How to cancel a running task if needed
- KB-008: How to monitor a running task in real-time
- KB-004: How to connect your first GitHub repository to Claude Code Web

## Sources
1. Claude Code on the web - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
2. How I'm Using Claude Code On Web (New Feature) To Run Multiple Coding Tasks in Parallel - https://medium.com/@joe.njenga/how-im-using-claude-code-on-the-web-new-feature-to-run-multiple-coding-tasks-in-parallel-3cd7f47931a6 (Accessed: November 16, 2025)
3. Claude Code comes to web and mobile, letting devs launch parallel jobs - VentureBeat - https://venturebeat.com/ai/claude-code-comes-to-web-and-mobile-letting-devs-launch-parallel-jobs-on (Accessed: November 16, 2025)
4. Embracing the parallel coding agent lifestyle - Simon Willison's Blog - https://simonwillison.net/2025/Oct/5/parallel-coding-agents/ (Accessed: November 16, 2025)
5. Claude Code for web—a new asynchronous coding agent from Anthropic - Simon Willison's Blog - https://simonwillison.net/2025/Oct/20/claude-code-for-web/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
