# How to Intervene and Guide Claude Mid-Session When It Encounters Issues

**Article ID**: KB-022
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 7 minutes

## Overview
Even with clear task descriptions, Claude may occasionally encounter issues, take an unexpected approach, or need guidance during a session. This guide shows you how to effectively intervene, redirect, and guide Claude when problems arise.

## Prerequisites
- Active Claude Pro or Max subscription
- Understanding of monitoring sessions (KB-008)
- Familiarity with providing context (KB-021)
- Experience with at least a few sessions

## Understanding When to Intervene

### Signs Claude Needs Guidance

**Watch for these indicators:**

**Wrong Direction:**
```
Activity Log:
[2:35 PM] Creating authentication with custom JWT implementation

You realize: Project already has Auth0 integration
Action needed: Redirect to use existing auth system
```

**Repeated Errors:**
```
Activity Log:
[2:40 PM] Running tests... 3 failed
[2:42 PM] Fixing tests...
[2:43 PM] Running tests... 3 failed
[2:44 PM] Fixing tests...
[2:45 PM] Running tests... 3 failed

Pattern: Stuck in a loop
Action needed: Provide guidance or different approach
```

**Missing Important Context:**
```
Activity Log:
[2:38 PM] Installing Express framework

You realize: Project uses Fastify, not Express
Action needed: Correct the framework choice
```

**Scope Creep:**
```
Activity Log:
[2:50 PM] Adding user authentication
[2:55 PM] Creating admin dashboard
[3:00 PM] Building email notification system

Original task: "Add password reset button"
Action needed: Refocus on original scope
```

**Inefficient Approach:**
```
Activity Log:
[3:10 PM] Reading all 500 component files individually

You realize: Only need to modify header component
Action needed: Direct to specific files
```

### When NOT to Intervene

**Let Claude work when:**
- Taking more time than expected but making progress
- Using different but valid approach than you'd use
- Handling routine tasks competently
- Working through errors systematically
- Still in analysis or planning phase
- Following reasonable patterns

**Trust Claude to:**
- Choose variable and function names
- Organize code structure
- Handle standard error cases
- Format code appropriately
- Write commit messages
- Select common libraries

## Steps to Intervene Effectively

### Step 1: Observe and Confirm Issue

**Before intervening:**

**Ask yourself:**
- Is this actually a problem or just different than my approach?
- Will this cause real issues or is it a style preference?
- Has Claude had enough time to work through it?
- Is the session truly stuck or just slow?

**Wait for confirmation:**
- Give Claude 2-3 minutes to self-correct
- Check if error is being actively addressed
- See if next steps might resolve it
- Verify it's not a temporary state

### Step 2: Access the Session Chat

**Open communication channel:**
1. Navigate to the active session
2. Find the message or chat interface
3. Locate input field for sending messages
4. Prepare your intervention message

### Step 3: Provide Clear Guidance

**Write intervention message:**

**Effective intervention patterns:**

**Pattern 1: Stop and Redirect**
```
"Stop - please don't install Express. This project uses Fastify.
The setup is already in src/server.js. Use that instead."
```

**Pattern 2: Clarify Misunderstanding**
```
"I think there's a misunderstanding. We already have authentication
set up with Auth0 (see src/auth/). You just need to add a password
reset button that calls the existing resetPassword() function."
```

**Pattern 3: Provide Missing Information**
```
"The tests are failing because the test database isn't seeded.
Run 'npm run db:seed:test' before running tests. This is
documented in tests/README.md."
```

**Pattern 4: Narrow Scope**
```
"This is getting broader than intended. For this session, please
focus only on adding the password reset button. We'll handle the
email notification in a separate task."
```

**Pattern 5: Suggest Different Approach**
```
"Instead of reading all component files, please just modify
src/components/Header.js. That's the only file that needs changes
for this task."
```

**Pattern 6: Provide Solution to Blocker**
```
"The API you're trying to reach requires a proxy. Add this to
your fetch call:
  headers: { 'X-API-Key': process.env.VITE_API_KEY }

The environment variable is already set in .env"
```

### Step 4: Monitor Response

**Watch for Claude's acknowledgment:**

**Positive indicators:**
```
Claude: "You're right, I see Auth0 is already configured in
src/auth/. I'll use the existing resetPassword() function instead.
Proceeding with just adding the UI button."
```

**Activity log changes:**
```
[Before intervention]
[3:15 PM] Installing express
[3:16 PM] Creating authentication middleware

[After intervention]
[3:17 PM] Reading existing Auth0 configuration
[3:18 PM] Adding password reset button to LoginForm
```

### Step 5: Confirm Correction

**Verify new direction:**
- Check that approach has changed
- Ensure misunderstanding is resolved
- Watch for progress on correct path
- Confirm scope is now appropriate

**If issue persists:**
- Provide additional clarification
- Be more explicit
- Give specific examples
- Point to exact file locations
- Consider cancelling and restarting with better initial context

## Intervention Techniques

### Technique 1: The Gentle Redirect

**When to use:** Minor course correction needed

**Example:**
```
"Small note: prefer async/await over .then() chaining in this
project. See UserService.js for examples of our pattern."
```

### Technique 2: The Firm Stop

**When to use:** Claude is going completely wrong direction

**Example:**
```
"STOP - Do not create a new database schema. The schema is already
defined and in production. You should only add a new column to the
existing users table. Use a migration file."
```

### Technique 3: The Information Supply

**When to use:** Claude lacks necessary information

**Example:**
```
"Context you need:
- API endpoint: https://api.example.com/v2/users
- Auth token is in environment variable API_TOKEN
- Rate limit: 100 requests/minute
- Use retry logic from src/utils/retryRequest.js"
```

### Technique 4: The Scope Limiter

**When to use:** Task is expanding beyond intent

**Example:**
```
"Let's narrow the scope. For this session:
✅ Add the button
✅ Wire up the click handler
❌ Don't build the entire password reset flow
❌ Don't add email notifications

We'll do the rest in follow-up sessions."
```

### Technique 5: The Example Provider

**When to use:** Explanation isn't working, show instead

**Example:**
```
"Here's exactly what I need:

function handleReset() {
  const email = emailInput.value;
  await authService.requestPasswordReset(email);
  showSuccessMessage('Check your email');
}

Just add this to the component and connect it to the button."
```

### Technique 6: The Question Clarifier

**When to use:** Claude asked a question you missed

**Example:**
```
"To answer your earlier question about validation: Yes, validate
the email format before calling the API. Use the existing
validateEmail() function from utils/validation.js."
```

## Common Intervention Scenarios

### Scenario 1: Using Wrong Library/Framework

**Problem:**
```
Claude is installing a different library than what project uses
```

**Intervention:**
```
"Don't install moment.js - this project uses date-fns. It's
already in package.json. Import from 'date-fns' instead."
```

### Scenario 2: Stuck on Failing Tests

**Problem:**
```
Tests fail repeatedly, same error each time
```

**Intervention:**
```
"The test failure is because mock data is missing. Add this to
the test setup:

jest.mock('../api', () => ({
  fetchUser: jest.fn(() => Promise.resolve(mockUser))
}));

This follows the pattern in existing tests."
```

### Scenario 3: Overcomplicating Solution

**Problem:**
```
Claude is building elaborate solution for simple task
```

**Intervention:**
```
"This can be much simpler. Just add one line to the existing
handleSubmit function:

if (!email) return;

No need to create a new validation service."
```

### Scenario 4: Wrong Files Being Modified

**Problem:**
```
Claude is editing files that shouldn't change
```

**Intervention:**
```
"Please don't modify database/schema.sql - that's production
schema. Only create a new migration file in database/migrations/.
Use the 2024_11_15_add_user_column.sql as a template."
```

### Scenario 5: Misunderstanding Requirements

**Problem:**
```
Claude implements something different than intended
```

**Intervention:**
```
"I think I wasn't clear. I don't need a full dark mode theme.
Just add a single toggle button that changes the background
color of the header from white to dark gray. That's all."
```

### Scenario 6: Can't Find Necessary Code

**Problem:**
```
Claude searching unsuccessfully for something that exists
```

**Intervention:**
```
"The authentication code is in src/lib/auth/ (not src/auth/).
Specifically, look at src/lib/auth/AuthProvider.tsx for the
context you need to use."
```

## Expected Results

After effective intervention, you should:
- See Claude acknowledge your guidance
- Observe immediate change in approach
- Watch session progress on correct path
- Have misunderstandings resolved
- See issues being addressed
- Notice improved alignment with intent
- Feel session is back on track

## Troubleshooting

### Common Issue 1
**Problem**: Claude doesn't respond to your intervention
**Solution**:
- Check that message was sent successfully
- Wait 30-60 seconds for processing
- Be more explicit in your message
- Use stronger language ("Stop" vs "maybe try")
- Provide specific file paths and examples
- If still no response, consider cancelling session

### Common Issue 2
**Problem**: Intervention doesn't change Claude's approach
**Solution**:
- Rephrase with more direct instructions
- Provide code examples instead of descriptions
- Point to specific files and line numbers
- Break down into smaller, explicit steps
- Check if Claude acknowledged but didn't understand
- Consider that Claude might know something you don't - ask for explanation

### Common Issue 3
**Problem**: Too many interventions needed in one session
**Solution**:
- Cancel session and restart with better initial description
- Provide comprehensive context upfront
- Use more specific task description
- Consider that task might be too complex
- Break into smaller, more focused sessions
- Add project documentation to help future sessions

### Common Issue 4
**Problem**: Unsure if you should intervene or wait
**Solution**:
- Give Claude 2-3 more minutes
- Check if current action might resolve issue
- Intervene if clearly wrong (wrong framework, etc.)
- Wait if just slower than expected
- Intervene if stuck in obvious loop
- Trust your judgment - you know the codebase

### Common Issue 5
**Problem**: Intervention contradicts earlier instructions
**Solution**:
- Acknowledge the change explicitly
- Explain why the new direction is better
- Be clear about which instruction to follow
- Apologize for any confusion
- Provide updated, complete context
- Consider starting fresh if too confusing

## Additional Tips

### Prevention Over Intervention

**Reduce need for intervention:**

**Better initial task descriptions:**
```
❌ "Add authentication"
✅ "Add a login button that calls the existing Auth0 login()
   function from src/lib/auth/AuthProvider. Follow the pattern
   in SignupButton.js."
```

**Provide context upfront:**
- Reference existing patterns
- Specify libraries to use
- Mention what NOT to do
- Link to relevant files
- Note project conventions

### Intervention Timing

**Intervene early when:**
- Wrong framework/library being chosen
- Completely wrong direction
- Misunderstanding is obvious
- About to modify production files inappropriately

**Wait a bit when:**
- Approach is different but valid
- Still in analysis phase
- Handling errors systematically
- Making steady progress

### Tone and Communication

**Effective:**
- Clear and direct
- Specific and actionable
- Respectful but firm
- Solution-oriented

**Avoid:**
- Vague suggestions
- Passive language when decisive action needed
- Criticism without guidance
- Micromanagement of details

### Learning from Interventions

**After session, reflect:**
- Why was intervention needed?
- Was task description insufficient?
- What context was missing?
- How can I prevent this next time?

**Improve future sessions:**
- Add missing info to project README
- Create documentation for common patterns
- Write better initial task descriptions
- Build reusable context snippets

### When to Give Up and Restart

**Consider cancelling and restarting if:**
- Multiple interventions haven't helped
- Session is deeply confused
- Wrong approach too far along to pivot
- Easier to start fresh with better context
- Session has been running way too long

**Restart with:**
- Comprehensive task description
- All necessary context upfront
- References to relevant files
- Specific libraries and patterns to use
- Clear scope boundaries

## Related Articles
- KB-021: How to provide additional context to Claude during a task
- KB-024: How to ask Claude to revise its approach to a problem
- KB-007: How to describe a task effectively for Claude to understand
- KB-008: How to monitor a running task in real-time
- KB-010: How to cancel a running task if needed
- KB-023: How to approve or reject Claude's suggested changes

## Sources
1. AI Collaboration Best Practices - Search: "guiding AI coding assistants during development" (Accessed: November 16, 2025)
2. Claude Code Web Interactive Sessions - Search: "claude code web intervene during session" (Accessed: November 16, 2025)
3. Developer-AI Communication Patterns - Search: "effective intervention techniques AI pair programming" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
