# How to Provide Additional Context to Claude During a Task

**Article ID**: KB-021
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 6 minutes

## Overview
Sometimes Claude needs more information to complete a task effectively. This guide shows you how to provide additional context, clarifications, and guidance during an active session to help Claude better understand your requirements and deliver the results you need.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one active or running session
- Understanding of task descriptions (KB-007)
- Familiarity with monitoring sessions (KB-008)

## Understanding Context in Claude Code Web

### Why Additional Context Matters

**Context helps Claude:**
- Understand project-specific conventions
- Make better implementation decisions
- Avoid assumptions
- Follow your preferred patterns
- Handle edge cases appropriately
- Deliver results aligned with your expectations

### When to Provide Additional Context

**Common scenarios:**

**Claude asks for clarification:**
```
Claude: "I found two potential approaches for implementing
the validation:
1. Client-side validation with real-time feedback
2. Server-side validation on form submit
Which would you prefer?"
```

**You notice Claude needs guidance:**
- Going in wrong direction
- About to use outdated pattern
- Not aware of project constraints
- Missing important requirements

**You want to influence implementation:**
- Specific library preferences
- Performance requirements
- Security considerations
- Styling guidelines

**You realize initial description was incomplete:**
- Forgot to mention edge cases
- Need to clarify scope
- Want to add requirements
- Have additional constraints

## Steps to Provide Context During a Session

### Step 1: Access the Active Session

**Navigate to the running session:**
1. Go to `https://claude.com/code`
2. Click on "Active Sessions"
3. Find and open the session needing context
4. Wait for session detail view to load

### Step 2: Locate the Interaction Area

**Find where to communicate:**

**Look for:**
- Chat interface within session view
- Message input box
- "Send message to Claude" area
- Text field with send button
- "Add context" or "Provide feedback" section

**Common locations:**
- Bottom of session view
- Sidebar panel
- Fixed footer
- Floating chat widget

### Step 3: Type Your Additional Context

**Write your message:**

**Be clear and specific:**

**Example - Clarifying requirements:**
```
Additional context: The email validation should also check
for common typos in popular email domains (gmial.com → gmail.com).
Show a confirmation dialog asking "Did you mean gmail.com?"
before accepting the form.
```

**Example - Providing technical details:**
```
For the API integration, use the axios library (already in
package.json). The API endpoint is https://api.example.com/users
and requires Bearer token authentication. Token is stored in
localStorage under 'authToken'.
```

**Example - Specifying constraints:**
```
Important: Keep the existing function signature for calculateTotal()
because it's called from multiple other components. Only change
the internal implementation.
```

**Example - Clarifying preferences:**
```
Prefer functional components with hooks rather than class components.
Follow the pattern used in src/components/UserProfile.js for
consistency with our existing codebase.
```

### Step 4: Send the Context

**Submit your message:**
1. Review your message for clarity
2. Click "Send" or press Enter
3. Wait for Claude to acknowledge
4. Watch for Claude to incorporate the context

**Confirmation indicators:**
```
You: [Your context message]

Claude: "Thanks for the clarification. I'll implement email
validation with typo detection for common domains. Proceeding
with that approach now."
```

### Step 5: Monitor How Context is Applied

**Observe Claude's actions:**
- Check activity log for acknowledgment
- Watch for changes in approach
- Verify new direction aligns with context
- Ensure requirements are understood

## Types of Context to Provide

### 1. Technical Specifications

**Be specific about implementation:**

**Examples:**
```
"Use bcrypt for password hashing, not md5"

"The database uses PostgreSQL, not MySQL"

"Tailwind CSS classes should follow our design system
in tailwind.config.js"

"API rate limit is 100 requests/minute"
```

### 2. Project Conventions

**Explain local standards:**

**Examples:**
```
"All component files should include a default export
and named exports for sub-components"

"Error messages are stored in src/constants/messages.js"

"We use absolute imports from @/components, not relative paths"

"Function names should be camelCase, not snake_case"
```

### 3. Business Logic

**Clarify business rules:**

**Examples:**
```
"Discounts can't be combined - if multiple codes are entered,
use only the one with highest value"

"Users under 18 can't access premium features, even with
parental consent"

"Prices should always display with 2 decimal places,
rounded up (ceiling)"

"Session timeout is 30 minutes of inactivity"
```

### 4. Edge Cases and Constraints

**Highlight special cases:**

**Examples:**
```
"Handle the case where the API returns an empty array -
show 'No results found' message, not an error"

"Maximum file upload size is 5MB"

"Support both light and dark mode - don't hard-code colors"

"Component must work on mobile screens (min-width: 320px)"
```

### 5. References to Existing Code

**Point to examples:**

**Examples:**
```
"Follow the same pattern as UserAuthentication.js
for the login flow"

"Use the same error handling approach as in
src/utils/errorHandler.js"

"Match the styling of the existing dashboard cards
in Dashboard.css"

"Reference the API integration in src/services/api.js"
```

### 6. Priority and Scope

**Clarify what's important:**

**Examples:**
```
"Focus on getting the core functionality working first.
Error handling and edge cases can come in a follow-up session."

"This is for a production hotfix - prioritize reliability
over code elegance"

"Nice-to-have: Add loading animations, but not required
for this task"

"Must be completed today for release"
```

## Best Practices for Providing Context

### Timing

**When to provide context:**

**✅ Good timing:**
- When Claude explicitly asks
- After Claude shares its plan, if you see issues
- Early in session if you have important constraints
- When you notice assumptions being made

**❌ Avoid:**
- Constant interruptions for minor details
- Providing everything upfront (let Claude ask)
- Adding context for decisions Claude handles well
- Changing requirements mid-implementation

### Clarity

**Make context easy to understand:**

**✅ Clear context:**
```
"The payment gateway API expects amounts in cents (e.g., 1000 = $10.00),
not dollars. Convert before sending."
```

**❌ Unclear context:**
```
"Fix the money thing"
```

**✅ Specific:**
```
"Add validation to prevent empty strings. Trim whitespace before
checking. Minimum length after trimming: 3 characters."
```

**❌ Vague:**
```
"Make sure validation is good"
```

### Volume

**Right amount of context:**

**✅ Appropriate:**
- 1-3 key points per message
- Essential information only
- Direct and concise
- Focused on current need

**❌ Too much:**
- Long essays of information
- Every possible edge case
- Detailed implementation steps
- Information Claude can infer

**❌ Too little:**
- Single word responses
- Ambiguous references
- Assumes Claude has context it doesn't

## Expected Results

After providing additional context, you should:
- See Claude acknowledge your message
- Observe changes in implementation approach
- Notice alignment with your requirements
- Have concerns addressed
- See questions answered
- Watch session progress with new direction
- Feel confident Claude understands

## Troubleshooting

### Common Issue 1
**Problem**: Claude doesn't seem to incorporate your context
**Solution**:
- Check if message was actually sent
- Verify Claude acknowledged receipt
- Be more explicit in your phrasing
- Provide concrete examples
- Rephrase using different words
- Reference specific files or code sections

### Common Issue 2
**Problem**: Can't find where to send messages to Claude
**Solution**:
- Look for chat interface in session view
- Check for expandable panels or sidebars
- Try clicking in different areas of session view
- Platform interface may vary
- Some sessions may not support mid-session messaging
- Consider cancelling and restarting with fuller context

### Common Issue 3
**Problem**: Provided too much context and Claude seems confused
**Solution**:
- Simplify your message
- Focus on one thing at a time
- Use numbered lists for multiple points
- Provide examples rather than abstractions
- Reference specific files instead of general concepts

### Common Issue 4
**Problem**: Context contradicts earlier instructions
**Solution**:
- Acknowledge the change explicitly
- Say "Actually, I need to correct that..."
- Clarify which instruction takes precedence
- Explain why the change is needed
- Ask Claude to confirm new understanding

### Common Issue 5
**Problem**: Unsure if context is needed or if Claude will figure it out
**Solution**:
- Err on the side of providing context for critical items
- Let Claude handle routine decisions
- Provide context if project-specific
- Wait to see Claude's approach first
- Add context if you see issues emerging

## Additional Tips

### Proactive vs Reactive Context

**Proactive (at session start):**
```
Initial task: "Add user registration form"

Immediate context: "Important: Use Formik library for form
management and Yup for validation. Follow the pattern in
LoginForm.js. Required fields: email, password, confirm password,
username."
```

**Reactive (during session):**
```
[Claude starts implementing]
You notice: Using a different pattern than existing forms

Reactive context: "Please use Formik like our other forms.
See LoginForm.js for the pattern we're following."
```

### Context Layering

**Build context progressively:**

**Layer 1 - Essential:**
```
"Use PostgreSQL for database, tables already exist"
```

**Layer 2 - If needed:**
```
"Connection details are in .env file"
```

**Layer 3 - If Claude asks:**
```
"Use the pg library, not Sequelize. Connection pool is
already configured in src/db/connection.js"
```

### Saving Context for Future Sessions

**Reusable context:**

Consider documenting common context in:
- README.md
- CONTRIBUTING.md
- .claude/settings.json (if supported)
- Project documentation
- Coding standards document

Then reference: "Follow conventions in CONTRIBUTING.md"

### Context Templates

**Prepare common context snippets:**

**For API integrations:**
```
"API integration context:
- Endpoint: [URL]
- Authentication: [type and location]
- Rate limits: [limits]
- Error format: [structure]
- Retry logic: [yes/no and details]"
```

**For styling:**
```
"Styling context:
- Framework: [Tailwind/Material-UI/etc]
- Color scheme: [reference]
- Responsive breakpoints: [values]
- Component reference: [file]"
```

## Related Articles
- KB-007: How to describe a task effectively for Claude to understand
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-024: How to ask Claude to revise its approach to a problem
- KB-008: How to monitor a running task in real-time
- KB-025: How to understand Claude's responses and actions in the interface

## Sources
1. Effective AI Prompt Engineering - Search: "providing context to AI coding assistants" (Accessed: November 16, 2025)
2. Claude Code Web Interactive Sessions - Search: "claude code web provide context during session" (Accessed: November 16, 2025)
3. Collaborative Coding Best Practices - Search: "developer AI collaboration communication" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
