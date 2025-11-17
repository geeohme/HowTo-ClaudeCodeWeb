# How to create a custom skill for repeated workflows

**Article ID**: KB-083
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview
Skills are modular, self-contained packages that extend Claude's capabilities by providing specialized knowledge, workflows, and tools. They enable you to transform recurring workflows into reusable components that Claude can automatically discover and apply when relevant. Instead of pasting the same long prompt repeatedly, you create a skill once and Claude loads it dynamically when needed, ensuring consistent execution across all sessions.

## Prerequisites
- Claude Code Web with an active Pro, Max, Team, or Enterprise subscription
- Basic understanding of YAML syntax
- Familiarity with markdown formatting
- A clearly defined repeatable workflow you want to automate
- (Optional) Knowledge of bash scripting for advanced skills with bundled scripts

## Understanding Skills

### What Are Skills?

Skills are folders containing instructions, scripts, and resources that Claude discovers and loads dynamically when relevant to a task. Think of them as "a neatly labeled toolkit" that packages:

- **Specialized workflows** for specific domains (e.g., code review using OWASP standards)
- **Tool integrations** for file formats and APIs (e.g., XLSX manipulation, PDF extraction)
- **Domain expertise** and business logic (e.g., brand guidelines, internal communication templates)
- **Bundled resources** including scripts, references, and assets

### Skills vs. Other Claude Code Features

**Skills vs. Slash Commands**:
- Slash commands are prompt templates you invoke explicitly (e.g., `/review-pr`)
- Skills load automatically when Claude detects relevance
- Use slash commands for ad-hoc tasks; use skills for recurring patterns

**Skills vs. Hooks**:
- Hooks execute at specific lifecycle events (SessionStart, PreBash, etc.)
- Skills provide instructions and context for Claude to execute tasks
- Hooks are automation; skills are knowledge packages

**Skills vs. MCP Servers**:
- MCP servers provide external tool integrations (databases, APIs)
- Skills provide workflow instructions and domain knowledge
- MCP extends capabilities; skills guide behavior

### When to Create a Skill

Create a skill when you:
- Type the same prompt repeatedly across multiple conversations
- Need consistent execution of complex workflows (e.g., "review for security vulnerabilities")
- Want to share domain expertise with your team
- Have specialized formatting requirements (e.g., "executive summary with key findings")
- Need to package scripts and resources together

## Steps

### Step 1: Define Your Workflow and Use Cases

Before creating a skill, clearly define what it should accomplish.

**Ask yourself**:
1. What task does this skill perform?
2. When should Claude use this skill?
3. What are concrete examples of usage?
4. What resources (scripts, templates, documentation) does it need?

**Example**: A code review skill

```markdown
Purpose: Review code for security vulnerabilities using OWASP standards
When to use: When reviewing pull requests or analyzing code files
Examples:
  - "Review this authentication module for security issues"
  - "Check this API endpoint for OWASP Top 10 vulnerabilities"
Resources needed:
  - OWASP Top 10 checklist (reference document)
  - Security scanning script (optional)
```

**Document your workflow**: Write out step-by-step what Claude should do when using this skill.

### Step 2: Choose Your Creation Method

There are two approaches to creating skills:

**Method A: Manual Creation** - Create the folder structure and SKILL.md file yourself
- Best for: Simple skills, learning the structure, full control
- Time: 10-15 minutes

**Method B: Use skill-creator** - Interactive guided creation using the built-in skill-creator skill
- Best for: Complex skills, first-time creators, rapid prototyping
- Time: 5-10 minutes
- Requires: skill-creator skill installed in Claude Code

For this guide, we'll cover both methods. Choose based on your comfort level.

### Step 3A: Manual Creation - Create the Folder Structure

If creating manually, start by creating a folder for your skill.

**Location**: Skills can be stored locally in `~/.claude/skills/` or in a repository you can share.

```bash
# Create a skill folder (use lowercase, hyphens for spaces)
mkdir -p ~/.claude/skills/code-security-reviewer

# Navigate to the folder
cd ~/.claude/skills/code-security-reviewer
```

**Naming convention**: Use descriptive, hyphenated lowercase names (e.g., `code-security-reviewer`, `brand-guidelines`, `api-doc-generator`).

### Step 3B: Using skill-creator - Interactive Creation

If using the skill-creator skill, Claude will guide you through the process.

**Prerequisites**: First install the skill-creator from the anthropics/skills marketplace:

```
/plugin marketplace add anthropics/skills
/plugin install example-skills@anthropic-agent-skills
```

**Then invoke it**:

```
Use the skill-creator skill to create a new skill for reviewing code security
```

Claude will ask you questions about:
1. Concrete examples of how you'll use the skill
2. What resources are needed (scripts, references, assets)
3. The skill name and description

The skill-creator will generate the folder structure, format the SKILL.md file, and bundle resources automatically.

**For the rest of this guide, we'll focus on manual creation to understand the full structure.**

### Step 4: Create the SKILL.md File with YAML Frontmatter

Every skill requires a `SKILL.md` file with YAML frontmatter and markdown instructions.

Create the file:

```bash
touch SKILL.md
```

**Basic SKILL.md structure**:

```yaml
---
name: code-security-reviewer
description: Reviews code for security vulnerabilities using OWASP Top 10 standards. Use this skill when analyzing code for security issues, reviewing authentication/authorization logic, or evaluating API endpoints for common vulnerabilities.
---

# Code Security Reviewer

You are a security-focused code reviewer specializing in identifying vulnerabilities based on OWASP Top 10 standards.

## Review Process

When reviewing code, follow these steps:

1. **Identify the code type**: Determine if it's authentication, API endpoint, data handling, etc.
2. **Apply OWASP checklist**: Review against relevant OWASP Top 10 categories
3. **Flag vulnerabilities**: Identify specific issues with severity (Critical, High, Medium, Low)
4. **Provide remediation**: Suggest concrete fixes for each vulnerability
5. **Summarize findings**: Provide executive summary of security posture

## OWASP Top 10 Checklist

- **A01: Broken Access Control** - Check for missing authorization, insecure direct object references
- **A02: Cryptographic Failures** - Verify encryption at rest and in transit, key management
- **A03: Injection** - Look for SQL, NoSQL, OS command, LDAP injection vulnerabilities
- **A04: Insecure Design** - Evaluate threat modeling, secure design patterns
- **A05: Security Misconfiguration** - Check default credentials, unnecessary features, error messages
- **A06: Vulnerable Components** - Identify outdated dependencies with known vulnerabilities
- **A07: Authentication Failures** - Review session management, credential storage, MFA
- **A08: Software and Data Integrity** - Verify CI/CD pipeline security, unsigned updates
- **A09: Security Logging** - Check logging of security events, monitoring
- **A10: Server-Side Request Forgery** - Validate URL inputs, network segmentation

## Output Format

Provide findings in this structure:

### Executive Summary
[Brief overview of security posture and critical findings]

### Detailed Findings

#### Finding 1: [Vulnerability Title]
- **Severity**: [Critical/High/Medium/Low]
- **OWASP Category**: [A0X]
- **Location**: [File:line or function name]
- **Description**: [What the vulnerability is]
- **Impact**: [What could happen if exploited]
- **Remediation**: [Specific code changes needed]

[Repeat for each finding]

### Recommendations
[Prioritized list of security improvements]

## Examples

- "Review this Express.js authentication middleware for security vulnerabilities"
- "Analyze this user registration endpoint for OWASP Top 10 issues"
- "Check this password reset function for security weaknesses"

## Guidelines

- Always provide specific line numbers or function names when identifying issues
- Include code snippets showing both vulnerable and fixed versions
- Prioritize findings by severity and exploitability
- Consider the full attack chain, not just isolated issues
- Be thorough but avoid false positives
```

### Step 5: Understand YAML Frontmatter Fields

The YAML frontmatter at the top of SKILL.md defines metadata Claude uses to discover and load your skill.

**Required fields**:

```yaml
name: my-skill-name
```
- Unique identifier for the skill
- Must be lowercase, hyphenated (no spaces)
- Should match the containing directory name
- Restricted to Unicode alphanumeric characters and hyphens

```yaml
description: A complete description of what the skill does and when to use it
```
- Explains what the skill does and when Claude should use it
- This is critical: Claude uses the description to determine relevance
- Be specific and comprehensive
- Include keywords that trigger the skill's usage

**Optional fields**:

```yaml
license: MIT
```
- Specifies the skill's license terms
- Keep brief (e.g., "MIT", "Apache-2.0", "Proprietary")

```yaml
allowed-tools:
  - Bash
  - Read
  - Edit
```
- Pre-approved tools the skill can use
- Currently supported in Claude Code only
- Prevents prompting for permission on common tools

```yaml
metadata:
  author: Your Name
  version: "1.0"
  category: security
  tags: ["code-review", "security", "owasp"]
```
- Custom key-value pairs for extended properties
- Use unique keys to prevent conflicts with other skills
- Useful for organizational purposes

### Step 6: Add Bundled Resources (Optional)

Skills can include bundled resources beyond just the SKILL.md file. These are organized in specific directories:

**Directory structure**:

```
code-security-reviewer/
├── SKILL.md (required)
├── scripts/ (optional - executable code)
│   ├── scan_dependencies.py
│   └── check_secrets.sh
├── references/ (optional - documentation loaded into context as needed)
│   └── owasp-top-10-detailed.md
└── assets/ (optional - files used in output, NOT loaded into context)
    ├── report-template.md
    └── security-checklist.pdf
```

**Scripts directory** (`scripts/`):
- Contains executable code for deterministic tasks
- Python, Bash, JavaScript, or other languages
- Claude can run these scripts when needed
- Example: Dependency vulnerability scanner

**Example script** (`scripts/scan_dependencies.py`):

```python
#!/usr/bin/env python3
"""Scan package dependencies for known vulnerabilities."""

import json
import subprocess
import sys

def scan_npm_dependencies():
    """Run npm audit and return results."""
    try:
        result = subprocess.run(
            ["npm", "audit", "--json"],
            capture_output=True,
            text=True,
            timeout=30
        )
        audit_data = json.loads(result.stdout)
        return audit_data
    except Exception as e:
        print(f"Error scanning dependencies: {e}", file=sys.stderr)
        return None

if __name__ == "__main__":
    results = scan_npm_dependencies()
    if results:
        vulnerabilities = results.get("vulnerabilities", {})
        print(f"Found {len(vulnerabilities)} vulnerabilities")
        for name, data in vulnerabilities.items():
            print(f"- {name}: {data.get('severity', 'unknown')} severity")
```

**References directory** (`references/`):
- Documentation loaded into context when needed
- Markdown, text, or other readable formats
- Provides deep domain knowledge
- Example: Detailed OWASP guidelines

**Assets directory** (`assets/`):
- Files used in output generation
- Templates, icons, fonts, boilerplate code
- NOT loaded into Claude's context
- Claude can read and use these when generating output
- Example: Report templates, configuration files

### Step 7: Test Your Skill Locally

Before installing or sharing, test your skill to ensure it works correctly.

**Validate YAML syntax**:

```bash
# Check if SKILL.md has valid YAML frontmatter
python3 << 'EOF'
import yaml
import sys

with open('SKILL.md', 'r') as f:
    content = f.read()

# Extract frontmatter between --- markers
if content.startswith('---'):
    parts = content.split('---', 2)
    if len(parts) >= 3:
        frontmatter = parts[1]
        try:
            data = yaml.safe_load(frontmatter)
            print("✓ Valid YAML frontmatter")
            print(f"  Name: {data.get('name')}")
            print(f"  Description: {data.get('description')[:60]}...")

            # Check required fields
            if not data.get('name'):
                print("✗ Missing required field: name")
                sys.exit(1)
            if not data.get('description'):
                print("✗ Missing required field: description")
                sys.exit(1)

        except yaml.YAMLError as e:
            print(f"✗ Invalid YAML: {e}")
            sys.exit(1)
    else:
        print("✗ Frontmatter not properly formatted")
        sys.exit(1)
else:
    print("✗ SKILL.md must start with ---")
    sys.exit(1)
EOF
```

**Test bundled scripts**:

```bash
# Make scripts executable
chmod +x scripts/*.sh scripts/*.py

# Test each script individually
python3 scripts/scan_dependencies.py

# Check for syntax errors
bash -n scripts/*.sh
```

**Review the structure**:

```bash
# Verify directory structure
tree .

# Expected output:
# .
# ├── SKILL.md
# ├── scripts
# │   └── scan_dependencies.py
# ├── references
# │   └── owasp-top-10-detailed.md
# └── assets
#     └── report-template.md
```

### Step 8: Install Your Skill in Claude Code Web

Once your skill is ready, install it so Claude can use it.

**Method A: Local installation (for personal use)**

Copy your skill to the Claude skills directory:

```bash
# Copy to local skills directory
cp -r code-security-reviewer ~/.claude/skills/

# Verify installation
ls -la ~/.claude/skills/code-security-reviewer
```

Claude Code will automatically discover skills in `~/.claude/skills/`.

**Method B: Repository-based installation (for team sharing)**

For team-wide skills, create a git repository:

```bash
# Create a repository for your skills
mkdir my-company-skills
cd my-company-skills

# Create skill in subdirectory
mkdir code-security-reviewer
# ... add SKILL.md and resources ...

# Initialize git
git init
git add .
git commit -m "Add code security reviewer skill"

# Push to GitHub/GitLab
git remote add origin https://github.com/yourorg/company-skills.git
git push -u origin main
```

**Install from repository in Claude Code**:

```
/plugin marketplace add yourorg/company-skills
/plugin install code-security-reviewer@yourorg-company-skills
```

**Method C: Using anthropics/skills marketplace**

To contribute to the official skills repository:

1. Fork `https://github.com/anthropics/skills`
2. Add your skill folder to the appropriate category
3. Submit a pull request
4. Once merged, anyone can install via `/plugin install your-skill@anthropic-agent-skills`

### Step 9: Use Your Skill

Once installed, Claude will automatically load your skill when relevant.

**Automatic invocation** - Claude detects when to use the skill based on the description:

```
Review this authentication code for security vulnerabilities

[Claude will automatically load the code-security-reviewer skill]
```

**Explicit invocation** - You can explicitly reference the skill:

```
Use the code-security-reviewer skill to analyze this API endpoint
```

**Verify skill is loaded**:

When Claude uses a skill, it will typically mention it in the response:

```
I'll use the code-security-reviewer skill to perform a thorough security analysis...
```

### Step 10: Iterate and Improve

Skills benefit from real-world testing and refinement.

**Gather feedback**:
- Does Claude invoke the skill when expected?
- Are the instructions clear and specific enough?
- Do the outputs match your expectations?

**Common improvements**:

1. **Refine the description**: If Claude doesn't load the skill when expected, enhance the description with more keywords and use cases

```yaml
# Before
description: Reviews code for security issues

# After
description: Reviews code for security vulnerabilities using OWASP Top 10 standards. Use when analyzing authentication, authorization, API endpoints, data validation, cryptography, or any code handling user input or sensitive data.
```

2. **Add more examples**: Help Claude understand usage patterns

3. **Break down complex instructions**: Use numbered lists, clear sections, bullet points

4. **Add error handling to scripts**: Make bundled scripts robust

5. **Update references**: Keep documentation current

**Version your skills**:

```yaml
metadata:
  version: "1.1"
  changelog: "Added SQL injection detection patterns"
```

**Track changes in git**:

```bash
git add .
git commit -m "v1.1: Improve injection detection"
git tag v1.1
git push --tags
```

## Expected Results

After creating and installing a custom skill, you should observe:

### Automatic Discovery
1. Claude detects when the skill is relevant based on your request
2. The skill loads without explicit invocation
3. Claude follows the workflow defined in SKILL.md

### Consistent Execution
1. The same workflow executes every time, ensuring consistency
2. No need to re-explain procedures or paste long prompts
3. Team members get identical results using the same skill

### Enhanced Capabilities
1. Claude has access to specialized knowledge from references
2. Bundled scripts execute deterministic tasks reliably
3. Output follows standardized formats from templates

### Example Usage Session

**Without skill**:
```
You: Review this code for security issues
Claude: I'll review the code. Let me check for common vulnerabilities...
[Generic review, may miss specific OWASP categories]
```

**With code-security-reviewer skill**:
```
You: Review this authentication middleware
Claude: I'll use the code-security-reviewer skill to perform an OWASP-based security analysis...

### Executive Summary
The authentication middleware has 2 High severity and 1 Medium severity vulnerabilities...

### Detailed Findings

#### Finding 1: Broken Session Management
- **Severity**: High
- **OWASP Category**: A07 - Authentication Failures
- **Location**: middleware/auth.js:45
...
```

## Troubleshooting

### Common Issue 1
**Problem**: Skill doesn't load automatically when expected
**Solution**:
- Enhance the description field with more specific keywords and use cases
- Ensure the description clearly states when Claude should use the skill
- Add examples section in SKILL.md demonstrating typical usage
- Try explicitly referencing the skill: "Use the [skill-name] skill to..."
- Check that the skill is actually installed: `ls ~/.claude/skills/`
- Verify YAML frontmatter is properly formatted (no syntax errors)
- Restart Claude Code session to reload skill definitions

### Common Issue 2
**Problem**: "Invalid YAML frontmatter" error when installing skill
**Solution**:
- Validate YAML syntax at yamllint.com or using Python's yaml module
- Ensure there are exactly three dashes `---` before and after the frontmatter
- Check for proper indentation (use spaces, not tabs)
- Verify required fields `name` and `description` are present
- Ensure name contains only lowercase letters, numbers, and hyphens
- Check for special characters in description that need quoting
- Remove any leading/trailing whitespace from frontmatter

### Common Issue 3
**Problem**: Bundled scripts don't execute or throw permission errors
**Solution**:
- Make scripts executable: `chmod +x scripts/*.sh scripts/*.py`
- Add shebang line at top of scripts: `#!/bin/bash` or `#!/usr/bin/env python3`
- Verify script paths are relative, not absolute
- Check that required interpreters are available (python3, bash, node, etc.)
- Test scripts independently before bundling: `python3 scripts/myscript.py`
- Add error handling in scripts to fail gracefully
- Check Claude Code environment has necessary dependencies installed

### Common Issue 4
**Problem**: Skill works locally but not when team members install it
**Solution**:
- Ensure skill is committed to git repository, not just local
- Verify all team members installed from the correct repository/marketplace
- Check for hardcoded local paths in scripts or references
- Use environment variables or relative paths instead of absolute paths
- Don't rely on user-specific configurations or tools
- Test skill in a clean environment without your personal setup
- Document any prerequisites in SKILL.md (e.g., "requires gh CLI")
- Consider using `allowed-tools` to declare required Claude Code tools

### Common Issue 5
**Problem**: Skill description is too long or Claude truncates it
**Solution**:
- Keep description concise but comprehensive (2-3 sentences max)
- Put detailed usage instructions in the markdown body, not description
- Description should focus on WHEN to use the skill and WHAT it does
- Use the examples section for detailed use cases
- Avoid redundancy between description and markdown body
- Test with a shorter description and gradually add detail

### Common Issue 6
**Problem**: Cannot install skill from custom repository
**Solution**:
- Verify repository URL is correct and accessible
- Check repository is public or you have access credentials
- Ensure skill folder structure matches requirements (has SKILL.md)
- Use correct install syntax: `/plugin install skill-name@org-repo-name`
- Try adding marketplace first: `/plugin marketplace add org/repo-name`
- Verify repository contains a valid skills structure
- Check for typos in skill name or repository name

## Additional Tips

### Design Philosophy: Progressive Disclosure

Skills should employ "progressive disclosure" - Claude loads information only when needed:

1. **Metadata always loads** (name, description from frontmatter)
2. **SKILL.md body loads when triggered** (instructions, guidelines)
3. **Bundled resources load only when referenced** (scripts, references, assets)

This keeps Claude's context efficient while maintaining full functionality.

**Example**: A PDF skill includes a large PDF parsing library in scripts/, but Claude only loads it when actually parsing a PDF.

### Writing Effective Instructions

**Use imperative language**:
```markdown
# Good
1. Identify the vulnerability type
2. Assess the severity level
3. Provide specific remediation steps

# Avoid
You should try to identify vulnerabilities and maybe assess severity
```

**Be specific and actionable**:
```markdown
# Good
Check that all database queries use parameterized statements or prepared statements, never string concatenation

# Avoid
Look for SQL injection issues
```

**Provide structure**:
```markdown
## Review Process
1. Step one
2. Step two
3. Step three

## Output Format
### Section 1
[Content]

### Section 2
[Content]
```

### Skill Naming Best Practices

**Good names**:
- `code-security-reviewer` - Clear purpose, hyphenated
- `api-doc-generator` - Describes function
- `brand-guidelines` - Concise and specific
- `webapp-testing` - Domain and action

**Avoid**:
- `myskill` - Too generic
- `Code_Security_Reviewer` - Use hyphens, not underscores or camelCase
- `the-best-security-tool-ever` - Too verbose
- `skill-1` - Not descriptive

### Organizing Multiple Skills

**For personal use**:
```
~/.claude/skills/
├── code-security-reviewer/
├── api-doc-generator/
└── meeting-notes-formatter/
```

**For teams** (repository structure):
```
company-skills/
├── security/
│   ├── code-security-reviewer/
│   └── dependency-auditor/
├── documentation/
│   ├── api-doc-generator/
│   └── readme-creator/
└── communication/
    ├── meeting-notes-formatter/
    └── internal-comms/
```

### Security Considerations

**Never include secrets**:
```yaml
# BAD - Don't do this
description: Use API key sk-1234567890 to access the service

# GOOD
description: Accesses the service using provided API credentials
```

**Validate inputs in scripts**:
```python
# Good - validate before executing
import sys
import re

def validate_email(email):
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

if __name__ == "__main__":
    email = sys.argv[1] if len(sys.argv) > 1 else ""
    if not validate_email(email):
        print("Invalid email format", file=sys.stderr)
        sys.exit(1)
```

**Review third-party skills**: Before installing skills from others, review the SKILL.md and any scripts for malicious code.

### Performance Optimization

**Keep skills focused**: One skill = one clear purpose. Don't create mega-skills that do everything.

**Lazy-load heavy resources**: Put large files in `references/` so Claude only loads them when needed, not in the main SKILL.md body.

**Optimize bundled scripts**:
```python
# Good - fast, focused
def check_vulnerability(code):
    return analyze(code)

# Avoid - slow, does too much
def check_everything_and_fix_and_test(code):
    analyze()
    fix()
    test()
    deploy()  # Way too much for a single script
```

### Sharing and Distribution

**Document prerequisites**:
```markdown
## Prerequisites
- GitHub CLI (`gh`) must be installed and authenticated
- Repository must have a `SECURITY.md` file
- Claude Code environment must have network access enabled
```

**Provide examples**:
```markdown
## Example Usage

### Basic security review
"Review this authentication function for security issues"

### Comprehensive audit
"Perform a full OWASP security audit of the /api/auth directory"

### Specific vulnerability check
"Check if this code is vulnerable to SQL injection"
```

**Create a README**: If distributing via repository, add a README.md explaining:
- What skills are included
- How to install them
- Usage examples
- Contribution guidelines

### Combining Skills with Other Features

**Skills + Hooks**: Use SessionStart hook to load skill preferences
```bash
# .claude/sessionStart.sh
echo "Security review skills are available for code analysis"
```

**Skills + Slash Commands**: Create slash commands that invoke specific skills
```markdown
<!-- .claude/commands/security-review.md -->
Use the code-security-reviewer skill to analyze the current changes
```

**Skills + MCP**: Skills can use MCP servers for external tool access
```yaml
# SKILL.md
Use the database MCP server to query vulnerability data
```

## Related Articles
- KB-068: How to create your first custom slash command
- KB-061: How to create a SessionStart hook for repository initialization
- KB-075: How to install and configure an MCP server
- KB-072: How to create project-specific vs. personal slash commands
- KB-073: How to share commonly used slash commands with your team

## Sources
- Introducing Agent Skills - https://claude.com/blog/skills (Accessed: November 16, 2025)
- Skills Repository (Official) - https://github.com/anthropics/skills (Accessed: November 16, 2025)
- Agent Skills Specification - https://github.com/anthropics/skills/blob/main/agent_skills_spec.md (Accessed: November 16, 2025)
- Skills Explained: How Skills compares to prompts, Projects, MCP, and subagents - https://www.claude.com/blog/skills-explained (Accessed: November 16, 2025)
- How to Create Your First Claude Skill: Step-by-Step Tutorial - https://skywork.ai/blog/ai-agent/how-to-create-claude-skill-step-by-step-guide/ (Accessed: November 16, 2025)
- Awesome Claude Skills Repository - https://github.com/travisvn/awesome-claude-skills (Accessed: November 16, 2025)
- Claude Skills Are Awesome - https://simonwillison.net/2025/Oct/16/claude-skills/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
