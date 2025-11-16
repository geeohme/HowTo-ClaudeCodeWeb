# How to allocate resources between parallel tasks

**Article ID**: KB-030
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10 minutes

## Overview
When running multiple coding tasks in parallel on Claude Code Web, understanding how to effectively allocate resources across concurrent sessions helps maximize productivity and avoid bottlenecks. This guide explains Claude Code Web's automatic resource allocation, how to strategically distribute work across available task slots, and best practices for managing parallel workflows.

## Prerequisites
- Active Claude Pro or Max subscription
- Understanding of concurrent task limits (KB-005)
- Experience starting and managing sessions (KB-016, KB-017)
- At least one connected repository (KB-004)

## Understanding Resource Allocation in Claude Code Web

### Automatic Resource Management

**Claude Code Web handles resource allocation automatically:**
- Each task runs in an isolated sandbox environment
- Resources are allocated per task on Anthropic-managed infrastructure
- No manual configuration of CPU, memory, or storage needed
- Network and filesystem access are controlled by the platform

**What gets allocated automatically:**
- Compute resources for running code analysis and generation
- Memory for processing files and maintaining context
- Isolated filesystem for repository operations
- Network access through secure proxy for Git operations

### Resource Constraints

**Your primary resources are:**

**1. Concurrent Task Slots**
- Pro: 3 simultaneous tasks
- Max: 10+ simultaneous tasks
- Your most limited and valuable resource

**2. Rate Limits**
- Shared across all Claude Code usage
- Applies to API calls, file operations, and AI processing
- Can be throttled during high-demand periods
- Affects how quickly tasks complete

**3. Time and Attention**
- Your ability to monitor multiple sessions
- Responding to tasks waiting for input
- Managing context across parallel work

## Steps

### Step 1: Assess Your Available Resources

Before distributing work across parallel tasks, understand what you have available.

**Check Your Concurrent Task Capacity:**

1. Navigate to `https://claude.com/code`
2. Look for task counter showing "X/3" (Pro) or "X/10+" (Max)
3. Identify how many slots are currently available
4. Review active sessions to see what's already running

**Example Assessment:**
```
Claude Pro User:
- Total Slots: 3
- In Use: 1 (long-running feature development)
- Available: 2 slots for new work
```

**Monitor Rate Limit Status:**
- Tasks may slow down if approaching rate limits
- Check for any warnings or throttling messages
- Consider timing of task starts to spread load

### Step 2: Categorize Your Pending Tasks

Organize tasks by resource needs and priority to make informed allocation decisions.

**Task Categories by Duration:**

**Quick Tasks (5-15 minutes):**
- Bug fixes
- Lint/formatting corrections
- Documentation updates
- Simple refactoring
- Adding comments or logging

**Medium Tasks (15-45 minutes):**
- Feature additions
- Test writing
- Moderate refactoring
- Integration work
- Configuration updates

**Long Tasks (45+ minutes):**
- Major features
- Large-scale refactoring
- Migration work
- Comprehensive test suites
- Multi-file architecture changes

**Task Categories by Importance:**

**Critical:**
- Production bugs
- Security issues
- Blocking other work
- Time-sensitive fixes

**High Priority:**
- Sprint commitments
- Feature work with deadlines
- Important refactoring

**Normal Priority:**
- Technical debt
- Improvements
- Nice-to-have features

**Low Priority:**
- Experimental work
- Documentation
- Code cleanup

### Step 3: Apply Resource Allocation Strategies

Use these proven strategies to distribute work across your concurrent task slots.

#### Strategy 1: The Rotation Model (Best for Pro - 3 slots)

Combine one long-running task with rotating quick tasks.

**Setup:**
```
Slot 1: Long-running task (major feature, 60+ min)
Slot 2: Quick task rotation (bug fix, 5-10 min each)
Slot 3: Quick task rotation (documentation, 5-10 min each)
```

**Benefits:**
- Maintain progress on complex work
- Complete many quick wins
- Maximize throughput
- Efficient slot utilization

**Example Timeline:**
```
0:00  - Start Slot 1 (major feature)
0:05  - Start Slot 2 (bug fix #1)
0:10  - Start Slot 3 (docs update #1)
0:15  - Slot 2 completes, start bug fix #2
0:20  - Slot 3 completes, start docs update #2
0:25  - Slot 2 completes, start bug fix #3
...
1:00  - Slot 1 completes (major feature done)
      - 10-12 quick tasks also completed
```

#### Strategy 2: The Parallel Development Model (Best for Max - 10+ slots)

Distribute work across multiple projects or feature areas.

**Setup:**
```
Slots 1-3:  Major features (different components)
Slots 4-6:  Bug fixes and improvements
Slots 7-8:  Testing and quality assurance
Slots 9-10: Documentation and cleanup
```

**Benefits:**
- Work across multiple repositories simultaneously
- Separate concerns by type of work
- Balance different work categories
- Room for urgent tasks

**Example Allocation:**
```
Repository A:
  Slot 1: Feature X development
  Slot 4: Bug fix in component Y
  Slot 7: Integration tests

Repository B:
  Slot 2: Feature Z development
  Slot 5: Performance optimization

Repository C:
  Slot 3: API endpoint additions
  Slot 6: Security patch
  Slot 8: E2E tests
  Slot 9: API documentation
```

#### Strategy 3: The Priority Cascade Model

Allocate slots based on task priority, reserving capacity for urgent work.

**Setup for Pro (3 slots):**
```
Slot 1: Critical priority (reserved for urgent issues)
Slot 2: High priority work
Slot 3: Normal priority work
```

**Setup for Max (10 slots):**
```
Slots 1-2:  Critical (reserved for emergencies)
Slots 3-5:  High priority
Slots 6-8:  Normal priority
Slots 9-10: Low priority / Experimental
```

**Benefits:**
- Always have capacity for urgent work
- Clear prioritization
- Prevents resource starvation for important tasks
- Flexible emergency response

#### Strategy 4: The Repository-Based Model

Organize tasks by repository or project.

**Setup:**
```
Repository A (3 slots): Frontend project
  - Feature development
  - Bug fixes
  - UI tests

Repository B (3 slots): Backend services
  - API development
  - Performance tuning
  - Integration tests

Repository C (2 slots): Shared libraries
  - Dependency updates
  - Documentation

Repository D (2 slots): Infrastructure
  - Config updates
  - CI/CD improvements
```

**Benefits:**
- Logical grouping by codebase
- Easier context management
- Repository-specific progress tracking
- Reduced mental context switching

### Step 4: Start Tasks Strategically

Launch your parallel tasks in an order that maximizes efficiency.

**Launch Sequence Best Practices:**

**1. Start Long Tasks First:**
```
✓ Launch major features before quick tasks
✓ Gives long tasks maximum time to complete
✓ Quick tasks can rotate while long tasks run
```

**2. Stagger Similar Tasks:**
```
✓ Don't start 3 heavy analysis tasks simultaneously
✓ Avoid rate limit contention
✓ Spread resource-intensive operations
```

**3. Leave Buffer Capacity:**
```
Pro (3 slots):  Keep 1 slot free for urgent work
Max (10 slots): Keep 2-3 slots free for flexibility
```

**Example Optimal Startup Sequence:**
```
Time 0:00  - Start long feature task (Slot 1)
Time 0:30  - Verify Slot 1 is progressing well
Time 1:00  - Start medium bug fix (Slot 2)
Time 1:05  - Start quick documentation (Slot 3)
Time 1:15  - Slot 3 completes, start next quick task
Time 1:45  - Slot 2 completes, start next medium task
...
```

### Step 5: Monitor and Rebalance Resources

Actively manage your parallel tasks to ensure optimal resource usage.

**Continuous Monitoring:**

**Check Every 15-30 Minutes:**
1. Review active sessions (KB-017)
2. Identify tasks waiting for input
3. Check for stuck or slow tasks
4. Verify progress on long-running tasks
5. Look for completion opportunities

**Signs You Need to Rebalance:**

**Problem Signs:**
```
⚠ All slots full with long-running tasks
⚠ Multiple tasks waiting for your input
⚠ Tasks stuck with no progress for 10+ minutes
⚠ Rate limit warnings appearing
⚠ Can't start urgent work due to full slots
```

**Rebalancing Actions:**
```
✓ Cancel stuck or low-priority tasks
✓ Respond to tasks waiting for input
✓ Free slots for urgent work
✓ Restart failed tasks if needed
✓ Adjust task priorities
```

**Example Rebalancing:**
```
Before:
  Slot 1: Feature A (running 45 min, 60% done)
  Slot 2: Documentation (waiting for input, 10 min)
  Slot 3: Bug fix (stuck, no progress)

Action:
  - Respond to Slot 2 (frees it in 2 min)
  - Cancel Slot 3 (stuck task)
  - Keep Slot 1 (making good progress)

After:
  Slot 1: Feature A (continuing)
  Slot 2: Available for urgent bug fix
  Slot 3: Available for new work
```

### Step 6: Optimize for Rate Limits

Manage your parallel tasks to work within shared rate limits.

**Rate Limit Awareness:**

**What Consumes Rate Limits:**
- File reading and analysis
- Code generation
- Running commands
- AI processing and decision-making

**Strategies to Manage Rate Limits:**

**1. Stagger Task Starts:**
```
Instead of:
  Start all 3 tasks at 0:00 (spike in resource use)

Try:
  Start task 1 at 0:00
  Start task 2 at 0:05 (after initial analysis)
  Start task 3 at 0:10 (staggered load)
```

**2. Mix Task Complexity:**
```
✓ Combine resource-intensive with lightweight tasks
✓ Balance heavy analysis with simple changes
✓ Don't run multiple large-scale analyses simultaneously
```

**3. Time Task Execution:**
```
Off-peak hours: Run many parallel tasks
Peak hours: Reduce concurrent tasks
High-demand periods: Focus on critical work only
```

### Step 7: Develop Task Allocation Workflows

Create repeatable patterns for common scenarios.

**Workflow 1: Morning Startup (Pro - 3 slots)**
```
8:00 AM  - Review overnight PRs and issues
8:15 AM  - Start Slot 1: Most important feature
8:20 AM  - Start Slot 2: Critical bug fix
8:45 AM  - Slot 2 completes, start next bug
9:00 AM  - Start Slot 3: Quick documentation
9:10 AM  - Slot 3 completes, rotate quick tasks
11:00 AM - Slot 1 completes, review and deploy
```

**Workflow 2: Sprint Work (Max - 10 slots)**
```
Slots 1-4:  Sprint commitment features
Slots 5-6:  Bug backlog reduction
Slot 7:     Test coverage improvements
Slot 8:     Code review assistance
Slots 9-10: Available for urgent issues
```

**Workflow 3: Maintenance Day (Pro - 3 slots)**
```
Slot 1: Dependency updates (long-running)
Slot 2: Rotating lint fixes (5-10 min each)
Slot 3: Rotating documentation (5-10 min each)

Result: 1 major update + 20-30 small improvements
```

**Workflow 4: Multi-Repository Agency Work (Max - 10 slots)**
```
Client A (Slots 1-3):
  - Feature development
  - Bug fixes
  - Testing

Client B (Slots 4-6):
  - New API endpoints
  - Frontend updates
  - Documentation

Client C (Slots 7-9):
  - Security patches
  - Performance optimization
  - Infrastructure updates

Slot 10: Available for urgent requests
```

## Expected Results

After implementing effective resource allocation strategies, you should experience:

**Immediate Outcomes:**
- Clear understanding of available concurrent task capacity
- Strategic distribution of work across task slots
- No idle task slots (unless intentionally reserved)
- Balanced mix of long-running and quick tasks
- Buffer capacity for urgent work

**Productivity Improvements:**
- Higher task completion throughput
- Fewer blocked waiting periods
- Better utilization of concurrent limits
- Ability to juggle multiple priorities effectively
- Reduced context switching between unrelated tasks

**Workflow Benefits:**
- Predictable task allocation patterns
- Easier planning and estimation
- Better resource utilization
- Flexibility to handle urgent requests
- Reduced frustration from limit constraints

## Troubleshooting

### Common Issue 1
**Problem**: All task slots are full and can't start urgent work
**Solution**:
- Review active tasks and identify lowest priority
- Cancel or pause low-priority tasks to free slots
- If multiple tasks are near completion, wait 5-10 minutes
- Always maintain 1 free slot for urgent work using Priority Cascade Model
- Consider upgrading from Pro to Max if this happens frequently

### Common Issue 2
**Problem**: Tasks are running slower than expected
**Solution**:
- You may be hitting shared rate limits
- Reduce number of concurrent tasks temporarily
- Stagger task starts instead of launching simultaneously
- Avoid starting multiple analysis-heavy tasks at once
- Check for platform status or throttling messages
- Try running fewer tasks during peak hours

### Common Issue 3
**Problem**: Difficult to monitor many parallel tasks effectively
**Solution**:
- Use task categorization to organize mentally
- Check active sessions dashboard regularly (KB-017)
- Set calendar reminders to review every 15-30 minutes
- Use repository-based organization to reduce context switching
- Don't run more tasks than you can meaningfully monitor
- Consider if you're actually benefiting from maximum parallelization

### Common Issue 4
**Problem**: Some task slots sit idle while others are overloaded
**Solution**:
- Break large tasks into smaller independent tasks
- Review your task queue and start appropriate-sized work
- Use rotation model to keep slots active
- Don't reserve slots unnecessarily
- Start new tasks promptly when others complete
- Set up alert or reminder when slots become available

### Common Issue 5
**Problem**: Constantly switching between tasks, losing productivity
**Solution**:
- Let long tasks run without constant checking
- Batch your task monitoring (check every 20-30 min, not constantly)
- Use repository-based model to group related work
- Only respond to "waiting for input" notifications promptly
- Trust Claude Code to work independently
- Focus your attention on tasks that actually need guidance

### Common Issue 6
**Problem**: Unclear which resource allocation strategy to use
**Solution**:
- Start with Rotation Model (easiest)
- Pro users: Stick with Rotation or Priority Cascade
- Max users: Experiment with Parallel Development Model
- Match strategy to your work type (agency vs solo, single repo vs multi)
- Try different strategies for a week each
- Combine strategies as needed (no one-size-fits-all)

## Additional Tips

### Maximizing Pro's 3-Slot Limit

**Golden Rules for Pro Users:**
- Never let all 3 slots run long tasks simultaneously
- Always have at least one quick-rotating slot
- Reserve one slot for urgent work during business hours
- Chain quick tasks in succession for maximum throughput
- Use morning hours for long tasks, afternoons for quick rotations

**Example Optimal Pro Allocation:**
```
Monday-Friday 9am-5pm:
  Slot 1: Priority work (changes based on day's needs)
  Slot 2: Quick rotation (bug fixes, small tasks)
  Slot 3: Reserved for urgent issues

Evening/Weekend:
  All 3 slots: Longer tasks that can run unmonitored
```

### Leveraging Max's 10+ Slot Capacity

**Golden Rules for Max Users:**
- Group tasks by repository for mental organization
- Keep 2-3 slots free for urgent work
- Run different task types in parallel (dev, test, docs)
- Use extra capacity for experimental or learning tasks
- Batch similar tasks together

**Advanced Max Strategies:**
```
Allocate by work type:
  30% - Feature development (3 slots)
  30% - Bug fixes and improvements (3 slots)
  20% - Testing and QA (2 slots)
  10% - Documentation (1 slot)
  10% - Reserved for urgent work (1 slot)
```

### Task Sizing for Better Allocation

**Break Down Large Tasks:**
```
❌ "Rebuild entire authentication system"
✓ "Implement JWT token generation"
✓ "Add token refresh endpoint"
✓ "Create login UI component"
✓ "Write authentication tests"

Benefits: Each can run in parallel, easier to allocate
```

### Time-Based Allocation

**Consider time of day and week:**

**Peak Productivity Hours:**
- Run most important tasks
- Keep capacity for interruptions
- Balance long and short tasks

**Off-Peak Hours:**
- Queue up longer tasks
- Less need for immediate monitoring
- Can utilize full capacity

**Weekends:**
- Long-running maintenance tasks
- Experimental work
- Large refactoring projects

### Measuring Allocation Effectiveness

**Track these metrics over a week:**
- Number of tasks completed
- Average time per task type
- Percentage of time at full capacity
- Number of times blocked by full slots
- Number of cancelled or failed tasks

**Good indicators:**
- Completing 15+ tasks per day (Pro)
- Completing 40+ tasks per day (Max)
- Less than 10% idle slot time
- Rare blocking due to full capacity
- Most tasks completing successfully

### Coordination with Team Members

**If sharing a Claude Code account:**
- Establish slot allocation agreements
- Communicate before starting large tasks
- Use naming conventions for task descriptions
- Set up rotation schedules
- Maintain a shared task queue

### Emergency Reallocation

**When critical issues arise:**
1. Immediately cancel low-priority tasks
2. Pause medium-priority tasks if needed
3. Allocate freed slots to critical work
4. Resume normal allocation after emergency
5. Document incident for future planning

## Related Articles
- KB-005: How to understand the difference between Pro and Max concurrent limits
- KB-016: How to start a new Claude Code session
- KB-017: How to view all your active sessions
- KB-008: How to monitor a running task in real-time
- KB-010: How to cancel a running task if needed
- KB-007: How to describe a task effectively for Claude to understand

## Sources
1. Claude Code on the Web Launch Announcement - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
2. Embracing the Parallel Coding Agent Lifestyle - https://simonwillison.net/2025/Oct/5/parallel-coding-agents/ (Accessed: November 16, 2025)
3. Claude Code Web Parallel Tasking for Developers - VentureBeat Article (Accessed: November 16, 2025)
4. How to Use Claude Code Subagents to Parallelize Development - https://zachwills.net/how-to-use-claude-code-subagents-to-parallelize-development/ (Accessed: November 16, 2025)

Google Search Suggestion: `claude code web parallel tasks resource management best practices 2025`

---
*This article is part of the Claude Code Web knowledge base*
