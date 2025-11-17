# How to share commonly used slash commands with your team

**Article ID**: KB-073
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15 minutes

## Overview
Sharing custom slash commands across your team ensures consistent workflows, standardized automation, and collective productivity gains. By leveraging version control, plugin systems, and Claude Code Web's configuration hierarchy, you can distribute command libraries that enforce coding standards, streamline reviews, and maintain best practices across your entire development organization.

## Prerequisites
- Completed KB-068: Creating your first custom slash command
- Git repository access and basic Git knowledge
- Team members with Claude Code Web access
- Understanding of `.claude/` directory structure
- Administrative permissions to configure team settings (for enterprise features)

## Steps

### Step 1: Organize Commands for Team Distribution

Before sharing, organize your commands into a logical structure that makes sense for your team:

```bash
cd your-project-root
mkdir -p .claude/commands/workflows
mkdir -p .claude/commands/tools
mkdir -p .claude/commands/standards
```

Create a clear directory structure:

```
.claude/
└── commands/
    ├── workflows/           # Multi-step development workflows
    │   ├── feature-development.md
    │   └── bug-triage.md
    ├── tools/               # Single-purpose utilities
    │   ├── security-scan.md
    │   └── performance-audit.md
    └── standards/           # Code quality and standards
        ├── code-review.md
        └── style-check.md
```

This organization provides:
- **Namespacing**: Commands are invoked as `/workflows:feature-development`
- **Discoverability**: Team members can browse commands by category
- **Maintainability**: Related commands are grouped logically

### Step 2: Add Documentation to Commands

Make commands self-documenting by adding metadata using frontmatter and clear descriptions:

Create `.claude/commands/workflows/feature-development.md`:

```markdown
---
description: "Orchestrates complete feature development from design to PR"
argument-hint: "feature description"
allowed-tools: ["read", "write", "bash", "edit"]
---

# Feature Development Workflow

This command guides you through implementing a new feature following our team's best practices.

For the feature: $ARGUMENTS

Please follow these steps:

1. **Design Phase**: Review existing architecture and propose implementation approach
2. **Implementation**: Write clean, well-tested code following our style guide
3. **Testing**: Create unit and integration tests with >80% coverage
4. **Documentation**: Update API docs and add inline comments
5. **Review**: Prepare PR with comprehensive description and test plan

Ensure all changes follow our coding standards defined in CONTRIBUTING.md.
```

The frontmatter provides:
- `description`: Shows up in command listings
- `argument-hint`: Helps users understand expected arguments
- `allowed-tools`: Restricts which tools Claude can use (optional)

### Step 3: Version Control Your Commands

Commit the `.claude/commands/` directory to your repository so it's shared automatically:

```bash
git add .claude/commands/
git commit -m "Add team slash command library

- Added workflow commands for feature development and bug triage
- Added tool commands for security and performance audits
- Added standards commands for code review and style checks
- All commands include documentation and argument hints"
git push origin main
```

**Important**: Verify `.claude/` is NOT in your `.gitignore`:

```bash
# Check if .claude is ignored
git check-ignore .claude/commands/

# If it returns a path, remove .claude from .gitignore
```

When team members clone or pull the repository, they automatically receive all project-scoped commands.

### Step 4: Create a Team Command Library README

Add documentation explaining your command library to help team members discover and use commands:

Create `.claude/commands/README.md`:

```markdown
# Team Slash Command Library

## Available Commands

### Workflows (Multi-step Operations)
- `/workflows:feature-development [description]` - Complete feature implementation workflow
- `/workflows:bug-triage [issue-number]` - Systematic bug investigation and fix

### Tools (Specialized Utilities)
- `/tools:security-scan` - Run comprehensive security vulnerability scan
- `/tools:performance-audit` - Analyze and optimize performance bottlenecks

### Standards (Quality Enforcement)
- `/standards:code-review` - Perform thorough code review against team standards
- `/standards:style-check` - Verify code follows style guide

## Usage Examples

```
/workflows:feature-development Add OAuth2 authentication
/workflows:bug-triage 1234
/tools:security-scan
```

## Contributing New Commands

1. Create command in appropriate directory
2. Add frontmatter with description and argument-hint
3. Include usage examples in command file
4. Submit PR for team review
5. Update this README
```

This serves as a command catalog for your team.

### Step 5: Configure Team Settings with .claude/settings.json

For advanced team configurations, create `.claude/settings.json` to specify shared preferences:

```json
{
  "commands": {
    "defaults": {
      "model": "claude-sonnet-4-5-20250929",
      "maxTokens": 8000
    },
    "namespaces": {
      "workflows": {
        "description": "Multi-agent orchestration for complex operations"
      },
      "tools": {
        "description": "Single-purpose development utilities"
      }
    }
  },
  "hooks": {
    "SessionStart": [".claude/hooks/setup-environment.sh"],
    "PostToolUse": [".claude/hooks/format-code.sh"]
  }
}
```

Commit this file to version control:

```bash
git add .claude/settings.json
git commit -m "Add team Claude Code configuration"
git push
```

Team members who trust the repository will automatically use these settings.

### Step 6: Share Commands via Plugin System

For distributing commands across multiple repositories or organizations, use Claude Code's plugin system.

**Create a Plugin Repository:**

```bash
# Create a new repository for your team commands
mkdir my-team-claude-commands
cd my-team-claude-commands

# Create plugin structure
mkdir -p .claude-plugin/commands
mkdir -p .claude-plugin/agents
```

**Create Plugin Manifest** at `.claude-plugin/plugin.json`:

```json
{
  "name": "my-team-commands",
  "version": "1.0.0",
  "description": "Standard slash commands for our engineering team",
  "author": "Your Team Name",
  "commands": [
    {
      "name": "team-review",
      "path": "commands/team-review.md",
      "description": "Perform code review using team standards"
    },
    {
      "name": "deploy-check",
      "path": "commands/deploy-check.md",
      "description": "Pre-deployment verification checklist"
    }
  ],
  "homepage": "https://github.com/your-org/my-team-claude-commands",
  "license": "MIT"
}
```

**Add Your Commands:**

Create `.claude-plugin/commands/team-review.md` and other command files.

**Publish the Plugin:**

```bash
git add .
git commit -m "Initial plugin release"
git push origin main
git tag v1.0.0
git push origin v1.0.0
```

**Team Installation:**

Team members can install via:

```
/plugin install my-team-commands from https://github.com/your-org/my-team-claude-commands
```

Or add to their local config for automatic installation.

### Step 7: Create a Plugin Marketplace for Your Organization

For enterprises managing multiple plugin collections, set up a marketplace:

Create a repository with `.claude-plugin/marketplace.json`:

```json
{
  "name": "Acme Corp Claude Code Marketplace",
  "description": "Approved plugins for Acme Corp engineering teams",
  "version": "1.0.0",
  "plugins": [
    {
      "name": "acme-code-standards",
      "repository": "https://github.com/acme-corp/claude-code-standards",
      "description": "Code review and quality standards enforcement",
      "version": "2.1.0",
      "category": "quality"
    },
    {
      "name": "acme-security-tools",
      "repository": "https://github.com/acme-corp/claude-security-tools",
      "description": "Security scanning and vulnerability detection",
      "version": "1.5.0",
      "category": "security"
    },
    {
      "name": "acme-deployment",
      "repository": "https://github.com/acme-corp/claude-deployment-workflows",
      "description": "Deployment automation and verification",
      "version": "3.0.1",
      "category": "deployment"
    }
  ],
  "maintainers": [
    {
      "name": "Platform Engineering",
      "email": "platform-eng@acme-corp.com"
    }
  ]
}
```

**Team members add the marketplace:**

```
/plugin marketplace add acme-corp/claude-marketplace
```

Then browse and install plugins:

```
/plugin
# Select "Browse Plugins"
# Select marketplace
# Install desired plugins
```

### Step 8: Enforce Team Standards with Managed Settings

For enterprise deployments, use managed settings to enforce command usage:

Create `/etc/claude-code/managed-settings.json` (requires admin access):

```json
{
  "plugins": {
    "required": [
      {
        "name": "acme-code-standards",
        "marketplace": "acme-corp/claude-marketplace",
        "version": ">=2.0.0"
      }
    ],
    "marketplaces": [
      {
        "url": "https://github.com/acme-corp/claude-marketplace",
        "enabled": true
      }
    ]
  },
  "commands": {
    "enforceProjectCommands": true
  }
}
```

This ensures all team members have required plugins and cannot override critical settings.

## Expected Results

After completing these steps, you should achieve:

1. **Automatic Command Distribution**: Team members pulling repository changes automatically receive new commands
2. **Command Discovery**: `/help` shows all available team commands with descriptions
3. **Consistent Workflows**: All team members use identical command definitions
4. **Namespaced Organization**: Commands accessible via `/namespace:command-name`
5. **Plugin Installation**: Team commands installable across multiple projects
6. **Marketplace Access**: Centralized plugin distribution for enterprise teams
7. **Version Control**: Full history of command changes and updates

When a team member types `/workflows:feature-development Add user authentication`, they execute your team's standardized workflow with consistent quality checks and best practices.

## Troubleshooting

### Team Members Don't See Shared Commands

**Problem**: After pulling changes, team members don't see project commands
**Solution**:
- Verify `.claude/commands/` is committed and pushed
- Check that `.claude/` is not in `.gitignore`
- Ask team members to pull latest changes: `git pull origin main`
- Refresh Claude Code Web or start a new conversation
- Verify team members are working in the correct repository directory

### Command Namespace Conflicts

**Problem**: Personal commands conflict with team command names
**Solution**:
- Use explicit namespaces: `/project:command-name` vs `/user:command-name`
- Rename conflicting personal commands
- Organize team commands in subdirectories for automatic namespacing
- Document which commands are team-standard vs personal preference

### Plugin Installation Fails

**Problem**: `/plugin install` returns an error
**Solution**:
- Verify plugin repository URL is correct and accessible
- Check that `.claude-plugin/plugin.json` is valid JSON
- Ensure all command paths in plugin.json exist
- Verify Git tag exists if installing specific version
- Check network connectivity and repository permissions

### Commands Not Updating After Changes

**Problem**: Modified commands still use old version
**Solution**:
- Ensure changes are committed and pushed to repository
- Team members must pull latest changes
- Try refreshing Claude Code Web session
- Check git status to verify changes are tracked
- For plugins, increment version number and create new tag

### Marketplace Not Loading Plugins

**Problem**: Marketplace added but plugins don't appear
**Solution**:
- Verify `marketplace.json` is valid JSON format
- Check that all plugin repositories are accessible
- Ensure marketplace URL is correct
- Refresh plugin list: `/plugin marketplace refresh`
- Verify network access to GitHub/repository hosting

## Additional Tips

- **Start Small**: Begin with 3-5 high-impact commands before expanding
- **Gather Feedback**: Survey team to identify most-needed automation
- **Version Your Plugins**: Use semantic versioning (MAJOR.MINOR.PATCH) for plugin releases
- **Document Conventions**: Create team guidelines for command naming and organization
- **Review Regularly**: Monthly audit of command usage and effectiveness
- **Encourage Contributions**: Make it easy for team members to propose new commands
- **Test Before Sharing**: Validate commands work as expected before pushing
- **Use Templates**: Create command templates for consistent structure
- **Monitor Adoption**: Track which commands are most/least used
- **Maintain Backwards Compatibility**: Avoid breaking changes to existing command interfaces
- **Security Review**: Audit commands for sensitive data exposure or unsafe operations
- **Cross-Platform Testing**: Test commands work on different operating systems if applicable

## Related Articles
- KB-068: How to create your first custom slash command in .claude/commands/
- KB-069: How to write slash commands using markdown files
- KB-070: How to use the $ARGUMENTS keyword in slash commands
- KB-067: How to share hooks across your development team
- KB-061: How to create a SessionStart hook for repository initialization

## Sources
- Slash commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- Plugins - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/plugins (Accessed: November 16, 2025)
- Customize Claude Code with plugins - Anthropic Blog - https://www.anthropic.com/news/claude-code-plugins (Accessed: November 16, 2025)
- awesome-claude-code - GitHub Repository - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)
- Production-ready Commands Collection - GitHub Repository - https://github.com/wshobson/commands (Accessed: November 16, 2025)
- Using Claude Code with your Team or Enterprise plan - Claude Help Center - https://support.claude.com/en/articles/11845131-using-claude-code-with-your-team-or-enterprise-plan (Accessed: November 16, 2025)
- How Anthropic teams use Claude Code - Anthropic Blog - https://www.anthropic.com/news/how-anthropic-teams-use-claude-code (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
