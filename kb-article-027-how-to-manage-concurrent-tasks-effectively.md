# How to manage concurrent tasks effectively

**Article ID**: KB-027
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15 minutes

## Overview
Claude Code Web allows you to run multiple coding tasks simultaneously, enabling parallel development workflows that dramatically increase productivity. This guide teaches you how to effectively manage concurrent tasks, balance your workload across multiple sessions, and maximize your plan's concurrent execution limits.

## Prerequisites
- Active Claude Pro (3 concurrent tasks) or Max (10+ concurrent tasks) subscription
- Understanding of how to create and monitor tasks (KB-006, KB-008)
- Familiarity with viewing active sessions (KB-017)
- At least one connected repository

## Understanding Concurrent Task Management

### What Are Concurrent Tasks?

Concurrent tasks in Claude Code Web are independent coding sessions that run simultaneously in isolated sandbox environments. Each task has its own:
- Dedicated computing resources
- Isolated file system and environment
- Independent progress tracking
- Separate activity log and history

### Benefits of Concurrent Execution

**Parallel Workflows:**
```
Traditional Serial Approach (60 minutes total):
Task 1: Bug fix → Wait for completion (15 min)
Task 2: Documentation → Wait for completion (20 min)
Task 3: Feature addition → Wait for completion (25 min)

Concurrent Approach (25 minutes total):
Task 1: Bug fix ─────────┐
Task 2: Documentation ───┼─→ All complete at 25 min
Task 3: Feature addition ┘
```

**Use Cases for Concurrent Tasks:**
- Running tests on one repository while developing features in another
- Fixing bugs in production while experimenting with new features
- Generating documentation while implementing code changes
- Refactoring multiple independent modules simultaneously
- Exploring different implementation approaches in parallel

## Steps

### Step 1: Assess Your Concurrent Task Capacity

Before starting multiple tasks, understand your limits:

**Check Your Plan Limits:**
1. Go to `https://claude.com/code`
2. Look for active session counter (e.g., "2 of 3 active" or "5 of 10 active")
3. Note how many slots are available

**Claude Pro (3 concurrent tasks):**
- Best for: Small teams or individual developers
- Strategy: Mix of one long-running task + two quick tasks
- Rotation: Complete quick tasks to free slots for new work

**Claude Max (10+ concurrent tasks):**
- Best for: Power users and larger teams
- Strategy: Dedicate slots by priority and duration
- Flexibility: Run entire workflows in parallel

### Step 2: Plan Your Task Distribution

**Categorize Tasks by Duration:**

**Quick Tasks (5-10 minutes):**
- Simple bug fixes
- Documentation updates
- Code formatting or linting
- Small refactoring operations
- Adding comments or improving clarity

**Medium Tasks (15-30 minutes):**
- Adding new functions or components
- Writing unit tests
- Updating configurations
- Moderate refactoring
- Integration work

**Long Tasks (30-60+ minutes):**
- Major feature implementations
- Complex refactoring across multiple files
- Running comprehensive test suites
- Large-scale migrations
- Multi-file architectural changes

**Sample Task Allocation Strategy (Pro - 3 slots):**
```
Slot 1: Major feature (60 min) - Monitor periodically
Slot 2: Quick bug fix (10 min) - Rotate frequently
Slot 3: Documentation (15 min) - Rotate frequently

As quick tasks complete, start new ones immediately
```

**Sample Task Allocation Strategy (Max - 10 slots):**
```
Slots 1-3: Long-running features (high priority)
Slots 4-6: Medium complexity tasks (standard priority)
Slots 7-9: Quick tasks (rotating rapidly)
Slot 10: Reserved for urgent issues
```

### Step 3: Start Concurrent Tasks Strategically

**Best Practices for Starting Multiple Tasks:**

**Stagger Your Task Starts:**
Don't start all tasks simultaneously. Instead:
```
1. Start Task 1, wait for it to reach "Implementing" phase (2-3 min)
2. Start Task 2, wait for it to stabilize (2-3 min)
3. Start Task 3
```

This prevents:
- Resource contention during initialization
- Overwhelming your monitoring capacity
- Difficulty identifying which task encountered issues

**Choose Independent Tasks:**

**Good Concurrent Task Combinations:**
- ✅ Different repositories
- ✅ Different modules in same repository
- ✅ Frontend + Backend work
- ✅ Implementation + Testing
- ✅ Code changes + Documentation

**Avoid These Combinations:**
- ❌ Multiple tasks modifying the same file
- ❌ Tasks with dependencies on each other
- ❌ Tasks in same codebase area that might conflict
- ❌ Multiple complex tasks requiring frequent intervention

**Practical Example:**
```
Good Strategy:
Task 1: Fix authentication bug in auth.js
Task 2: Update README documentation
Task 3: Add new API endpoint in api-routes.js

Poor Strategy:
Task 1: Refactor user authentication module
Task 2: Add new authentication method
Task 3: Update authentication tests
^ These might conflict or have dependencies
```

### Step 4: Monitor Concurrent Tasks Effectively

**Dashboard Overview Method:**

1. Keep the active sessions view open: `https://claude.com/code`
2. Scan the dashboard every 10-15 minutes
3. Look for status indicators:
   - **Green/Running**: Task progressing normally
   - **Yellow/Waiting**: Needs your input
   - **Red/Error**: Encountered a problem
   - **Completing**: Nearly finished

**Priority Monitoring Approach:**

**High Priority Tasks:**
- Check every 5-10 minutes
- Respond immediately to input requests
- Monitor for errors closely

**Medium Priority Tasks:**
- Check every 15-20 minutes
- Review at natural breakpoints
- Let them run independently

**Low Priority Tasks:**
- Check when convenient
- Review after completion
- Minimal active monitoring

**Create a Monitoring Routine:**
```
Every 15 minutes:
1. Open active sessions dashboard
2. Scan all task statuses
3. Respond to any "Waiting for Input" tasks
4. Check elapsed time on long-running tasks
5. Cancel any stuck tasks (no progress for 10+ min)
6. Start new tasks in freed slots
```

### Step 5: Use the Built-in Todo System for Complex Tasks

Claude Code Web automatically creates todo lists for complex multi-step tasks. Leverage this for better concurrent task management:

**What the Todo System Provides:**

**Task Breakdown:**
When you create a task, Claude automatically breaks it into steps:
```
Task: "Add user authentication system"

Auto-generated todos:
☐ Create user model and database schema
☐ Implement password hashing utility
☐ Build registration endpoint
☐ Create login endpoint with JWT
☐ Add authentication middleware
☐ Write tests for auth flows
```

**Real-time Progress Tracking:**
- ✓ Completed steps are marked
- ▶ Current step shows "in progress"
- ☐ Pending steps wait in queue

**Benefits for Concurrent Management:**
- Quick visual assessment of task progress
- Understand what phase each concurrent task is in
- Estimate time remaining
- Identify stuck tasks (same todo item for 10+ minutes)

**Viewing Todo Status:**
1. Click into any active session
2. Look for the todo list section (usually near top or side panel)
3. See which step is currently executing
4. Estimate remaining work based on uncompleted todos

### Step 6: Handle Task Completion and Slot Rotation

**When Tasks Complete:**

**Immediate Actions:**
1. Review the changes made
2. Check the commit message and files modified
3. Verify the task achieved its goal
4. Close or archive the completed session

**Free Up Slots Quickly:**
Once you've reviewed a completed task:
- Don't let completed tasks occupy your view
- Start the next queued task immediately
- Maintain high throughput by rapid slot rotation

**Completion Workflow:**
```
Task completes → Review (2 min) → Start next task → Monitor initialization

This keeps your concurrent slots fully utilized
```

### Step 7: Handle Intervention and Course Correction

**When Claude Asks for Input:**

**Prioritize Response:**
1. Tasks waiting for input are blocked
2. Respond quickly to unblock progress
3. Don't let multiple tasks pile up in "waiting" state

**Typical Input Requests:**
- Clarification on requirements
- Choice between multiple implementation approaches
- Permission for significant changes
- Resolution of ambiguous specifications

**Response Strategy:**
```
If 2+ tasks need input simultaneously:
1. Respond to highest priority task first
2. Then handle others in order of importance
3. Consider cancelling low-priority blocked tasks
```

**When Tasks Encounter Errors:**

**Triage Process:**
1. Open the task detail view
2. Read the error message in activity log
3. Decide: Guide Claude to fix, or cancel and restart?

**Guide Claude During Execution:**
- Use the chat/input to provide additional context
- Point to relevant files or documentation
- Suggest alternative approaches
- Clarify misunderstood requirements

See KB-022 for detailed guidance on intervening during task execution.

### Step 8: Optimize Your Concurrent Workflow

**Advanced Strategies:**

**Task Batching:**
Group similar tasks together:
```
Morning Batch (3 tasks):
- Fix bugs reported overnight
- All quick fixes in same codebase
- Complete before starting feature work

Afternoon Batch (3 tasks):
- New feature implementations
- Longer duration tasks
- Less monitoring required
```

**Pipeline Approach:**
Create a continuous pipeline:
```
Queue: [Task A, Task B, Task C, Task D, Task E, Task F]

Active:
Slot 1: Task A (running)
Slot 2: Task B (running)
Slot 3: Task C (running)

When Task A completes → Start Task D
When Task B completes → Start Task E
When Task C completes → Start Task F

Maintains constant 3/3 slot utilization
```

**Repository-Based Organization:**
```
With Max (10 slots):
Slots 1-3: Main product repository
Slots 4-5: Documentation repository
Slots 6-7: API services repository
Slots 8-9: Testing repository
Slot 10: Flex slot for urgent work

Organizes work by codebase
```

**Time-Based Rotation:**
```
Set time blocks:
9:00-10:00: Start 3 quick tasks
10:00-12:00: Monitor completions, rotate in new tasks
12:00-13:00: Review all completed work
13:00-14:00: Start 3 longer tasks for afternoon
```

## Expected Results

When effectively managing concurrent tasks, you should:
- Maximize your plan's concurrent slot utilization (approaching 100%)
- Complete 2-3x more tasks per day compared to serial execution
- Reduce idle time waiting for task completion
- Maintain awareness of all active sessions and their status
- Quickly identify and respond to tasks needing attention
- Experience smooth task rotation as slots free up
- Achieve parallel progress across multiple codebases or features
- Feel confident managing multiple sessions simultaneously

## Troubleshooting

### Common Issue 1
**Problem**: All concurrent slots filled but nothing completing
**Solution**:
- Check if all tasks are genuinely long-running or stuck
- Review activity logs for each task (look for no updates in 5+ min)
- Identify and cancel stuck tasks
- Consider if tasks are too complex and need breaking down
- Reduce concurrent task count and use longer serial execution for complex work
- Ensure tasks don't have circular dependencies

### Common Issue 2
**Problem**: Losing track of which tasks are doing what
**Solution**:
- Use descriptive task names when starting sessions
- Check the todo list for each task to see current step
- Keep a personal notepad with task priorities and goals
- Use the repository filter to view tasks by codebase
- Don't run more concurrent tasks than you can mentally track
- For Pro users: 3 tasks is manageable; for Max: start with 5-7 before scaling to 10

### Common Issue 3
**Problem**: Multiple tasks waiting for input simultaneously
**Solution**:
- This indicates task descriptions weren't specific enough
- Provide more detailed requirements when starting tasks (see KB-007)
- Respond to input requests in priority order
- Consider cancelling low-priority blocked tasks
- For remaining tasks, provide comprehensive answers to unblock
- In future, include more context upfront to reduce clarification needs

### Common Issue 4
**Problem**: Concurrent tasks interfering with each other (merge conflicts)
**Solution**:
- This happens when tasks modify overlapping code
- Cancel one of the conflicting tasks
- Complete the first task fully, merge changes
- Then restart the second task with updated codebase
- In future, choose more independent tasks for concurrent execution
- Use different branches or different repositories for concurrent work

### Common Issue 5
**Problem**: Can't start new task despite showing available slots
**Solution**:
- Tasks may be in cleanup/teardown phase
- Refresh the page to get updated status
- Wait 1-2 minutes for complete cleanup
- Check for "ghost" sessions in different repository views
- Log out and back in if issue persists
- Contact support if slots remain blocked after refresh

### Common Issue 6
**Problem**: Overwhelmed by monitoring too many concurrent tasks
**Solution**:
- Reduce concurrent task count to comfortable level
- Use the priority monitoring approach (Step 4)
- Choose longer-running tasks that need less intervention
- Set phone timer for regular check-ins rather than constant monitoring
- Focus on 1-2 high-priority tasks, let others run independently
- Remember: you don't need to watch every second of execution

## Additional Tips

### Maximizing Productivity

**Focus on Throughput, Not Just Parallelism:**
- Running 3 well-chosen tasks is better than 10 poorly-chosen ones
- Quality of task selection matters more than quantity
- Choose tasks you can actually monitor effectively

**Use Concurrent Tasks for Different Types of Work:**
```
Cognitive Load Distribution:
- High focus: Complex feature implementation
- Medium focus: Bug fixes or refactoring
- Low focus: Documentation or formatting tasks

Run 1 high + 1 medium + 1 low simultaneously
Reduces mental fatigue while maintaining productivity
```

### Task Independence Is Key

**Ensure Tasks Are Truly Independent:**
- Different files or modules
- Different branches (if supported)
- Different repositories
- No shared dependencies
- Can complete in any order

### Learning Curve

**Start Small and Scale Up:**
```
Week 1: Run 2 concurrent tasks max
Week 2: Try 3 concurrent tasks
Week 3: Add 4-5 concurrent tasks (Max users)
Week 4: Optimize and find your comfortable concurrent limit
```

### Common Patterns

**Bug Fixing Sprint:**
```
Run 3 concurrent bug fix tasks:
- Each targets different module
- All relatively quick (10-15 min each)
- Rotate in new bugs as each completes
- Can address many bugs in short time
```

**Feature Development:**
```
Main feature in Slot 1 (60 min)
Supporting tasks in Slots 2-3:
- Documentation for feature
- Tests for feature
- Related code cleanup

All complete around same time
```

**Multi-Repository Work:**
```
Update API in Backend repo (Slot 1)
Update Frontend to use new API (Slot 2)
Update Documentation (Slot 3)

Coordinated release across repositories
```

### Notification and Monitoring Tools

**Set Up External Reminders:**
- Phone timer every 15 minutes to check dashboard
- Calendar blocks for task review
- Browser tab with active sessions always open
- Consider second monitor for task monitoring

### Resource Management

**Be Aware of Compute Resources:**
- Each concurrent task uses cloud computing resources
- Very complex tasks (large builds, comprehensive test suites) may be resource-intensive
- If tasks seem slower than usual, consider reducing concurrent count
- Balance throughput with task performance

### Communication with Team

**If Using Claude Code Web in a Team:**
- Communicate which repositories you're working on
- Coordinate to avoid concurrent work on same files
- Share completion status in team channels
- Use branch naming to indicate automated Claude sessions

## Related Articles
- KB-005: How to understand the difference between Pro and Max concurrent limits
- KB-006: How to create your first coding task in Claude Code Web
- KB-007: How to describe a task effectively for Claude to understand
- KB-008: How to monitor a running task in real-time
- KB-010: How to cancel a running task if needed
- KB-017: How to view all your active sessions
- KB-022: How to intervene and guide Claude mid-session when it encounters issues

## Sources
1. Claude Code on the Web - Anthropic Official Announcement - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
2. How I'm Using Claude Code On Web To Run Multiple Coding Tasks in Parallel - Medium - https://medium.com/@joe.njenga/how-im-using-claude-code-on-the-web-new-feature-to-run-multiple-coding-tasks-in-parallel-3cd7f47931a6 (Accessed: November 16, 2025)
3. Todo Lists - Claude Docs - https://docs.claude.com/en/docs/agent-sdk/todo-tracking (Accessed: November 16, 2025)
4. Claude Code Subagent Deep Dive - Code Centre - https://cuong.io/blog/2025/06/24-claude-code-subagent-deep-dive (Accessed: November 16, 2025)
5. Embracing the Parallel Coding Agent Lifestyle - Simon Willison - https://simonwillison.net/2025/Oct/5/parallel-coding-agents/ (Accessed: November 16, 2025)
6. Claude Code Background Tasks - Apidog Blog - https://apidog.com/blog/claude-code-background-tasks/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
