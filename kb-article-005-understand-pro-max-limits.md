# How to Understand the Difference Between Pro (3 Tasks) and Max (10+ Tasks) Concurrent Limits

**Article ID**: KB-005
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 5 minutes

## Overview
Claude Code Web offers different concurrent task limits based on your subscription tier. Understanding these limits helps you choose the right plan and manage your coding workflows effectively. This guide explains what concurrent tasks are, how limits differ between Pro and Max, and how to work within them.

## Prerequisites
- Basic understanding of Claude Code Web functionality
- Knowledge of Claude Pro and Max subscription tiers

## What Are Concurrent Tasks?

**Concurrent tasks** are coding sessions that run simultaneously in Claude Code Web. Each task represents an independent coding workflow running in its own isolated environment.

**Examples of Tasks:**
- Implementing a new feature in Repository A
- Fixing a bug in Repository B
- Writing tests in Repository C
- Refactoring code in Repository D

When these run at the same time, they count as concurrent tasks.

## Subscription Tier Comparison

### Claude Pro: 3 Concurrent Tasks

**What You Get:**
- Run up to **3 coding tasks simultaneously**
- Each task in a separate isolated sandbox
- Full access to all Claude Code Web features
- Suitable for individual developers

**Best For:**
- Solo developers
- Working on 1-3 projects at a time
- Sequential or moderately parallel workflows
- Personal projects or small-scale development
- Learning and experimenting with Claude Code

**Example Workflow:**
- Task 1: Feature development in your main project
- Task 2: Bug fix in a side project
- Task 3: Documentation updates in another repository

### Claude Max: 10+ Concurrent Tasks

**What You Get:**
- Run **10 or more coding tasks simultaneously**
- Significantly higher parallelization capacity
- All Claude Code Web features (same as Pro)
- Extended usage limits for intensive workflows

**Best For:**
- Professional developers managing multiple projects
- Team leads coordinating work across repositories
- Agencies working with multiple client projects
- Heavy parallel development workflows
- CI/CD pipeline integration needs
- Large-scale refactoring across multiple repositories

**Example Workflow:**
- Tasks 1-5: Feature development across 5 microservices
- Tasks 6-7: Bug fixes in different projects
- Task 8: Writing tests
- Task 9: Code review assistance
- Task 10: Documentation generation
- Plus room for additional urgent tasks

## Understanding the Limits

### What Counts as a Concurrent Task?

**Counts Toward Your Limit:**
- Active coding sessions that are currently running
- Tasks that are in progress (reading, analyzing, writing code)
- Paused tasks that haven't been completed or cancelled

**Does NOT Count Toward Your Limit:**
- Completed tasks
- Cancelled tasks
- Tasks that have finished and been closed
- Historical/archived sessions

### What Happens When You Reach Your Limit?

**If you're on Pro and have 3 active tasks:**
1. You cannot start a 4th task until one of the existing tasks completes or is cancelled
2. You'll see a message indicating you've reached your concurrent task limit
3. The interface will show "X/3 tasks running" or similar indicator
4. You'll need to wait for a task to finish or manually cancel one

**If you're on Max and have 10+ active tasks:**
- Same principle applies at the higher threshold
- More flexibility for complex, parallel workflows
- Better suited for managing multiple urgent tasks simultaneously

### Task Lifecycle

Understanding when a task occupies a slot:

```
Task Created → Counts toward limit
    ↓
Task Running → Counts toward limit
    ↓
Task Paused → Still counts toward limit
    ↓
Task Completed/Cancelled → Frees up a slot
```

## Choosing the Right Plan

### Choose Claude Pro If:
- ✓ You work on 1-3 projects at a time
- ✓ You're a solo developer or freelancer
- ✓ Your workflows are mostly sequential
- ✓ You can wait for tasks to complete before starting new ones
- ✓ You're cost-conscious and want the essential features
- ✓ You're new to Claude Code and want to test it out

### Choose Claude Max If:
- ✓ You manage multiple projects simultaneously
- ✓ You need to respond to urgent issues across different codebases
- ✓ You work in a fast-paced environment with parallel demands
- ✓ You're part of a development team or agency
- ✓ You frequently context-switch between repositories
- ✓ You run automated workflows or integrations
- ✓ Three concurrent tasks feel limiting for your workflow

### Upgrade Decision Points

**Consider upgrading from Pro to Max when:**
1. You frequently hit the 3-task limit
2. You find yourself waiting for tasks to complete to start new ones
3. You manage 4+ active projects
4. You need rapid parallel execution across repositories
5. Your workflow demands exceed what 3 concurrent tasks can handle

## Working Within Your Limits

### Strategies for Pro Users (3 Tasks):

**1. Prioritize Tasks**
- Start the most urgent or important tasks first
- Queue less critical tasks for later

**2. Sequential Workflows**
- Complete tasks before starting new ones when possible
- Group related work to minimize context switching

**3. Cancel Unnecessary Tasks**
- If a task becomes irrelevant, cancel it to free up a slot
- Don't leave tasks paused indefinitely

**4. Plan Your Sessions**
- Think about which 3 tasks you need running simultaneously
- Stagger non-urgent work

### Strategies for Max Users (10+ Tasks):

**1. Parallel Workflows**
- Take advantage of high concurrency for cross-repository work
- Run comprehensive test suites alongside feature development
- Manage multiple client projects simultaneously

**2. Rapid Response**
- Keep slots available for urgent bug fixes
- Allocate tasks across different priority levels

**3. Team Coordination**
- If sharing an account, coordinate task usage
- Monitor active tasks to avoid hitting limits

## Monitoring Your Usage

### How to Check Your Current Usage:

1. Look for a task counter in the Claude Code Web interface
2. Common locations:
   - Dashboard header: "2/3 tasks running"
   - Active sessions panel
   - Status bar or indicator
3. Review your active sessions list to see all running tasks

### What to Look For:
- **Pro**: "X/3 tasks" where X is your current count
- **Max**: "X/10+ tasks" where X is your current count
- Visual indicators (progress bars, colors) showing limit proximity

## Expected Results

After understanding concurrent limits, you should be able to:
- Explain the difference between Pro's 3 tasks and Max's 10+ tasks
- Identify which plan suits your development workflow
- Monitor your current task usage
- Work effectively within your subscription's limits
- Make informed decisions about upgrading or downgrading
- Plan coding sessions according to your available task slots

## Troubleshooting

### Common Issue 1
**Problem**: Can't start a new task, seeing "limit reached" message
**Solution**:
- Check your active tasks list
- Complete or cancel tasks you no longer need
- Wait for running tasks to finish
- Consider upgrading to Max if you consistently hit this limit

### Common Issue 2
**Problem**: Unclear how many task slots are currently in use
**Solution**:
- Navigate to your sessions or dashboard page
- Look for active/running session indicators
- Count tasks with "In Progress" or "Running" status
- Completed and cancelled tasks don't count

### Common Issue 3
**Problem**: Paused task is preventing new tasks from starting
**Solution**:
- Paused tasks still occupy a concurrent slot
- Either resume and complete the paused task
- Or cancel it to free up the slot
- Don't leave tasks paused unless you plan to return to them soon

### Common Issue 4
**Problem**: Unsure whether to upgrade from Pro to Max
**Solution**:
- Track how often you hit the 3-task limit over a week
- If hitting it frequently (multiple times per day), consider Max
- If rarely hitting it (once a week or less), Pro is likely sufficient
- Consider your growth trajectory and future project plans

## Additional Tips
- Task limits are enforced at the subscription level, not per repository
- You can mix repositories in your concurrent tasks (e.g., 3 different repos)
- Completing a task instantly frees up a slot for a new task
- The limit is for truly concurrent/parallel tasks, not total tasks per day
- There's no daily or monthly task limit—only concurrent/simultaneous tasks
- Both Pro and Max have the same feature set; only concurrency differs
- Monitor your actual usage patterns before deciding to upgrade or downgrade
- Some tasks complete quickly (minutes), others may run longer (hours for complex work)

## Related Articles
- KB-002: How to sign up for a Claude Pro or Max account
- KB-006: How to create your first coding task in Claude Code Web
- KB-008: How to monitor a running task in real-time
- KB-010: How to cancel a running task if needed
- KB-017: How to view all your active sessions

## Sources
1. Claude Subscription Plans Comparison - https://claude.com/upgrade (Accessed: November 16, 2025)
2. Claude Code Web Concurrent Task Limits - Search: "Claude Code Web Pro Max concurrent tasks limits" (Accessed: November 16, 2025)
3. Claude Code Web Pricing Documentation - Search: "claude pro max concurrent task comparison" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
