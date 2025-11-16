# How to Generate Documentation for Your Code

**Article ID**: KB-055
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15-30 minutes

## Overview
Claude Code Web can automatically generate documentation for your codebase, including README files, code comments, docstrings, API documentation, and technical guides. This capability helps you maintain up-to-date documentation without manual effort, ensuring your code is well-documented and accessible to other developers.

## Prerequisites
- Active Claude Pro or Max subscription
- GitHub repository connected to Claude Code Web (KB-004)
- Understanding of how to create tasks effectively (KB-007)
- Code files that need documentation
- Clear idea of what documentation format you need

## Steps

### Step 1: Determine What Documentation You Need

Before asking Claude to generate documentation, identify the specific type:

**Common Documentation Types:**
- **README files** - Project overview, installation, usage instructions
- **Code comments** - Inline explanations of code logic
- **Docstrings/JSDoc** - Function and class documentation
- **API documentation** - Endpoint descriptions, parameters, responses
- **Architecture guides** - System design and component relationships
- **Changelog files** - Version history and updates
- **Contributing guides** - Development setup and contribution process

Choose one type to start with rather than requesting "all documentation" at once.

### Step 2: Prepare Your Documentation Requirements

Decide on the specifics for your documentation:

**Format Considerations:**
- Documentation style (JSDoc, Google Style, Numpy Style, etc.)
- Level of detail (brief summaries vs. comprehensive)
- Tone (formal for enterprise, casual for open source)
- Language (English, or specify another language)
- Examples needed (yes/no, how many)

**Example Requirements:**
```
I want JSDoc comments for all functions in utils.js with:
- Brief description
- @param tags for each parameter
- @returns tag
- @example with one usage example
```

### Step 3: Create a Task for README Generation

If you need a README file, use a clear, specific task description:

**Basic README Task:**
```
Create a README.md file for this project that includes:
- Project title and description
- Installation instructions
- Basic usage examples
- List of main dependencies
- License information
```

**Comprehensive README Task:**
```
Generate a complete README.md with the following sections:
1. Project overview and purpose
2. Features list
3. Prerequisites
4. Installation steps for development environment
5. Usage examples with code snippets
6. Configuration options
7. API reference (if applicable)
8. Contributing guidelines
9. License
10. Contact information

Use clear formatting with headers, code blocks, and bullet points.
```

### Step 4: Create a Task for Code Comments and Docstrings

For adding comments and docstrings to existing code:

**Single File Documentation:**
```
Add comprehensive JSDoc comments to all functions in src/utils/validation.js. Each function should include:
- A clear description of what it does
- @param tags with types and descriptions
- @returns tag with type and description
- @example with at least one usage example
- Any relevant @throws tags for error cases
```

**Multiple Files Documentation:**
```
Add Python docstrings following Google Style Guide to all functions and classes in the src/services/ directory. Include:
- Brief description
- Args: parameter descriptions with types
- Returns: return value description with type
- Raises: any exceptions that might be raised
- Example usage where helpful
```

**Inline Comments Task:**
```
Add inline comments to the complex logic in src/algorithms/sorting.js explaining:
- The algorithm being used (Quick Sort)
- Why specific optimizations were made
- Edge cases being handled
- Time and space complexity
Keep comments concise and focused on the "why" not the "what"
```

### Step 5: Create a Task for API Documentation

For documenting API endpoints:

**API Documentation Task:**
```
Create API documentation for all endpoints in api/routes/users.js. For each endpoint, document:
- HTTP method and path
- Description of what it does
- Request parameters (path, query, body)
- Request body schema with example JSON
- Response format with example JSON
- Possible status codes and error responses
- Authentication requirements

Format as a markdown file: docs/api/users-api.md
```

**OpenAPI/Swagger Task:**
```
Generate an OpenAPI 3.0 specification (swagger.yaml) for all API endpoints in the routes/ directory. Include:
- All paths and operations
- Request/response schemas
- Authentication schemes
- Example requests and responses
- Error codes and descriptions
```

### Step 6: Create a Task for Architecture Documentation

For system-level documentation:

**Architecture Guide Task:**
```
Create a technical architecture document (docs/architecture.md) that explains:
1. Overall system design and components
2. Directory structure and purpose of each folder
3. Data flow between components
4. Key design patterns used
5. External dependencies and why they were chosen
6. Database schema overview
7. Deployment architecture

Include diagrams using mermaid syntax where helpful.
```

### Step 7: Update Existing Documentation

If documentation exists but is outdated:

**Update Task:**
```
Update the README.md file to reflect recent changes:
- Add the new authentication feature to the Features section
- Update installation instructions to include Redis requirement
- Add examples for using the new API endpoints
- Update the dependencies list with current versions
- Revise the configuration section with new environment variables
```

### Step 8: Generate CLAUDE.md for Project Standards

Create a CLAUDE.md file to document your project conventions for future Claude sessions:

**CLAUDE.md Task:**
```
Create a CLAUDE.md file in the repository root that documents:
- Project code style and conventions (ESLint, Prettier settings)
- Testing approach and how to run tests
- Common commands (build, test, deploy)
- Branch naming conventions
- Commit message format
- File organization principles
- Key files and their purposes
- Any project-specific quirks or gotchas

Format it for clarity and conciseness. This will help Claude Code understand the project better in future sessions.
```

### Step 9: Monitor Documentation Generation

As Claude generates documentation:

1. **Review the approach** - Check that Claude understands your requirements
2. **Provide feedback** - If Claude misunderstands, clarify immediately
3. **Check examples** - Ensure code examples are accurate and helpful
4. **Verify completeness** - Confirm all requested sections are included
5. **Review technical accuracy** - Check that descriptions match actual code behavior

See KB-008 for monitoring task progress in detail.

### Step 10: Review and Refine Generated Documentation

After Claude completes the documentation:

**Quality Checks:**
- **Accuracy** - Does documentation match actual code behavior?
- **Completeness** - Are all important aspects covered?
- **Clarity** - Is it easy to understand for the target audience?
- **Examples** - Are code examples correct and helpful?
- **Formatting** - Is markdown properly formatted?
- **Links** - Do internal references and links work?

**Request Refinements:**
```
The API documentation looks good, but please:
1. Add more detailed examples for the POST /api/users endpoint
2. Include error response examples for validation failures
3. Add a section about rate limiting
```

## Expected Results

After successfully generating documentation with Claude Code Web, you should have:

- Well-structured documentation files in your repository
- Clear, accurate descriptions of code functionality
- Properly formatted comments or docstrings in your code
- Examples that help users understand how to use your code
- Documentation that follows your specified style guide
- Files committed to a new branch ready for review
- A pull request (if requested) with the documentation changes

The documentation should be:
- Technically accurate and synchronized with actual code
- Properly formatted in markdown or your chosen format
- Easy to read and understand for your target audience
- Complete with all requested sections
- Ready to merge or further refine based on team feedback

## Troubleshooting

### Common Issue 1
**Problem**: Generated documentation is too technical or too basic for the intended audience
**Solution**:
- Specify the target audience in your task description: "Write for junior developers who are new to this framework"
- Request a specific tone: "Use a friendly, tutorial style with lots of examples"
- Provide examples of the documentation style you want
- Ask Claude to revise with specific guidance: "Make this more beginner-friendly by explaining technical terms"

### Common Issue 2
**Problem**: Code examples in documentation are incorrect or outdated
**Solution**:
- Ask Claude to verify examples against actual code: "Please verify these examples work with the current implementation"
- Request that Claude test code snippets if possible
- Specify that examples should use actual function signatures from the codebase
- Review and test examples yourself before merging

### Common Issue 3
**Problem**: Documentation format doesn't match project standards
**Solution**:
- Provide a reference file: "Format comments like in src/utils/helpers.js"
- Specify the exact style guide: "Use Google Style Python docstrings"
- Include formatting requirements in your task description
- Reference your project's CLAUDE.md if it contains documentation standards

### Common Issue 4
**Problem**: Claude documents implementation details instead of usage
**Solution**:
- Clarify the documentation purpose: "This is user-facing API documentation, not internal implementation details"
- Request focus on "what" and "why" rather than "how": "Document what each function does from a user's perspective"
- Provide examples of the level of abstraction you want

### Common Issue 5
**Problem**: Generated README is generic or missing project-specific details
**Solution**:
- Provide more context about your project's unique features and purpose
- Specify special requirements: "Include sections about our microservices architecture and Docker setup"
- Reference existing documentation or project details
- Ask Claude to analyze specific files to understand project specifics

### Common Issue 6
**Problem**: Documentation is incomplete or missing sections
**Solution**:
- Be explicit about all required sections in your task description
- Use numbered lists or checkboxes for required elements
- Review Claude's plan before it starts writing to confirm all sections are included
- Request additions: "Please add a Troubleshooting section to the README"

## Additional Tips

### Best Practices for Documentation Tasks
- Start with one documentation type at a time (e.g., README first, then docstrings)
- Provide examples of documentation style you like from other projects
- Specify any style guides or conventions your project follows
- Ask Claude to prioritize the most important or frequently-used functions first
- Review generated documentation before committing to ensure accuracy

### Maintaining Documentation Standards
- Create a CLAUDE.md file with your documentation conventions
- Include documentation requirements in that file for consistency
- Reference existing well-documented files as templates
- Keep documentation style consistent across the project

### Documentation That Claude Excels At
- README files with structure and examples
- Function and class docstrings/comments
- API endpoint documentation
- Code explanations for complex algorithms
- Installation and setup guides
- Changelog generation from git history
- Architecture overviews based on code analysis

### Combining Documentation with Code Changes
You can ask Claude to document while implementing:
```
Add a new calculateDiscount function to pricing.js that applies percentage discounts to prices. Include comprehensive JSDoc comments with parameter types, return type, and usage examples.
```

### Iterative Refinement
Don't expect perfect documentation on the first try:
1. Start with a basic documentation generation task
2. Review the output
3. Request specific improvements
4. Repeat until satisfied

### Using Documentation Templates
Provide Claude with templates:
```
Generate API documentation for all endpoints using this template for each:

## [HTTP Method] [Path]

**Description:** [What this endpoint does]

**Authentication:** [Required/Not required]

**Parameters:**
- [Parameter name] ([type]): [description]

**Request Body:**
[JSON schema and example]

**Response:**
[JSON schema and example]

**Error Responses:**
[Status codes and descriptions]
```

### Leveraging Claude's Code Understanding
Claude analyzes your code to generate accurate documentation:
- It can identify function parameters and return types
- It understands code flow and logic
- It recognizes common patterns and frameworks
- It can infer purpose from variable and function names

## Related Articles
- KB-006: How to create your first coding task in Claude Code Web
- KB-007: How to describe a task effectively for Claude to understand
- KB-015: How to view changes made by Claude before accepting them
- KB-021: How to provide additional context to Claude during a task
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-023: How to approve or reject changes Claude suggests
- KB-024: How to ask Claude to revise its approach to a problem

## Sources
1. Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
2. Can Claude Code Really Generate Professional Documentation From Your Code? - https://apidog.com/blog/claude-code-generate-documentation-from-code/ (Accessed: November 16, 2025)
3. How to Generate Documentation & Unit Tests with Claude Code Plugin - https://skywork.ai/blog/how-to-generate-documentation-unit-tests-claude-code-plugin/ (Accessed: November 16, 2025)
4. Claude Code Overview and Features - Google Search: "Claude Code Web documentation generation capabilities 2025" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
