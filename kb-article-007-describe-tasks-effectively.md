# How to Describe a Task Effectively for Claude to Understand

**Article ID**: KB-007
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 10 minutes

## Overview
The quality of your task description directly impacts how well Claude understands and executes your coding request. This guide teaches you how to write clear, specific, and actionable task descriptions that lead to better results.

## Prerequisites
- Understanding of Claude Code Web basics (KB-001 through KB-006)
- A connected repository
- A clear idea of what you want to accomplish

## Key Principles of Effective Task Descriptions

### 1. Be Specific and Clear
**Poor**: "Improve the code"
**Good**: "Refactor the getUserData() function in utils.js to use async/await instead of promises"

### 2. State the Desired Outcome
**Poor**: "Work on the login page"
**Good**: "Add email validation to the login form that shows an error message if the email format is invalid"

### 3. Provide Context When Needed
**Poor**: "Fix the bug"
**Good**: "Fix the bug in checkout.js where the total price calculates incorrectly when a discount code is applied"

### 4. Focus on One Main Objective
**Poor**: "Add a contact form, fix the navigation menu, and update the styling"
**Good**: "Add a contact form to the Contact page with fields for name, email, and message" (then create separate tasks for the other items)

## Task Description Template

Use this structure for well-formed task descriptions:

```
[Action] + [What] + [Where] + [Any specific requirements or context]
```

**Examples:**

```
Add a validateEmail function to utils.js that checks if an email address is in valid format
```

```
Fix the calculation error in calculateTotal() in cart.js where tax is being applied twice
```

```
Create a new React component called UserProfile that displays username and avatar
```

```
Update the README.md file to include installation instructions for Node.js v18+
```

## Effective Task Description Examples

### Simple Tasks

**Documentation:**
```
Add a code comment above the processPayment function explaining what it does and what parameters it expects
```

**Bug Fixes:**
```
Fix the typo in line 42 of index.html where "Welcom" should be "Welcome"
```

**Small Features:**
```
Add a "Clear All" button to the todo list that removes all completed items
```

### Medium Complexity Tasks

**New Feature:**
```
Implement a search bar in the header that filters the product list as the user types. The search should be case-insensitive and match product names.
```

**Refactoring:**
```
Convert the class-based ProductCard component to a functional component using React hooks, maintaining the same functionality
```

**Testing:**
```
Write unit tests for the calculateDiscount function in pricing.js, testing edge cases like zero prices, 100% discounts, and negative numbers
```

### Providing Additional Context

When your task requires understanding of existing code or specific requirements:

**Include Relevant Details:**
```
Add pagination to the blog posts list. The API returns a 'page' parameter. Display 10 posts per page with Next/Previous buttons. Reference how pagination is implemented in the Users page (users.js) for styling consistency.
```

**Specify Constraints:**
```
Create a password validation function that requires: minimum 8 characters, at least one uppercase letter, one number, and one special character. The function should return an object with validation status and specific error messages.
```

**Mention Related Files:**
```
Update the user authentication flow in auth.js to store the JWT token in localStorage instead of sessionStorage. Make sure to update the logout function in header.js to clear the token.
```

## Common Pitfalls to Avoid

### ❌ Too Vague
```
Make the website better
Work on the dashboard
Optimize performance
```

### ❌ Too Many Tasks in One
```
Add user authentication, create a dashboard, implement notifications, and set up email confirmation
```

### ❌ Missing Critical Information
```
Add validation
```
*What should be validated? Where? What are the validation rules?*

### ❌ Assuming Too Much Context
```
Fix it like we discussed
```
*Claude doesn't have access to previous conversations or discussions*

## ✅ Well-Formed Task Descriptions

### Example 1: New Feature
```
Add a dark mode toggle button to the navigation bar. When clicked, it should:
- Switch between light and dark color schemes
- Save the preference to localStorage
- Apply the appropriate CSS classes to the body element
```

### Example 2: Bug Fix
```
Fix the infinite loop bug in useEffect hook in Dashboard.js (line 45-52) that occurs when the user object updates. Add a dependency array with only the user.id property.
```

### Example 3: Refactoring
```
Extract the repeated form validation logic from login.js, register.js, and profile.js into a reusable validateForm() utility function in utils/validation.js
```

### Example 4: Testing
```
Add integration tests for the checkout flow:
1. Adding items to cart
2. Applying a discount code
3. Proceeding to checkout
4. Successful payment submission
Use the same testing approach as in the existing auth.test.js file
```

## How to Improve Your Task Descriptions

### Start With Action Verbs
- **Add** - for new features or components
- **Fix** - for bugs and errors
- **Update/Modify** - for changes to existing code
- **Refactor** - for code improvements without changing functionality
- **Create** - for new files, functions, or components
- **Remove/Delete** - for removing code or features
- **Convert** - for changing from one approach to another
- **Implement** - for adding functionality based on a specification

### Include File and Function Names
```
Good: "Update the calculateTax function in utils/pricing.js"
Better than: "Update the tax calculation"
```

### Specify Expected Behavior
```
Good: "The button should turn green and display 'Saved!' for 2 seconds when clicked"
Better than: "Add feedback to the save button"
```

### Reference Existing Patterns
```
Good: "Style this component to match the UserCard component in components/UserCard.js"
Better than: "Style this component nicely"
```

## Interactive Approach

You don't need to include everything in the initial description. Claude can ask clarifying questions. Here's a balanced approach:

**Initial Task:**
```
Add form validation to the contact form
```

**Claude Might Ask:**
- What fields need validation?
- What are the validation rules?
- Should error messages appear inline or at the top?
- Should validation occur on blur or on submit?

**Better Initial Task:**
```
Add form validation to the contact form in contact.html. Validate that:
- Name field is not empty
- Email field contains a valid email format
- Message field has at least 10 characters
Show error messages below each field in red text. Validate on form submit.
```

## Tips for Complex Tasks

### Break Down Large Tasks
Instead of:
```
Build a complete user authentication system
```

Create separate tasks:
```
Task 1: Create user registration form with email and password fields
Task 2: Implement password hashing and user creation API endpoint
Task 3: Create login form and JWT token generation
Task 4: Add protected route middleware for authenticated users
Task 5: Implement logout functionality
```

### Provide Examples or References
```
Create an API endpoint for fetching user posts, similar to the existing /api/users endpoint in api.js, but filtered by user ID
```

### Link to External Resources (When Helpful)
```
Implement OAuth2 authentication following the GitHub OAuth flow. Reference: https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps
```

## Expected Results

After learning to describe tasks effectively, you should:
- Write task descriptions that Claude understands immediately
- Reduce back-and-forth clarification questions
- Get more accurate implementations on first try
- Complete tasks faster with fewer revisions
- Feel confident breaking down complex requirements into clear instructions

## Troubleshooting

### Common Issue 1
**Problem**: Claude asks many clarifying questions
**Solution**:
- Add more specifics to your initial description
- Include file names, function names, and expected behaviors
- Specify validation rules, edge cases, and constraints
- Reference existing code patterns when applicable

### Common Issue 2
**Problem**: Claude implements something different than intended
**Solution**:
- Be more explicit about the desired outcome
- Provide examples of expected input and output
- Specify which files or components should be modified
- Avoid ambiguous language like "improve" or "optimize"

### Common Issue 3
**Problem**: Task seems too complex to describe concisely
**Solution**:
- Break it into smaller, manageable sub-tasks
- Create multiple separate tasks instead of one large one
- Start with the first step and iterate
- Use the interactive approach - provide basics and answer questions

### Common Issue 4
**Problem**: Unsure how much detail to provide
**Solution**:
- Start with: What, Where, and Why
- Add specific requirements that matter to you
- Don't over-specify implementation details unless necessary
- Let Claude use its judgment for routine decisions (like variable names)

## Additional Tips
- Use natural language - no special syntax required
- Be conversational but specific
- Include acceptance criteria when helpful ("The feature is complete when...")
- Mention any constraints (performance, compatibility, style guides)
- Reference related files or similar implementations
- Update the task description during the session if needed
- Don't worry about perfect descriptions - you can guide Claude during execution

## Related Articles
- KB-006: How to create your first coding task in Claude Code Web
- KB-021: How to provide additional context to Claude during a task
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-024: How to ask Claude to revise its approach to a problem

## Sources
1. Effective Prompt Engineering for Code - Search: "prompt engineering best practices coding AI" (Accessed: November 16, 2025)
2. Claude Code Web Task Description Guide - Search: "claude code web how to write good task descriptions" (Accessed: November 16, 2025)
3. AI Coding Assistant Communication - Search: "how to describe coding tasks to AI assistants" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
