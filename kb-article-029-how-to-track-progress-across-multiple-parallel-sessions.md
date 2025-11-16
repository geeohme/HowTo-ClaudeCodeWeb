# How to track progress across multiple parallel sessions

**Article ID**: KB-029
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15 minutes

## Overview
Claude Code Web's parallel session capability allows you to run multiple coding tasks simultaneously, each in its own isolated environment. This guide teaches you how to effectively track progress across all your parallel sessions, manage concurrent work efficiently, and ensure no task falls through the cracks when juggling multiple AI-driven development tasks.

## Prerequisites
- Active Claude Pro (3 concurrent sessions) or Max (10+ concurrent sessions) subscription
- At least 2-3 connected repositories or multiple tasks in one repository
- Understanding of individual session monitoring (KB-008, KB-009)
- Familiarity with viewing active sessions (KB-017)

## Understanding Parallel Session Architecture

### How Parallel Sessions Work

**Isolated Execution:**
Each Claude Code Web session runs in its own isolated Ubuntu-based container:
- Independent file systems
- Separate execution environments
- No cross-session interference
- Isolated network access

**Concurrent Session Limits:**

**Claude Pro Plan:**
- Maximum 3 simultaneous sessions
- Sufficient for small teams or individual developers
- Requires strategic task allocation

**Claude Max Plan:**
- Maximum 10+ simultaneous sessions
- Ideal for power users and larger projects
- Enables comprehensive parallel workflows

**Resource Consumption:**
- Each parallel session consumes rate limits proportionately
- Running 3 tasks uses 3x the rate limit of a single task
- Plan accordingly to avoid hitting limits

### Benefits of Parallel Execution

**Time Efficiency:**
```
Serial Execution (one at a time):
Task A: 15 min → Task B: 10 min → Task C: 8 min
Total time: 33 minutes

Parallel Execution (3 concurrent):
Task A: 15 min
Task B: 10 min  } Running simultaneously
Task C: 8 min
Total time: 15 minutes (limited by longest task)
```

**Use Cases for Parallel Sessions:**
- Tackling bug backlogs across multiple repositories
- Running test generation while implementing features
- Working on frontend and backend simultaneously
- Conducting exploratory refactoring on different modules
- Parallel documentation and code updates

## Steps

### Step 1: Set Up Your Multi-Session Dashboard View

**Access the Sessions Overview:**

1. Navigate to `https://claude.com/code`
2. Look for "Active Sessions" or "All Sessions" section
3. Ensure you can see all running sessions simultaneously
4. Maximize the view to see as many sessions as possible

**Optimize Your View:**

**Layout Options:**
- **Grid View**: See multiple session cards at once (recommended for 3-6 sessions)
- **List View**: Compact view showing more sessions (better for 7+ sessions)
- **Split View**: Monitor one session in detail while seeing others in sidebar

**Session Card Information:**
Each session card should display:
- Repository name and branch
- Task description/title
- Current status (Analyzing, Implementing, Testing, etc.)
- Elapsed time
- Progress indicators
- Quick action buttons (View, Cancel)

### Step 2: Establish a Session Naming and Organization System

**Create Descriptive Task Names:**

**Poor naming:**
```
"Fix bug"
"Update component"
"Add feature"
```

**Better naming:**
```
"[URGENT] Fix login redirect bug in UserAuth"
"[REPO: Frontend] Update Header component dark mode"
"[Feature] Add email validation to ContactForm"
```

**Naming Convention Template:**
```
[PRIORITY] [REPO/MODULE] Brief specific description
```

**Examples:**
- `[P1] [API] Fix rate limiting bug in /auth endpoint`
- `[P2] [Frontend] Add loading spinners to all forms`
- `[P3] [Docs] Update README with new installation steps`

**Organize by Priority:**

**High Priority (P1):**
- Production bugs
- Blocking issues
- Time-sensitive features
- Critical security fixes

**Medium Priority (P2):**
- Important but non-blocking features
- Refactoring tasks
- Performance improvements
- Non-critical bug fixes

**Low Priority (P3):**
- Documentation updates
- Code cleanup
- Exploratory tasks
- Nice-to-have features

### Step 3: Track Progress Across All Sessions

**Create a Progress Tracking Matrix:**

**Mental or Written Checklist:**
```
Session 1: [API] Auth Bug
Status: Testing (85%)
Expected completion: 5 min
Needs attention: No
Next action: Review when complete

Session 2: [Frontend] Dark Mode
Status: Implementing (60%)
Expected completion: 12 min
Needs attention: No
Next action: Check in 10 min

Session 3: [Tests] Add unit tests
Status: Waiting for Input (40%)
Expected completion: Unknown
Needs attention: YES - needs clarification
Next action: Respond now
```

**Use Status Indicators:**

**Visual Cues to Monitor:**
- **Green/Spinning**: Actively working, no intervention needed
- **Yellow/Pause icon**: Waiting for your input - NEEDS ATTENTION
- **Progress percentage**: Track how close to completion
- **Elapsed time**: Identify unusually long-running tasks

**Track Completion Velocity:**
Monitor how quickly sessions complete:
```
Completed in last hour:
- Session A: 8 minutes (bug fix)
- Session B: 15 minutes (feature)
- Session C: 12 minutes (tests)

Average completion: 11.7 minutes
Remaining sessions: 2
Estimated remaining time: ~23 minutes
```

### Step 4: Implement a Regular Check-In Schedule

**Establish Check-In Intervals:**

**For 3 Concurrent Sessions (Pro):**
```
Every 5-10 minutes:
- Quick scan of all session statuses
- Identify any "Waiting for Input" sessions
- Note progress percentages
- Check for any error indicators

Every 15-20 minutes:
- Deep dive into each session's activity log
- Verify progress is healthy
- Cancel any stuck sessions
- Start new sessions in freed slots
```

**For 10+ Concurrent Sessions (Max):**
```
Every 10-15 minutes:
- Scan high-priority sessions (P1)
- Quick check on medium priority (P2)
- Note completion of any sessions

Every 30 minutes:
- Comprehensive review of all sessions
- Respond to waiting sessions
- Reallocate slots as needed
- Review low-priority sessions (P3)
```

**Triage During Check-Ins:**

**Priority-Based Review:**
1. **First**: Check sessions marked "Waiting for Input"
2. **Second**: Review high-priority (P1) sessions
3. **Third**: Verify sessions approaching completion (90%+)
4. **Fourth**: Scan for error messages or stuck sessions
5. **Fifth**: Quick status check on low-priority sessions

### Step 5: Use Advanced Tracking Techniques

**Session Grouping by Repository:**

**Organize View by Repository:**
```
Repository: E-commerce-Frontend
├─ Session 1: Fix cart calculation (85% - Testing)
├─ Session 2: Add promo code field (60% - Implementing)
└─ Session 3: Update product images (30% - Analyzing)

Repository: E-commerce-API
├─ Session 4: Rate limiting fix (95% - Completing)
└─ Session 5: Add new endpoint (40% - Planning)

Repository: Documentation
└─ Session 6: Update API docs (50% - Implementing)
```

**Track Session Dependencies:**

Some tasks may have dependencies:
```
Blocked/Dependent Tasks:
- Session A: Must complete before Session C can merge
- Session B: Independent, can merge anytime
- Session C: Depends on Session A (waiting)

Order of Operations:
1. Complete Session A (70% - 8 min remaining)
2. Review and merge Session A
3. Session C can then proceed independently
```

**Use Time-Based Organization:**

**Sort by Expected Completion:**
```
Completing Soon (next 5 min):
→ Session 2: Auth fix (95%)
→ Session 5: Documentation (90%)

In Progress (5-15 min):
→ Session 1: Feature A (60%)
→ Session 4: Refactor B (55%)

Early Stages (15+ min):
→ Session 3: New feature (25%)
→ Session 6: Large refactor (15%)
```

### Step 6: Manage Session Lifecycle Across Parallel Work

**Session Start Strategy:**

**Staggered Starts (Recommended):**
```
Time 0:00 - Start Session 1 (high priority)
Time 0:05 - Verify Session 1 stable, start Session 2
Time 0:10 - Verify both stable, start Session 3
```

Benefits:
- Easier to monitor initial phases
- Catch configuration issues early
- Avoid simultaneous "waiting for input" prompts
- Better mental model of each task

**Batch Starts (For Experienced Users):**
```
Time 0:00 - Start all 3-10 sessions simultaneously
```

Benefits:
- Maximum parallelism immediately
- Good for well-defined, independent tasks
- Saves setup time

**Session Completion Strategy:**

**Continuous Rotation:**
```
Session 1 completes → Review → Start new Session 4
Session 2 completes → Review → Start new Session 5
Session 3 completes → Review → Start new Session 6
```

**Batch Completion:**
```
Let all sessions complete → Review all together → Start next batch
```

### Step 7: Handle Multi-Session Edge Cases

**When Multiple Sessions Need Input Simultaneously:**

**Prioritization Strategy:**
1. Answer blocking questions first
2. Respond to high-priority sessions before low-priority
3. Provide quick answers to simple clarifications
4. Save complex decision-making for later if needed

**Example:**
```
Session A (P1): Asking "Should I use PUT or PATCH?" → Answer immediately
Session C (P3): Asking "Do you want detailed comments?" → Answer when P1/P2 handled
Session B (P2): Asking "Which validation library?" → Research if needed
```

**When Hitting Concurrent Limits:**

**Pro Plan (3 session limit):**
```
All 3 slots full, new urgent task arrives:
1. Identify lowest-priority running session
2. Evaluate: Cancel or let complete?
3. If canceling: Session should be <30% progress or stuck
4. If waiting: Session should be >70% progress
5. Start urgent task in freed/completed slot
```

**Max Plan (10+ session limit):**
- Less common to hit limits
- Same prioritization logic applies
- Consider if you can realistically monitor 10+ sessions

**When Sessions Fail or Get Stuck:**

**Recognition:**
- No updates for 5+ minutes
- Error messages in activity feed
- Progress stuck at same percentage
- Repeated failed operations

**Response:**
1. Click into the session details
2. Review recent activity log
3. Look for specific error messages
4. Decide: Intervene or cancel and restart?

## Expected Results

After implementing multi-session progress tracking, you should:
- Maintain awareness of status across all parallel sessions
- Know which sessions need attention without constant monitoring
- Efficiently allocate your limited concurrent session slots
- Complete multiple tasks in parallel without losing track
- Respond quickly to sessions requiring input
- Identify and handle stuck or failed sessions promptly
- Maximize throughput with strategic session management
- Feel confident juggling 3-10+ concurrent tasks

## Troubleshooting

### Common Issue 1
**Problem**: Overwhelmed by tracking too many parallel sessions
**Solution**:
- Reduce concurrent sessions to a manageable number (2-3 for beginners)
- Focus on high-priority tasks only
- Use staggered starts instead of batch starts
- Set timers for check-ins rather than watching continuously
- Accept that you don't need to watch every second - logs are available later
- Consider using fewer repositories to reduce context switching

### Common Issue 2
**Problem**: Lost track of which session is doing what
**Solution**:
- Improve task naming with descriptive titles
- Add repository name prefixes to session names
- Use the session grouping/filtering features
- Maintain a simple text checklist outside the interface
- Review each session's full description before starting new ones
- Close completed sessions to reduce clutter

### Common Issue 3
**Problem**: Multiple sessions waiting for input simultaneously
**Solution**:
- This is normal when tasks have decision points
- Prioritize by task priority (P1 > P2 > P3)
- Answer simple questions first to free up sessions
- Research complex questions while other sessions continue
- Don't rush decisions - a delayed good answer beats a quick bad one
- Consider pausing low-priority sessions while handling urgent ones

### Common Issue 4
**Problem**: Can't tell which sessions are progressing normally vs stuck
**Solution**:
- Learn normal progression patterns from experience
- Look for regular activity updates (every 1-3 minutes is healthy)
- Check elapsed time against task complexity expectations
- Review activity logs for repeated errors or loops
- When in doubt, wait 5 minutes before assuming stuck
- Refresh the page to ensure UI is showing current state

### Common Issue 5
**Problem**: Difficulty prioritizing which sessions to monitor closely
**Solution**:
- Always monitor P1 (high priority) sessions most closely
- Sessions >90% complete deserve attention (ready for review)
- Sessions waiting for input need immediate attention
- Sessions <20% complete can run unattended briefly
- Recently started sessions (first 2-3 min) should be verified as stable
- Long-running sessions (>20 min) should be checked for problems

### Common Issue 6
**Problem**: Running out of concurrent session slots when needed
**Solution**:
- Maintain 1 slot free for urgent tasks when possible
- Review task queue and adjust priorities
- Cancel sessions that are stuck or low-priority
- Let fast sessions complete before starting similar ones
- Consider upgrading from Pro to Max if consistently hitting limits
- Batch similar tasks to run sequentially rather than in parallel

## Additional Tips

### Workspace Organization

**Use Multiple Browser Windows (Carefully):**
```
Window 1: Dashboard view - all sessions overview
Window 2: Detailed view - currently active high-priority session
Window 3: Documentation/research for decisions

Warning: Avoid multiple tabs interacting with same session (causes desynchronization)
```

**External Tracking Tools:**

**Simple Spreadsheet:**
```
Session | Repository | Task | Status | % | Started | ETA | Priority | Notes
1       | Frontend   | Auth | Impl   | 65| 10:15   | 5m  | P1       | Going well
2       | API        | Rate | Test   | 85| 10:10   | 3m  | P1       | Almost done
3       | Docs       | Read | Impl   | 45| 10:20   | 8m  | P3       | Can wait
```

**Note-Taking App:**
```markdown
## Active Sessions - November 16, 2025

### High Priority
- [ ] Session 1: Auth bug fix (65% complete, ETA 5 min)
- [x] Session 4: Rate limiting (COMPLETED)

### Medium Priority
- [ ] Session 2: Dark mode (40% complete, needs decision)

### Low Priority
- [ ] Session 3: Documentation (45% complete)
```

### Strategic Session Allocation

**Match Task Duration to Available Time:**

**Have 30 minutes?**
```
Strategy A: 3 short tasks (10 min each) in parallel
- Maximum throughput
- Frequent rotation
- Good for bug fixes

Strategy B: 1 medium + 2 short tasks
- 1x 25-min task
- 2x 10-min tasks (rotating)
- Mixed complexity
```

**Have 2 hours?**
```
Strategy A: Complex features + quick fixes
- Slots 1-2: Major features (60-90 min)
- Slot 3: Quick fixes (rotating every 10-15 min)

Strategy B: Comprehensive repository work
- All slots on same repository
- Different aspects (frontend, backend, tests)
- Cohesive progress on single project
```

### Pattern Recognition

**Learn Your Task Patterns:**
- Simple bug fixes: Usually 5-12 minutes
- Adding form validation: Usually 8-15 minutes
- New React components: Usually 12-25 minutes
- API endpoint creation: Usually 15-30 minutes
- Refactoring modules: Usually 20-45 minutes
- Comprehensive test suites: Usually 25-60 minutes

**Use Patterns for Planning:**
```
Available: 3 slots, 45 minutes

Plan A:
- 1x Refactoring (30 min)
- 2x Bug fixes (10 min each, rotating)
Result: 1 refactor + 6 bug fixes done

Plan B:
- 3x New components (15 min each)
Result: 3 components done
```

### Maximizing Parallel Efficiency

**3 Concurrent Sessions (Pro):**
```
Optimal Mix:
- 1x Long-running complex task (30-60 min)
- 1x Medium task (15-30 min)
- 1x Quick rotation slot (5-15 min tasks)

This provides:
- Consistent progress on major work
- Medium-term deliverables
- Frequent completion "wins"
```

**10 Concurrent Sessions (Max):**
```
Optimal Mix:
- 2-3x Major features (45-90 min)
- 3-4x Medium features (20-45 min)
- 3-4x Quick fixes (5-20 min)
- 1x Emergency/urgent slot (kept free)

This provides:
- Balanced progress across complexity levels
- Capacity for urgent interrupts
- Manageable monitoring load
```

### Communication and Collaboration

**For Team Environments:**

**Session Coordination:**
- Communicate which repositories you're working on
- Avoid parallel sessions on same files (merge conflicts)
- Share progress on shared goals
- Coordinate priority alignment

**Example Team Protocol:**
```
Before starting sessions:
1. Check team's active sessions
2. Identify repository/module overlaps
3. Communicate your intended work
4. Stagger work to avoid conflicts
```

### Mental Models for Success

**Think in Batches:**
- Group similar tasks together
- Complete a batch before starting new types
- Reduces context switching
- Easier to track progress

**Accept Imperfect Monitoring:**
- You can't watch everything simultaneously
- Trust the system to work autonomously
- Check in periodically
- Review logs after completion for learning

**Prioritize Ruthlessly:**
- Not all sessions deserve equal attention
- Some can run completely unattended
- Focus monitoring where it matters most
- Learn to let go of low-priority session details

## Related Articles
- KB-017: How to view all your active sessions
- KB-008: How to monitor a running task in real-time
- KB-009: How to view the progress of your task execution
- KB-016: How to start a new Claude Code session
- KB-018: How to pause and resume a session (if available)
- KB-010: How to cancel a running task if needed
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-005: How to understand the difference between Pro and Max concurrent limits

## Sources
1. Claude Code on the Web - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
2. How I'm Using Claude Code On Web (New Feature) To Run Multiple Coding Tasks in Parallel - https://medium.com/@joe.njenga/how-im-using-claude-code-on-the-web-new-feature-to-run-multiple-coding-tasks-in-parallel-3cd7f47931a6 (Accessed: November 16, 2025)
3. Claude Code comes to web and mobile - https://venturebeat.com/ai/claude-code-comes-to-web-and-mobile-letting-devs-launch-parallel-jobs-on (Accessed: November 16, 2025)
4. Managing Multiple Claude Code Sessions Without Worktrees - https://blog.gitbutler.com/parallel-claude-code (Accessed: November 16, 2025)

Google Search Suggestion: `claude code web managing multiple concurrent sessions best practices 2025`

---
*This article is part of the claudecodeweb knowledge base*
