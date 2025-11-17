# How to share plugins with your team or community

**Article ID**: KB-085
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview

Claude Code plugins are a powerful way to package and distribute custom workflows, tools, and automations. Plugins can bundle slash commands, subagents, MCP servers, and hooks into a single installable package that can be shared with your team or the broader developer community. This guide covers how to create plugin marketplaces, configure repository-level plugin sharing for teams, and distribute your plugins to the community.

## Prerequisites

- Claude Code Pro or Max account with access to the plugin system (public beta as of October 2025)
- Basic understanding of Git and repository management
- Familiarity with Claude Code plugins and their components
- Related articles:
  - KB-068: Create your first custom slash command
  - KB-061: Create SessionStart hook for repository initialization
  - KB-075: Install and configure an MCP server

## Steps

### Step 1: Understand Plugin Sharing Methods

Claude Code offers three primary ways to share plugins:

**1. Repository-Level Configuration** (Team sharing)
- Configure plugins in `.claude/settings.json` within your project repository
- When team members trust the repository, plugins install automatically
- Best for team standardization and onboarding

**2. Plugin Marketplaces** (Organizational/Community sharing)
- Host a git repository with a `.claude-plugin/marketplace.json` file
- Users add your marketplace with `/plugin marketplace add`
- Best for distributing multiple plugins or building a plugin ecosystem

**3. Direct Plugin Installation** (Individual sharing)
- Share individual plugin directories or repositories
- Users install with `/plugin install` command
- Best for quick sharing of single plugins

### Step 2: Create a Plugin Package

Before sharing, organize your plugin components in a standard structure:

```
my-awesome-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── deploy.md
│   └── test.md
├── agents/
│   └── code-reviewer.md
├── hooks/
│   └── SessionStart/
│       └── init.sh
└── servers/
    └── custom-mcp/
        └── server.js
```

Create a `plugin.json` manifest file:

```json
{
  "name": "my-awesome-plugin",
  "version": "1.0.0",
  "description": "Automated deployment and testing workflows",
  "author": {
    "name": "Your Name",
    "email": "you@example.com"
  },
  "homepage": "https://github.com/yourusername/my-awesome-plugin",
  "repository": "https://github.com/yourusername/my-awesome-plugin",
  "license": "MIT",
  "keywords": ["deployment", "testing", "automation"],
  "category": "productivity",
  "commands": [
    "./commands/"
  ],
  "agents": [
    "./agents/code-reviewer.md"
  ],
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "${CLAUDE_PLUGIN_ROOT}/hooks/SessionStart/init.sh"
      }
    ]
  },
  "mcpServers": {
    "custom-tools": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/custom-mcp/server.js"]
    }
  }
}
```

**Key fields:**
- `name`: Unique identifier for your plugin
- `version`: Semantic versioning (major.minor.patch)
- `commands`, `agents`, `hooks`, `mcpServers`: Paths to plugin components
- `${CLAUDE_PLUGIN_ROOT}`: Environment variable that resolves to plugin installation directory

### Step 3: Share with Your Team (Repository Configuration)

For team-wide plugin distribution, configure plugins at the repository level:

Create or edit `.claude/settings.json` in your project repository:

```json
{
  "extraKnownMarketplaces": {
    "company-tools": {
      "source": {
        "source": "github",
        "repo": "your-company/claude-plugins"
      }
    },
    "project-specific": {
      "source": {
        "source": "git",
        "url": "https://git.company.com/project-plugins.git"
      }
    }
  },
  "enabledPlugins": [
    "company-tools/deployment-workflow",
    "company-tools/testing-suite",
    "project-specific/local-dev-tools"
  ]
}
```

**Benefits:**
- New team members get the correct plugins automatically when they trust the repository
- Eliminates "works on my machine" configuration drift
- Reduces setup time from hours to minutes
- Ensures consistent tooling across the entire team

Commit this file to your repository:

```bash
git add .claude/settings.json
git commit -m "Add Claude Code plugin configuration for team"
git push
```

### Step 4: Create a Plugin Marketplace

To distribute multiple plugins or build a community marketplace, create a marketplace repository:

**Structure:**

```
my-marketplace/
├── .claude-plugin/
│   └── marketplace.json
├── plugin-1/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   └── commands/
├── plugin-2/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   └── agents/
└── README.md
```

**Create `marketplace.json`:**

```json
{
  "name": "enterprise-tools",
  "version": "1.0.0",
  "description": "Enterprise development tools and workflows",
  "author": "Enterprise Dev Team",
  "homepage": "https://github.com/company/enterprise-tools",
  "plugins": {
    "deployment-workflow": {
      "name": "deployment-workflow",
      "version": "2.1.0",
      "description": "Automated deployment workflows",
      "source": {
        "source": "git",
        "url": "https://github.com/company/enterprise-tools.git",
        "subdir": "plugin-1"
      },
      "author": {
        "name": "DevOps Team",
        "email": "devops@company.com"
      },
      "keywords": ["deployment", "ci-cd"],
      "category": "productivity",
      "commands": ["./commands/"],
      "strict": false
    },
    "security-scanner": {
      "name": "security-scanner",
      "version": "1.5.0",
      "description": "Security scanning and compliance checks",
      "source": {
        "source": "git",
        "url": "https://github.com/company/enterprise-tools.git",
        "subdir": "plugin-2"
      },
      "agents": ["./agents/security-reviewer.md"],
      "keywords": ["security", "compliance"],
      "category": "security"
    }
  }
}
```

**Important marketplace.json settings:**
- `strict: false`: Marketplace entry serves as complete plugin manifest if no plugin.json exists
- `strict: true` (default): Marketplace fields supplement the plugin's own plugin.json
- `source.subdir`: Specifies subdirectory within repository containing the plugin

### Step 5: Host Your Marketplace

Push your marketplace to a git hosting service:

**GitHub:**
```bash
git init
git add .
git commit -m "Initial marketplace setup"
git remote add origin https://github.com/your-org/claude-plugins.git
git push -u origin main
```

**GitLab or other Git hosting:**
```bash
git remote add origin https://gitlab.com/your-org/claude-plugins.git
git push -u origin main
```

**Any marketplace works with:**
- GitHub repositories
- GitLab repositories
- Bitbucket repositories
- Any git hosting service with HTTPS access
- Direct URLs to marketplace.json files

### Step 6: Enable Users to Install Your Plugins

**For GitHub marketplaces:**

Users add your marketplace:
```
/plugin marketplace add your-org/claude-plugins
```

Then install specific plugins:
```
/plugin install deployment-workflow
```

**For other git hosting:**

Users add with full URL:
```
/plugin marketplace add git https://git.company.com/plugins.git
```

**Marketplace management commands:**
- `/plugin marketplace add <repo>` - Add a marketplace
- `/plugin marketplace update <name>` - Refresh marketplace listings
- `/plugin marketplace remove <name>` - Remove a marketplace
- `/plugin install <plugin-name>` - Install from any added marketplace
- `/plugin enable <plugin-name>` - Enable an installed plugin
- `/plugin disable <plugin-name>` - Disable a plugin without uninstalling
- `/plugin list` - View all installed plugins

### Step 7: Document Your Plugins

Create comprehensive documentation for plugin users:

**README.md template:**

```markdown
# Enterprise Tools Plugin Marketplace

Official Claude Code plugins for enterprise development workflows.

## Available Plugins

### Deployment Workflow
Automated deployment pipelines and environment management.

**Installation:**
```
/plugin marketplace add company/enterprise-tools
/plugin install deployment-workflow
```

**Commands:**
- `/deploy <environment>` - Deploy to specified environment
- `/rollback <version>` - Rollback to previous version

### Security Scanner
Automated security scanning and compliance checks.

**Installation:**
```
/plugin install security-scanner
```

**Agents:**
- `security-reviewer` - Reviews code for security vulnerabilities

## Requirements
- Claude Code Pro or Max account
- Access to company VPN (for deployment commands)
- Configured API keys in `.env` file

## Support
Contact devops@company.com for issues or questions.
```

### Step 8: Version and Maintain Your Plugins

Follow semantic versioning for plugin updates:

**Version numbering:**
- **Major (1.0.0 → 2.0.0)**: Breaking changes, incompatible updates
- **Minor (1.0.0 → 1.1.0)**: New features, backward compatible
- **Patch (1.0.0 → 1.0.1)**: Bug fixes, backward compatible

**Best practices:**
- Pin plugin versions in team configurations to prevent unexpected changes
- Test plugin updates before deploying to production teams
- Maintain a CHANGELOG.md documenting all version changes
- Use git tags for version releases
- Announce breaking changes to users in advance

**Update workflow:**

```bash
# Update plugin version
vim .claude-plugin/plugin.json  # Increment version number
git add .
git commit -m "Release v1.1.0: Add new deployment targets"
git tag v1.1.0
git push origin main --tags
```

Users update with:
```
/plugin marketplace update company-tools
/plugin upgrade deployment-workflow
```

## Expected Results

After completing these steps:

**For Team Sharing:**
- Team members who trust your repository automatically receive configured plugins
- Consistent development environment across all team members
- New developers onboard with complete tooling in minutes
- Plugin versions remain synchronized across the team

**For Community/Marketplace Sharing:**
- Your marketplace appears in users' available marketplace lists
- Developers can discover and install your plugins with simple commands
- Plugin updates propagate to all users via marketplace refresh
- Community members can contribute feedback and improvements

**Verification:**
1. Team member clones repository and trusts it in Claude Code
2. Configured marketplaces and plugins install automatically
3. Custom slash commands appear in `/help` output
4. Hooks execute at appropriate lifecycle events
5. MCP servers connect and provide tools successfully

## Troubleshooting

### Issue: Marketplace Add Hangs Indefinitely

**Problem**: `/plugin marketplace add` command hangs with no feedback
**Solution**:
- Verify the repository URL is accessible and public (or you have authentication configured)
- Check that `.claude-plugin/marketplace.json` exists in repository root
- Try adding with explicit git URL: `/plugin marketplace add git https://github.com/user/repo.git`
- Check Claude Code logs for connection errors

### Issue: Plugins Don't Auto-Install for Team Members

**Problem**: Team members trust repository but plugins don't install automatically
**Solution**:
- Verify `.claude/settings.json` is committed to the repository
- Ensure marketplace sources are accessible to all team members
- Check that plugin names in `enabledPlugins` match marketplace plugin IDs exactly
- Have users manually trigger: `/plugin marketplace update` then `/plugin install <name>`

### Issue: Plugin Components Not Found

**Problem**: Commands, hooks, or agents referenced in plugin.json aren't found
**Solution**:
- Use `${CLAUDE_PLUGIN_ROOT}` environment variable for all paths in plugin.json
- Verify relative paths are correct (./commands/ not /commands/)
- Check file permissions on plugin scripts and executables
- Ensure directory structure matches paths specified in manifest

### Issue: Version Conflicts

**Problem**: Different team members have different plugin versions installed
**Solution**:
- Pin specific versions in repository `.claude/settings.json`
- Document plugin version requirements in project README
- Use marketplace update + plugin upgrade commands to synchronize
- Consider implementing CI checks to validate plugin versions

### Issue: MCP Servers in Plugins Fail to Connect

**Problem**: Plugin-bundled MCP servers don't start or connect
**Solution**:
- Verify MCP server command and args use `${CLAUDE_PLUGIN_ROOT}` for paths
- Check that MCP server dependencies are included in plugin package
- Test MCP server independently before bundling in plugin
- Review MCP server logs for connection errors
- See KB-080: Troubleshoot MCP server connection issues

## Additional Tips

**Security Best Practices:**
- Review all plugin code before installing from untrusted sources
- Use private repositories for proprietary team plugins
- Implement code review processes for plugin updates
- Avoid hardcoding sensitive credentials in plugins (use environment variables)
- Document required environment variables and API keys

**Plugin Development Tips:**
- Start with a single component (e.g., one slash command) and expand gradually
- Test plugins in isolated repositories before team deployment
- Use descriptive plugin and command names to avoid namespace conflicts
- Include inline documentation in slash command markdown files
- Provide example usage and common workflows in plugin documentation

**Team Adoption Strategies:**
- Start with a small pilot team to validate plugin utility
- Gather feedback and iterate before organization-wide rollout
- Create video tutorials or documentation for complex plugins
- Designate plugin "champions" on each team for support
- Track usage metrics to understand which plugins provide most value

**Marketplace Curation:**
- Group related plugins in themed marketplaces (DevOps, Frontend, Testing, etc.)
- Maintain quality standards for plugins in organizational marketplaces
- Implement testing requirements before publishing plugin updates
- Create plugin templates to standardize structure across your organization
- Consider automated testing in CI/CD for plugin validation

**Performance Considerations:**
- Minimize plugin size by excluding unnecessary files (use .gitignore)
- Lazy-load heavy dependencies in MCP servers when possible
- Test plugin installation time, especially for team onboarding
- Consider splitting large plugin packages into multiple focused plugins

## Related Articles

- KB-068: Create your first custom slash command
- KB-061: Create SessionStart hook for repository initialization
- KB-067: Share hooks across your development team
- KB-073: Share commonly used slash commands with your team
- KB-075: Install and configure an MCP server
- KB-076: Connect Claude Code to external MCP servers
- KB-081: Create custom MCP server integrations

## Sources

- Customize Claude Code with plugins - https://www.claude.com/blog/claude-code-plugins (Accessed: November 16, 2025)
- Claude Code Plugins Documentation - https://docs.claude.com/en/docs/claude-code/plugins (Accessed: November 16, 2025)
- Plugin marketplaces Documentation - https://docs.claude.com/en/docs/claude-code/plugin-marketplaces (Accessed: November 16, 2025)
- Building Claude Code Plugins: The Complete Developer's Guide - https://levelup.gitconnected.com/building-claude-code-plugins-the-complete-developers-guide-b642c951cade (Accessed: November 16, 2025)
- Team Setup Guide: Standardizing Claude Code Plugin Usage - https://skywork.ai/blog/claude-code-plugin-standardization-team-guide/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
