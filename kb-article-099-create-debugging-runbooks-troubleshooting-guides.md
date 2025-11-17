# How to create debugging runbooks and troubleshooting guides

**Article ID**: KB-099
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 30-45 minutes

## Overview
Debugging runbooks and troubleshooting guides provide systematic, documented approaches to diagnosing and resolving common issues in your projects. This advanced guide demonstrates how to create comprehensive runbooks using Claude Code Web's built-in features (CLAUDE.md, slash commands, and hooks), leverage Claude itself to generate and maintain troubleshooting documentation, and build team-accessible resources that reduce debugging time and improve consistency across your development workflow.

## Prerequisites
- Active Claude Code Web session with repository access
- Understanding of your project's architecture and common failure modes
- Familiarity with CLAUDE.md configuration files
- Knowledge of custom slash commands and hooks
- Recommended: KB-068 (Create custom slash commands), KB-069 (Write slash commands as Markdown)
- Recommended: KB-066 (Debug hook failures), KB-051 (Fix specific bugs)

## Steps

### Step 1: Define Runbook Scope and Structure

Before creating documentation, identify what scenarios require documented procedures.

**Common runbook categories:**
- **Environment Setup**: Dependencies, configuration, initial setup procedures
- **Build & Deployment**: Build failures, deployment rollbacks, environment-specific issues
- **Runtime Errors**: Common exceptions, performance degradation, resource exhaustion
- **Integration Issues**: API failures, database connection problems, third-party service outages
- **Development Workflow**: Git conflicts, test failures, linting errors

**Create a runbook template:**

```markdown
# [Issue Category] Runbook

## Symptoms
- Observable behavior indicating this issue
- Error messages or codes associated with it
- When this typically occurs

## Diagnostic Steps
1. Check [first indicator]
2. Verify [second indicator]
3. Examine [logs/metrics/state]

## Root Causes
- **Cause 1**: [Description and frequency]
- **Cause 2**: [Description and frequency]

## Resolution Steps

### For Cause 1
1. [Specific action]
2. [Verification step]
3. [Confirmation]

### For Cause 2
1. [Alternative specific action]
2. [Verification step]

## Prevention
- How to avoid this issue in the future
- Monitoring or alerts to add
- Code patterns to adopt/avoid

## Related Issues
- Links to similar problems
- Cross-references to other runbooks
```

**Determine storage location:**
- **Project-specific**: `.claude/runbooks/` or `docs/debugging/`
- **Team-wide**: Centralized wiki or documentation repository
- **Quick reference**: CLAUDE.md for frequently-accessed procedures

### Step 2: Use Claude to Generate Initial Runbooks from Documentation

Leverage Claude Code Web to synthesize existing documentation into structured runbooks.

**Method 1: Consolidate from multiple sources**

This approach mirrors how Anthropic's Security Engineering team creates runbooks (as reported in their official blog):

```
Analyze the following documentation sources and create a comprehensive debugging runbook:

1. Read README.md for project overview
2. Read docs/architecture.md for system design
3. Search all files for "TODO" and "FIXME" comments
4. Review recent git commit messages for bug fixes (git log --grep="fix" -20)
5. Examine package.json/requirements.txt for dependencies

Generate a runbook covering:
- Common setup issues
- Known dependency conflicts
- Frequent runtime errors
- Environment-specific problems

Use the template structure from .claude/runbooks/template.md
```

**Method 2: Extract from error logs**

```
Analyze the log file at logs/production-errors.log and create a troubleshooting guide.

For each distinct error pattern:
1. Identify the error signature
2. Extract relevant stack traces
3. Determine frequency and impact
4. Propose diagnostic steps
5. Suggest resolution procedures

Format as a structured runbook and save to .claude/runbooks/production-errors.md
```

**Method 3: Generate from issue tracker**

```
Use gh to fetch the 50 most recent closed issues labeled "bug":

gh issue list --state closed --label bug --limit 50 --json number,title,body

Analyze these issues and create a troubleshooting guide that:
- Groups similar issues into categories
- Extracts resolution patterns
- Identifies recurring root causes
- Documents diagnostic procedures that worked

Save as .claude/runbooks/common-issues.md
```

### Step 3: Document Environment-Specific Debugging Procedures in CLAUDE.md

CLAUDE.md provides context that Claude automatically reads in every session, making it ideal for frequently-referenced debugging information.

**Create a debugging section in CLAUDE.md:**

```markdown
# Project Context

## Common Debugging Scenarios

### Database Connection Failures

**Symptoms**: `ECONNREFUSED` errors when starting the application

**Quick Diagnosis**:
```bash
# Check if PostgreSQL is running
docker ps | grep postgres

# Check connection settings
cat .env | grep DATABASE_URL
```

**Resolution**:
```bash
# Restart database container
docker-compose up -d postgres

# Verify connection
npm run db:ping
```

### Build Failures After Dependency Updates

**First Steps**:
1. Clear node_modules: `rm -rf node_modules && npm install`
2. Clear build cache: `npm run clean`
3. Check for peer dependency conflicts: `npm ls`

**Common Causes**:
- TypeScript version mismatch (check package.json vs. node_modules)
- Conflicting ESLint plugins (run `npm ls eslint`)

### Test Suite Failures in CI but Passes Locally

**Investigation**:
```bash
# Run tests with same environment as CI
NODE_ENV=test CI=true npm test

# Check for timezone-dependent tests
TZ=UTC npm test

# Verify database seed state
npm run db:reset && npm test
```

## Debugging Commands

When debugging issues, use these commands:

**View error logs**:
```bash
tail -f logs/error.log
```

**Check system resources**:
```bash
docker stats
```

**Database query debugging**:
```bash
npm run db:console
# Then: EXPLAIN ANALYZE <your-query>
```

**Network debugging**:
```bash
curl -v http://localhost:3000/api/health
```
```

**Benefits of CLAUDE.md documentation:**
- Automatically available in every session
- No need to search for debugging info
- Claude can reference these procedures when you report issues
- Team members get the same context immediately

### Step 4: Create Automated Troubleshooting Slash Commands

Transform common debugging workflows into reusable slash commands for rapid execution.

**Example: Database health check command** (`.claude/commands/db-health.md`):

```markdown
---
description: "Run comprehensive database health checks and diagnostics"
argument-hint: "[specific-check]"
---

# Database Health Check

Run the following diagnostic sequence and report findings:

## 1. Connection Status
```bash
docker ps --filter name=postgres --format "{{.Status}}"
```

## 2. Database Size and Table Counts
```bash
docker exec postgres psql -U app_user -d app_db -c "
  SELECT
    pg_size_pretty(pg_database_size('app_db')) as db_size,
    (SELECT count(*) FROM pg_tables WHERE schemaname = 'public') as table_count;
"
```

## 3. Active Connections
```bash
docker exec postgres psql -U app_user -d app_db -c "
  SELECT count(*), state FROM pg_stat_activity
  GROUP BY state;
"
```

## 4. Recent Errors
```bash
tail -n 50 logs/database.log | grep -i error
```

## 5. Slow Queries (if logged)
```bash
tail -n 100 logs/slow-queries.log
```

**Report Summary**:
- Overall health status (healthy/degraded/failed)
- Any issues detected
- Recommended actions if problems found
```

**Example: Dependency conflict resolver** (`.claude/commands/fix-deps.md`):

```markdown
---
description: "Diagnose and resolve dependency conflicts"
---

# Dependency Conflict Resolution

Execute this systematic troubleshooting workflow:

## Step 1: Identify Conflicts
```bash
npm ls 2>&1 | grep -E "UNMET|invalid|extraneous"
```

## Step 2: Analyze Package Tree
Show the full dependency tree for any conflicting packages identified in Step 1.

## Step 3: Check for Peer Dependency Issues
```bash
npm install --dry-run 2>&1 | grep -i peer
```

## Step 4: Propose Resolution
Based on the conflicts found:
1. Identify which packages are causing conflicts
2. Determine if we need to update, downgrade, or replace packages
3. Suggest exact commands to resolve (e.g., `npm install package@specific-version`)

## Step 5: Verification Plan
Outline which tests to run after applying fixes to ensure nothing breaks.

**Do not make changes automatically**. Present the analysis and wait for approval.
```

**Example: Deployment troubleshooting** (`.claude/commands/troubleshoot-deploy.md`):

```markdown
---
description: "Diagnose deployment failures with systematic checks"
argument-hint: "<environment>"
---

# Deployment Troubleshooting: $1

Run comprehensive deployment diagnostics for environment: $1

## Pre-deployment Checks

### 1. Build Status
```bash
npm run build
echo "Exit code: $?"
```

### 2. Test Suite Status
```bash
npm test
echo "Exit code: $?"
```

### 3. Environment Configuration
```bash
# Check if environment file exists
ls -la .env.$1

# Verify required variables (without showing secrets)
grep -E "^[A-Z_]+=" .env.$1 | cut -d= -f1
```

## Deployment History

### 4. Recent Deployments
```bash
gh api /repos/:owner/:repo/deployments \
  --jq ".[] | select(.environment==\"$1\") | {created_at, sha, state}" \
  | head -5
```

### 5. Last Successful Deployment
```bash
git log --grep="deploy" --grep="$1" -1 --oneline
```

## Current State Analysis

### 6. Service Health (if deployed)
```bash
curl -s https://$1.example.com/health | jq .
```

### 7. Container/Service Logs (last 50 lines)
Provide command to fetch logs based on infrastructure:
- Heroku: `heroku logs --tail --app app-$1`
- AWS: `aws logs tail /aws/elasticbeanstalk/app-$1/var/log/web.log`
- Docker: `docker logs app-$1 --tail 50`

## Diagnosis

Analyze all output above and:
1. Identify the specific failure point
2. Determine the root cause
3. Recommend resolution steps
4. Suggest preventive measures

**Output a deployment troubleshooting report** with clear next actions.
```

### Step 5: Implement Diagnostic Hooks for Automatic Issue Detection

Use hooks to automatically run diagnostics and provide troubleshooting context when issues occur.

**SessionStart hook for environment validation** (`.claude/hooks/SessionStart.json`):

```json
[
  {
    "command": "bash -c 'cat <<EOF\n=== Environment Diagnostic Report ===\n\nNode version: $(node --version)\nnpm version: $(npm --version)\n\nInstalled dependencies: $([ -d node_modules ] && echo \"✓ Present\" || echo \"✗ Missing - run npm install\")\n\nEnvironment file: $([ -f .env ] && echo \"✓ Present\" || echo \"✗ Missing - see .env.example\")\n\nDatabase status: $(docker ps --filter name=postgres --format \"{{.Status}}\" || echo \"Not running\")\n\nRecent errors in logs: $([ -f logs/error.log ] && tail -5 logs/error.log || echo \"No error log found\")\n\n=== End Diagnostic Report ===\nEOF'"
  }
]
```

This hook runs on every session start and provides Claude with diagnostic information automatically, making troubleshooting faster.

**PostToolUse hook for test failure capture** (`.claude/hooks/PostToolUse.json`):

```json
[
  {
    "pattern": "**/*test*.{js,ts,py}",
    "command": "bash -c 'echo \"Test file modified: ${FILE_PATH}. Run tests to verify: npm test $(dirname ${FILE_PATH})\" >&2'"
  }
]
```

When test files are modified, Claude receives a reminder to run tests, helping catch issues early.

### Step 6: Build a Troubleshooting Decision Tree

Create structured decision trees for complex debugging scenarios.

**Example troubleshooting guide** (`.claude/runbooks/api-errors.md`):

```markdown
# API Error Troubleshooting Decision Tree

## Starting Point: API Request Failing

### Question 1: What is the HTTP status code?

#### 400-level (Client Errors)

**401 Unauthorized**
- Check: Authentication token validity
  ```bash
  echo $AUTH_TOKEN | jwt decode -
  ```
- Check: Token expiration
- Action: Refresh token or re-authenticate
- Runbook: See `.claude/runbooks/auth-troubleshooting.md`

**403 Forbidden**
- Check: User permissions in database
  ```bash
  npm run console
  > User.findOne({ email: 'user@example.com' }).select('roles permissions')
  ```
- Check: Resource-level access control
- Action: Update user roles or permissions

**404 Not Found**
- Check: Route configuration in `src/routes/`
- Check: URL parameters (typos, wrong IDs)
- Action: Verify endpoint exists: `grep -r "router.get.*endpoint" src/routes/`

**422 Unprocessable Entity**
- Check: Request body validation
  ```bash
  # View validation schema
  grep -A 20 "validate.*RequestBody" src/controllers/
  ```
- Check: Required fields in request
- Action: Fix request payload to match schema

#### 500-level (Server Errors)

**500 Internal Server Error**
- Check: Application logs immediately
  ```bash
  tail -50 logs/error.log
  ```
- Check: Recent code changes
  ```bash
  git log --since="1 hour ago" --oneline
  ```
- Check: Database connectivity
  ```bash
  npm run db:ping
  ```
- Decision tree continues based on log findings...

**502 Bad Gateway**
- Check: Upstream service status
- Check: Reverse proxy configuration
- Check: Service running status
  ```bash
  pm2 status  # or equivalent
  ```

**503 Service Unavailable**
- Check: Database connection pool exhausted
  ```bash
  # Check active connections
  npm run db:connections
  ```
- Check: Memory usage
  ```bash
  free -h
  docker stats --no-stream
  ```
- Action: Scale resources or restart services

### Question 2: Is this affecting all users or specific users?

#### All Users
- Likely: Infrastructure or code issue
- Check: Recent deployments
- Check: Resource availability
- Escalation: Page on-call engineer

#### Specific Users
- Likely: Data or permission issue
- Check: User-specific data
- Check: Account status
- Action: User-level troubleshooting

### Question 3: When did this start happening?

#### Just now / Last few minutes
- Check: Recent deployments or changes
  ```bash
  git log -5 --oneline
  gh api /repos/:owner/:repo/deployments --jq '.[0]'
  ```
- Action: Consider rollback if deployment-related

#### Ongoing / Intermittent
- Check: Patterns in logs (time-based, load-based)
- Check: Third-party service status
- Action: Implement monitoring and capture more data

## Diagnostic Commands Reference

Run these to gather information:

```bash
# Full system health check
/db-health && /check-services && /recent-errors

# Capture request/response for debugging
curl -v -X POST https://api.example.com/endpoint \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"test": "data"}' \
  > debug-output.txt 2>&1

# Check dependency versions
npm ls <package-name>

# Database query performance
npm run db:console
> db.collection.find({}).explain("executionStats")
```

## Resolution Patterns

### Pattern 1: Token Expiration
```bash
# Refresh authentication
npm run auth:refresh

# Verify new token
curl -H "Authorization: Bearer $(cat .token)" https://api.example.com/me
```

### Pattern 2: Database Connection Pool Exhaustion
```bash
# Restart application to reset pool
pm2 restart app

# Increase pool size in config
# Edit config/database.js: pool.max = 20
```

### Pattern 3: Missing Environment Variable
```bash
# Compare .env with .env.example
diff <(sort .env.example | cut -d= -f1) <(sort .env | cut -d= -f1)

# Add missing variables
echo "MISSING_VAR=value" >> .env
```

## Related Runbooks
- `.claude/runbooks/auth-troubleshooting.md`
- `.claude/runbooks/database-issues.md`
- `.claude/runbooks/performance-degradation.md`
```

### Step 7: Create Interactive Troubleshooting Commands

Build commands that guide users through diagnostic processes interactively.

**Guided troubleshooting command** (`.claude/commands/guided-debug.md`):

```markdown
---
description: "Interactive guided debugging session"
argument-hint: "<issue-category>"
---

# Guided Debugging: $1

I'll walk you through systematic troubleshooting for: $1

## Phase 1: Information Gathering

First, I need to gather context. Please confirm you can provide:

1. **Error message** (exact text or screenshot)
2. **When it started** (timestamp or "just now")
3. **Reproduction steps** (how to trigger the issue)
4. **Environment** (local/staging/production)

Once you provide this information, I'll proceed with diagnostics.

## Phase 2: Initial Diagnostics

Based on your responses, I will:

1. **Identify the subsystem** affected (API, database, frontend, etc.)
2. **Check recent changes** in that subsystem
   ```bash
   git log --since="24 hours ago" -- <affected-path>/
   ```
3. **Review relevant logs**
4. **Run health checks** for affected components

## Phase 3: Hypothesis Formation

I will propose 2-3 potential root causes based on:
- Error signature
- Recent changes
- System state
- Historical patterns

For each hypothesis, I'll outline:
- **Likelihood**: High/Medium/Low
- **Diagnostic test**: How to confirm or rule out
- **Resolution**: Steps to fix if confirmed

## Phase 4: Systematic Testing

We'll test each hypothesis in order of likelihood:

1. Run diagnostic test
2. Analyze results
3. If confirmed, proceed to resolution
4. If ruled out, move to next hypothesis

## Phase 5: Resolution and Verification

Once root cause is identified:

1. **Apply fix** (with your approval)
2. **Verify resolution** (run tests, check logs)
3. **Document** the issue and fix in `.claude/runbooks/resolved-issues.md`
4. **Suggest prevention** measures

## Phase 6: Prevention

I will recommend:
- Monitoring/alerts to add
- Tests to write
- Documentation to update
- Code patterns to adopt

---

**Ready to begin?** Please provide the information from Phase 1.
```

### Step 8: Document Resolution Patterns and Create a Knowledge Base

Capture solutions to create a searchable troubleshooting knowledge base.

**Create a resolution log** (`.claude/runbooks/resolved-issues.md`):

```markdown
# Resolved Issues Log

Quick reference for previously solved problems.

## Format
Each entry contains:
- Date resolved
- Issue description
- Root cause
- Resolution steps
- Prevention measures

---

## 2025-11-15: Database Connection Pool Exhaustion

**Symptoms**:
- 500 errors on API endpoints
- "Connection pool timeout" in logs
- High database CPU usage

**Root Cause**:
Database connection pool limited to 5 connections, but concurrent request load reached 20+

**Resolution**:
1. Increased pool size in `config/database.js`:
   ```javascript
   pool: {
     min: 5,
     max: 20,  // was 5
     acquireTimeout: 30000
   }
   ```
2. Restarted application: `pm2 restart api`
3. Verified: Monitored connection count under load

**Prevention**:
- Added monitoring alert for connection pool usage >80%
- Documented pool sizing in `.claude/runbooks/database-tuning.md`
- Added load test to CI for connection handling

**Related**: See `.claude/runbooks/database-issues.md` for other DB problems

---

## 2025-11-12: TypeScript Build Failure After Dependency Update

**Symptoms**:
- Build fails with "Cannot find module '@types/node'"
- Worked before `npm update`

**Root Cause**:
`@types/node` version incompatible with TypeScript 5.3

**Resolution**:
1. Identified conflict: `npm ls @types/node`
2. Pinned compatible version:
   ```bash
   npm install --save-dev @types/node@20.10.0
   ```
3. Added to package.json as exact version
4. Rebuilt successfully

**Prevention**:
- Lock @types/* packages to exact versions
- Test builds before merging dependency updates
- Document compatible type package versions in README

**Related**: See `.claude/runbooks/typescript-issues.md`

---

## 2025-11-08: Production Deploy Rollback Due to Missing Environment Variable

**Symptoms**:
- Production site returning 500 errors after deploy
- Logs show "STRIPE_SECRET_KEY is not defined"

**Root Cause**:
New feature required STRIPE_SECRET_KEY but wasn't added to production environment

**Resolution**:
1. Immediately rolled back deployment:
   ```bash
   git revert HEAD
   git push
   gh workflow run deploy.yml --ref main
   ```
2. Added missing environment variable in hosting provider dashboard
3. Redeployed with variable present
4. Verified checkout flow working

**Prevention**:
- Created deployment checklist in `.claude/runbooks/deployment-checklist.md`
- Added pre-deployment script to check for new env vars:
  ```bash
  # .claude/commands/pre-deploy-check.md
  ```
- Added to CLAUDE.md: "Always check .env.example for new variables before deploying"

**Related**: See `.claude/runbooks/deployment-troubleshooting.md`

---

(Continue logging each resolved issue...)
```

**Create a searchable index** (`.claude/commands/search-runbooks.md`):

```markdown
---
description: "Search troubleshooting runbooks for specific issues"
argument-hint: "<search-term>"
---

# Search Runbooks: $ARGUMENTS

Searching all runbooks and troubleshooting documentation for: $ARGUMENTS

## Searching:
- `.claude/runbooks/*.md`
- `docs/debugging/*.md`
- CLAUDE.md debugging sections
- `.claude/runbooks/resolved-issues.md`

```bash
grep -r -i -n "$ARGUMENTS" .claude/runbooks/ docs/debugging/ CLAUDE.md --color=always
```

## Results Analysis

For each match found:
1. Show the file and line number
2. Display surrounding context (3 lines before and after)
3. Indicate which section of the runbook it appears in

## Relevant Matches

(Display results with context)

## Suggested Next Steps

Based on matches found, recommend:
- Which runbook to read first
- Which slash command to run for diagnostics
- Whether a similar issue was previously resolved
```

### Step 9: Integrate Runbooks with Git Workflow

Version control your runbooks and integrate them with your development workflow.

**Create a runbook update command** (`.claude/commands/update-runbook.md`):

```markdown
---
description: "Update runbook after resolving an issue"
argument-hint: "<runbook-name> <issue-summary>"
---

# Update Runbook: $1

You've just resolved an issue. Let's document it in the appropriate runbook.

## Issue Summary
$ARGUMENTS

## Documentation Steps

### 1. Identify Target Runbook
Which runbook should this be added to?
- `.claude/runbooks/database-issues.md`
- `.claude/runbooks/api-errors.md`
- `.claude/runbooks/deployment-troubleshooting.md`
- `.claude/runbooks/resolved-issues.md` (catch-all)
- New runbook needed?

### 2. Gather Resolution Details

Please provide or help me extract:
- **Symptoms**: What indicated this issue?
- **Root cause**: Why did it happen?
- **Resolution steps**: How was it fixed?
- **Prevention**: How to avoid in future?

### 3. Update Documentation

I will:
1. Read the target runbook
2. Add the new issue to the appropriate section
3. Follow the existing format and structure
4. Cross-reference related issues

### 4. Update Resolved Issues Log

Add entry to `.claude/runbooks/resolved-issues.md` with:
- Today's date
- Issue summary
- Quick reference to full runbook

### 5. Commit Changes

Create commit with message:
"docs: Add [$1] to troubleshooting runbook"

---

**Ready to proceed?** Provide the issue details and I'll update the documentation.
```

**Pre-commit hook to validate runbooks** (`.claude/hooks/PostToolUse.json`):

```json
[
  {
    "pattern": ".claude/runbooks/*.md",
    "command": "bash -c 'echo \"Runbook modified: ${FILE_PATH}\" >&2; if ! grep -q \"## Symptoms\" \"${FILE_PATH}\"; then echo \"Warning: Runbook missing Symptoms section\" >&2; fi; if ! grep -q \"## Resolution\" \"${FILE_PATH}\"; then echo \"Warning: Runbook missing Resolution section\" >&2; fi'"
  }
]
```

### Step 10: Create Team-Accessible Quick Reference

Build quick-reference guides that team members can access rapidly.

**Quick reference in CLAUDE.md**:

```markdown
## Quick Troubleshooting Reference

### Common Commands

**System Health**: `/system-health`
**Database Check**: `/db-health`
**Dependency Issues**: `/fix-deps`
**Deployment Problems**: `/troubleshoot-deploy <env>`
**Search Runbooks**: `/search-runbooks <keyword>`

### Emergency Procedures

**Production Down**:
1. Check status: `curl https://api.example.com/health`
2. View logs: `/prod-logs`
3. Review recent deploys: `gh api /repos/:owner/:repo/deployments | jq '.[0]'`
4. Rollback if needed: `git revert HEAD && /deploy prod`

**Database Issues**:
1. Check connection: `/db-health`
2. View active queries: `npm run db:active-queries`
3. Kill long-running queries: `npm run db:kill-query <pid>`
4. Restart if needed: `docker-compose restart postgres`

**Memory Issues**:
1. Check usage: `docker stats --no-stream`
2. Identify leaks: `/memory-profile`
3. Restart service: `pm2 restart app`

### Runbook Index

| Issue Category | Runbook Location |
|---------------|------------------|
| API Errors | `.claude/runbooks/api-errors.md` |
| Database | `.claude/runbooks/database-issues.md` |
| Deployment | `.claude/runbooks/deployment-troubleshooting.md` |
| Auth/Permissions | `.claude/runbooks/auth-troubleshooting.md` |
| Performance | `.claude/runbooks/performance-degradation.md` |
| Build/Dependencies | `.claude/runbooks/build-issues.md` |

### Escalation Path

1. **Self-service**: Check runbooks and run diagnostic commands
2. **Team Slack**: Post in #engineering-help with diagnostic output
3. **On-call**: Page if production impacting (use PagerDuty)
4. **Incident**: Create incident ticket for tracking

### Useful Diagnostic One-Liners

```bash
# Recent errors across all logs
find logs/ -name "*.log" -mmin -30 -exec tail {} \; | grep -i error

# Service status overview
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Database connection count
docker exec postgres psql -U app -c "SELECT count(*) FROM pg_stat_activity;"

# Memory usage by process
docker stats --no-stream --format "table {{.Name}}\t{{.MemUsage}}"

# Recent git changes
git log --since="2 hours ago" --oneline --all

# API endpoint health
for endpoint in /api/users /api/posts /api/auth; do echo -n "$endpoint: "; curl -s -o /dev/null -w "%{http_code}" http://localhost:3000$endpoint; echo; done
```
```

**Share with team via README**:

Create `docs/TROUBLESHOOTING.md` that links to all runbooks:

```markdown
# Troubleshooting Guide

Quick access to debugging runbooks and procedures.

## Getting Started

1. **Use Claude Code Web**: Connect to the repository and Claude will have access to all runbooks automatically via CLAUDE.md
2. **Search runbooks**: Use `/search-runbooks <keyword>` command
3. **Run diagnostics**: Use slash commands like `/db-health`, `/system-health`

## Runbook Directory

### By Category
- [API Errors](.claude/runbooks/api-errors.md)
- [Database Issues](.claude/runbooks/database-issues.md)
- [Deployment Problems](.claude/runbooks/deployment-troubleshooting.md)
- [Authentication](.claude/runbooks/auth-troubleshooting.md)
- [Performance](.claude/runbooks/performance-degradation.md)
- [Build Issues](.claude/runbooks/build-issues.md)

### Historical Reference
- [Resolved Issues Log](.claude/runbooks/resolved-issues.md) - Previously solved problems with solutions

## Available Diagnostic Commands

All team members have access to these custom slash commands in Claude Code Web:

| Command | Purpose |
|---------|---------|
| `/system-health` | Overall system health check |
| `/db-health [check]` | Database diagnostics |
| `/fix-deps` | Resolve dependency conflicts |
| `/troubleshoot-deploy <env>` | Deployment troubleshooting |
| `/search-runbooks <term>` | Search all runbooks |
| `/guided-debug <category>` | Interactive debugging session |
| `/update-runbook <name>` | Document a resolved issue |

## Contributing to Runbooks

When you solve a new issue:

1. Run `/update-runbook <category>` to document it
2. Or manually add to the appropriate runbook in `.claude/runbooks/`
3. Commit changes: `git commit -m "docs: Add solution for [issue]"`
4. Push to share with team

## Emergency Contacts

- **Production Issues**: #production-alerts Slack channel
- **On-Call Engineer**: PagerDuty
- **Database DBA**: #database-team Slack channel
```

## Expected Results

After creating comprehensive debugging runbooks and troubleshooting guides, you should have:

### Documentation Assets
- **Structured runbooks** in `.claude/runbooks/` covering common issue categories
- **CLAUDE.md** containing quick-reference debugging procedures automatically available to Claude
- **Resolved issues log** documenting historical problems and solutions
- **Team documentation** (README/docs) linking to all troubleshooting resources

### Automation Tools
- **Diagnostic slash commands** for rapid health checks and troubleshooting
- **Guided debugging commands** for interactive problem-solving
- **Search capabilities** to find relevant runbooks quickly
- **SessionStart hooks** providing automatic environment diagnostics
- **PostToolUse hooks** validating runbook structure and prompting tests

### Workflow Integration
- **Version-controlled runbooks** tracked in git with team access
- **Update procedures** for documenting new issues as they're resolved
- **Escalation paths** clearly defined for different severity levels
- **Quick reference guides** accessible to all team members

### Measurable Improvements
- **Reduced mean time to resolution (MTTR)** for common issues
- **Consistent debugging approaches** across team members
- **Knowledge retention** through documented solutions
- **Faster onboarding** for new team members with comprehensive guides
- **Fewer repeated issues** through documented prevention measures

## Troubleshooting

### Common Issue 1
**Problem**: Runbooks are created but team members don't use them or can't find them
**Solution**:
- Make runbooks discoverable: Add prominent link in README.md
- Add to CLAUDE.md so Claude automatically references them
- Create `/search-runbooks` command for easy searching
- Include runbook links in error messages or logs where applicable
- Demonstrate runbook usage in team meetings
- Add "check runbooks first" to team culture/onboarding
- Create slash commands that execute runbook procedures, making them easier to use than manual lookup

### Common Issue 2
**Problem**: Runbooks become outdated as the codebase changes
**Solution**:
- Add runbook review to sprint planning or retrospectives
- Create a `/validate-runbooks` command that checks if procedures still work
- Use git hooks to remind about runbook updates when related code changes
- Assign runbook ownership to specific team members or areas
- Include "update documentation" in definition of done for bug fixes
- Add timestamp/version to runbooks: "Last validated: 2025-11-16"
- Set calendar reminders for quarterly runbook reviews

### Common Issue 3
**Problem**: Runbooks are too long or complex, making them hard to use during incidents
**Solution**:
- Create tiered documentation: Quick reference in CLAUDE.md, detailed procedures in `.claude/runbooks/`
- Use decision trees to guide users to specific sections quickly
- Add "Quick Diagnosis" section at the top of each runbook
- Create slash commands that execute the most common runbook procedures
- Use clear headings and table of contents for easy scanning
- Keep symptom descriptions specific and searchable
- Create separate "Emergency Procedures" runbook for production incidents

### Common Issue 4
**Problem**: Runbooks describe manual procedures that could be automated
**Solution**:
- Convert runbook procedures into slash commands where possible
- Create hooks that automatically run diagnostics and alert to issues
- Build scripts in `scripts/` directory that execute multi-step procedures
- Use the runbook as specification for automation, then replace text with: "Run: /automated-fix-command"
- Document manual procedures as fallbacks: "Automated: /fix-deps. Manual: [steps below]"
- Prioritize automation of frequently-used runbooks first

### Common Issue 5
**Problem**: Different team members create runbooks with inconsistent formats
**Solution**:
- Use the template from Step 1 for all new runbooks
- Create `/create-runbook <name>` command that generates from template:
  ```markdown
  ---
  description: "Create new runbook from template"
  ---
  Create a new runbook at `.claude/runbooks/$ARGUMENTS.md` using the template structure:

  - Symptoms
  - Diagnostic Steps
  - Root Causes
  - Resolution Steps
  - Prevention
  - Related Issues
  ```
- Add PostToolUse hook to validate runbook structure (shown in Step 9)
- Conduct peer review for new runbooks before merging
- Maintain a runbook style guide in `.claude/runbooks/README.md`

### Common Issue 6
**Problem**: Runbooks don't capture environment-specific information (local vs staging vs production)
**Solution**:
- Add environment sections to runbooks: "### For Local Development", "### For Production"
- Create environment-specific runbooks when procedures differ significantly
- Use conditional logic in slash commands: "If environment is production, then..."
- Document environment differences in CLAUDE.md
- Create separate commands per environment: `/troubleshoot-deploy-prod` vs `/troubleshoot-deploy-staging`
- Include environment detection in diagnostic hooks

### Common Issue 7
**Problem**: Cannot track which runbooks are most useful or which issues recur most frequently
**Solution**:
- Add usage tracking to slash commands (log to file when invoked)
- Maintain statistics in resolved-issues.md: "This issue occurred 5 times"
- Review git history of runbooks to see which are updated most: `git log --oneline .claude/runbooks/`
- Create analytics command:
  ```markdown
  /runbook-analytics

  Analyze:
  - Most frequently updated runbooks (git log analysis)
  - Runbooks with most search hits (grep logs)
  - Common keywords in searches
  - Seasonal patterns in issue types
  ```
- Use findings to prioritize automation and prevention efforts

## Additional Tips

- **Start small**: Begin with 3-5 runbooks for your most common issues, then expand over time

- **Learn from incidents**: After every significant issue, immediately document it while details are fresh

- **Use Claude to bootstrap**: Ask Claude to analyze your logs, issues, and docs to generate initial runbooks, then refine them

- **Make runbooks executable**: Transform static documentation into executable slash commands when possible

- **Cross-reference liberally**: Link related runbooks together so troubleshooters can navigate easily

- **Include negative information**: Document what doesn't work or what to avoid, not just solutions

- **Add visual aids**: Include ASCII diagrams for architecture, decision trees, or system flows

- **Version your runbooks**: Use git tags or branches to maintain runbook versions matching code versions

- **Test your runbooks**: Periodically follow runbook procedures to ensure they still work

- **Capture tribal knowledge**: When senior team members share debugging wisdom, immediately add it to runbooks

- **Integrate with monitoring**: Link runbook references in alerts (e.g., "High error rate - see `.claude/runbooks/api-errors.md`")

- **Make them searchable**: Use consistent terminology and keywords that match error messages

- **Include cost/impact**: Note which resolution steps are expensive (e.g., "restarting this service causes 30s downtime")

- **Document prerequisites**: State what access, permissions, or tools are needed for each procedure

- **Maintain a runbook changelog**: Track what's been added, updated, or deprecated in `.claude/runbooks/CHANGELOG.md`

## Related Articles
- KB-051: How to fix a specific bug using Claude Code Web
- KB-066: How to debug hook failures and troubleshoot issues
- KB-068: How to create your first custom slash command
- KB-069: How to write slash commands as Markdown files
- KB-074: How to debug and test custom slash commands
- KB-080: How to troubleshoot MCP server connection issues

## Sources
- How Anthropic teams use Claude Code - https://www.claude.com/blog/how-anthropic-teams-use-claude-code (Accessed: November 16, 2025)
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code: Part 11 - Troubleshooting and Recovery - https://www.letanure.dev/blog/2025-08-09--claude-code-part-11-troubleshooting-recovery (Accessed: November 16, 2025)
- Claude Code on the Web Complete Guide for Developers (2025) - https://www.cursor-ide.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
- Troubleshooting Claude Code Problems: A Developer's Guide - https://www.arsturn.com/blog/decoding-the-glitches-your-guide-to-troubleshooting-claude-code-problems (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code Web debugging runbooks troubleshooting best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
