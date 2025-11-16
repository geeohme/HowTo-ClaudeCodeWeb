# How to Create Your First Coding Task in Claude Code Web

**Article ID**: KB-006
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Creating a coding task is how you tell Claude what you want it to do with your code. This guide walks you through creating your first task, from selecting a repository to describing what you want accomplished.

## Prerequisites
- Active Claude Pro or Max subscription
- GitHub account authenticated with Claude Code Web (KB-003)
- At least one repository connected to Claude Code Web (KB-004)
- Understanding of what you want Claude to accomplish

## Steps

### Step 1: Navigate to Claude Code Web

1. Go to `https://claude.com/code`
2. Ensure you're logged in
3. You should see your connected repositories

### Step 2: Select a Repository

Choose which repository you want to work on:

1. Click on the repository from your connected repositories list
2. Or look for a "New Task" or "Create Task" button that allows repository selection
3. Verify the repository name and branch information displayed

**Tips for First Task:**
- Choose a repository you're familiar with
- Start with a simple, well-defined task
- Consider using a test or non-production repository first

### Step 3: Access the Task Creation Interface

Look for one of these options:
- "New Task" button
- "Create Session" option
- "Start Coding" button
- A text input or prompt area
- "Describe what you want to build" field

Click to open the task creation interface.

### Step 4: Describe Your Coding Task

In the task description field, clearly explain what you want Claude to do:

**Good First Task Examples:**

```
Add a README.md file to my project with installation instructions
```

```
Fix the typo in the header of index.html - change "Welcom" to "Welcome"
```

```
Create a new function called calculateTotal that adds up an array of numbers
```

```
Add comments to the main.js file explaining what each function does
```

**What Makes a Good Task Description:**
- Clear and specific
- Focused on one main objective
- Includes relevant context
- Specifies the file or area of code (if applicable)
- States the desired outcome

See KB-007 for detailed guidance on writing effective task descriptions.

### Step 5: Review and Submit Your Task

Before submitting:

1. **Read your description** - Is it clear what you want?
2. **Check the repository** - Is it the correct one?
3. **Verify branch information** - Is Claude working on the right branch?
4. **Consider scope** - Is the task appropriately sized for a first attempt?

When ready, click:
- "Start Task"
- "Begin Session"
- "Create"
- Or similar submission button

### Step 6: Confirm Task Initiation

After submission, you should see:
- A confirmation that the task has started
- The task appearing in your active sessions
- Claude beginning to analyze your repository
- Status updates showing Claude's progress
- A session interface where you can monitor the task

## Expected Results

After creating your first coding task, you should:
- See the task in your "Active Sessions" or "Running Tasks" list
- Observe Claude analyzing your codebase
- See real-time updates as Claude works
- Have the ability to interact with Claude during the session
- Watch as Claude reads files, makes plans, and implements changes
- Receive notifications or updates on task progress

## Troubleshooting

### Common Issue 1
**Problem**: Can't find the "New Task" or "Create Task" button
**Solution**:
- Ensure you have at least one repository connected
- Try selecting a specific repository first
- Look for a prominent action button or text input area
- Refresh the page if the interface isn't loading properly
- Check that you haven't reached your concurrent task limit

### Common Issue 2
**Problem**: Task creation button is disabled or grayed out
**Solution**:
- Verify you haven't reached your plan's concurrent task limit (3 for Pro, 10+ for Max)
- Check if you need to complete or cancel existing tasks first
- Ensure your subscription is active and current
- Verify the repository is properly connected

### Common Issue 3
**Problem**: Task starts but immediately fails or errors
**Solution**:
- Check that your task description is clear and actionable
- Ensure the repository has content (not an empty repo)
- Verify the repository has a valid default branch
- Try a simpler task first to test the connection
- Check your internet connection

### Common Issue 4
**Problem**: Unsure what to put in the task description
**Solution**:
- Start with a very simple task like "Add a comment to the README"
- Be specific about what file or component you want modified
- State the desired outcome clearly
- Avoid vague descriptions like "improve the code"
- See KB-007 for detailed examples and best practices

### Common Issue 5
**Problem**: Task seems stuck on "Initializing" or "Starting"
**Solution**:
- Wait 30-60 seconds - initialization can take a moment
- Check your internet connection
- Refresh the page and check if the task is now showing as active
- If stuck for more than 2 minutes, cancel and try creating a new task
- Ensure the repository is accessible from GitHub

## Additional Tips

### Starting Small
- Your first task should be simple and low-risk
- Examples: adding comments, fixing typos, creating documentation
- Build confidence before tackling complex features or refactors

### Setting Expectations
- Claude will read and analyze relevant files first
- The task might take a few minutes to several hours depending on complexity
- You can monitor progress in real-time
- You can intervene and guide Claude during the session

### Best Practices for First Tasks
- ✓ Use a test repository or non-critical project
- ✓ Choose a well-defined, specific task
- ✓ Keep it simple (one clear objective)
- ✓ Be available to monitor the first task
- ✓ Read the progress updates to understand Claude's workflow

### What Happens Next
1. Claude analyzes your repository structure
2. Claude reads relevant files
3. Claude creates a plan for your task
4. Claude implements the changes
5. Claude commits changes to a new branch
6. Claude provides a summary and next steps (like creating a PR)

## Related Articles
- KB-004: How to connect your first GitHub repository to Claude Code Web
- KB-007: How to describe a task effectively for Claude to understand
- KB-008: How to monitor a running task in real-time
- KB-009: How to view the progress of your task execution
- KB-010: How to cancel a running task if needed
- KB-021: How to provide additional context to Claude during a task

## Sources
1. Claude Code Web Task Creation Guide - Search: "Claude Code Web create first task tutorial" (Accessed: November 16, 2025)
2. Claude Code Web Getting Started - Search: "claude code web beginner guide first session" (Accessed: November 16, 2025)
3. Anthropic Claude Code Documentation - Search: "claude code describe task how to start" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
