# How to Safely Provide API Keys Without Exposing Them to Claude

**Article ID**: KB-045
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
When working with Claude Code Web, you may need to use API keys and other secrets in your application code. This guide shows you how to securely manage API keys and sensitive credentials so your application can access them while preventing Claude from reading or accidentally exposing these values in responses, logs, or code changes.

## Prerequisites
- Active Claude Pro or Max subscription
- Connected repository with code requiring API keys
- Basic understanding of environment variables
- Familiarity with your repository's file structure (KB-013)
- Git basics and .gitignore files

## Understanding API Key Security in Claude Code Web

### Why API Key Protection Matters

**Security risks:**
- Accidental exposure in public repositories
- Leaking credentials in session logs or chat history
- Claude inadvertently displaying secrets when debugging errors
- Hard-coded keys committed to version control
- Exposure through file sharing or code reviews

**Recent security considerations:**
As of November 2025, security researchers have identified that AI coding assistants, including Claude, may inadvertently expose environment variables when encountering authentication errors or system confusion, even when values aren't explicitly requested. This makes proper protection configuration essential.

### Three-Layer Security Strategy

**Layer 1: Environment variables**
- Store secrets outside source code
- Use .env files for local development
- Never hard-code API keys

**Layer 2: Prevent Claude access**
- Configure permissions to deny .env file access
- Block Claude from reading sensitive files
- Use settings.json protection rules

**Layer 3: Git protection**
- Add .env to .gitignore
- Prevent committing secrets to repository
- Keep credentials out of version control

## Steps to Safely Provide API Keys

### Step 1: Create Environment Variable File

**Set up .env file for local development:**

1. Navigate to your project root directory
2. Create a new file named `.env`
3. Add your API keys in KEY=VALUE format
4. Never add quotes around values (unless part of the key itself)

**Example .env file:**
```bash
# API Keys and Secrets - DO NOT COMMIT
ANTHROPIC_API_KEY=sk-ant-api03-xxxxxxxxxxxxxxxxxxxxx
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxx
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
STRIPE_SECRET_KEY=sk_test_xxxxxxxxxxxxxxxxxxxxx
JWT_SECRET=your-super-secret-jwt-key-here
AWS_ACCESS_KEY_ID=AKIAXXXXXXXXXXXXX
AWS_SECRET_ACCESS_KEY=xxxxxxxxxxxxxxxxxxxxxxxx

# Third-party services
SENDGRID_API_KEY=SG.xxxxxxxxxxxxxxxxxxxxx
GOOGLE_MAPS_API_KEY=AIzaxxxxxxxxxxxxxxxxxxxxx
```

**File naming patterns to protect:**
- `.env` - main environment file
- `.env.local` - local overrides
- `.env.development` - development settings
- `.env.production` - production settings (never commit)
- `.env.test` - test environment
- `credentials.json` - service account files
- `secrets.yml` - configuration secrets

### Step 2: Configure Claude Code Permissions

**Deny Claude access to sensitive files:**

1. Navigate to your project root directory
2. Create directory `.claude` if it doesn't exist
3. Create or edit `.claude/settings.json`
4. Add permission denial rules

**Create .claude/settings.json:**
```json
{
  "permissions": {
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./config/credentials.json)",
      "Read(./secrets/**)",
      "Read(./.aws/credentials)",
      "Read(./service-account-key.json)"
    ]
  }
}
```

**What this does:**
- Completely blocks Claude from reading these files
- Prevents accidental exposure in responses
- Works at the permission system level
- Applies to all Claude Code sessions in this repository

**Permission pattern syntax:**
- `Read(./.env)` - exact file in root
- `Read(./.env.*)` - any .env.* variant
- `Read(./secrets/**)` - entire directory recursively
- `Read(./path/to/file.json)` - specific file path

**Verify permissions work:**
```
Expected behavior: If you ask Claude to read .env file,
it should respond: "I don't have permission to read that file"
rather than showing the contents.
```

### Step 3: Protect from Git Commits

**Add .env to .gitignore:**

1. Open or create `.gitignore` in project root
2. Add patterns for all environment files
3. Add other credential files
4. Commit .gitignore itself

**Add to .gitignore:**
```gitignore
# Environment variables and secrets
.env
.env.*
.env.local
.env.development
.env.production
.env.test

# Credentials
credentials.json
service-account-key.json
secrets.yml
secrets.yaml
config/credentials/*

# Cloud provider credentials
.aws/credentials
.gcp/keyfile.json
.azure/credentials

# Other sensitive files
*.pem
*.key
*.p12
*.pfx
```

**Verify .gitignore works:**
```bash
# Check what files would be committed
git status

# .env should NOT appear in untracked files if it exists
# If it does appear, .gitignore isn't working correctly
```

**If .env was already committed:**
```bash
# Remove from git history (use with caution)
git rm --cached .env
git commit -m "Remove .env from version control"

# Then rotate all exposed API keys immediately
```

### Step 4: Use Environment Variables in Code

**Access environment variables in your application:**

**Node.js / JavaScript:**
```javascript
// Install dotenv package
// npm install dotenv

// Load environment variables at app start
require('dotenv').config();

// Access variables
const apiKey = process.env.ANTHROPIC_API_KEY;
const dbUrl = process.env.DATABASE_URL;

// Example usage
const response = await fetch('https://api.example.com', {
  headers: {
    'Authorization': `Bearer ${process.env.API_KEY}`
  }
});
```

**Python:**
```python
# Install python-dotenv package
# pip install python-dotenv

from dotenv import load_dotenv
import os

# Load environment variables
load_dotenv()

# Access variables
api_key = os.getenv('ANTHROPIC_API_KEY')
db_url = os.getenv('DATABASE_URL')

# Example usage
headers = {
    'Authorization': f'Bearer {api_key}'
}
```

**Best practices in code:**
```javascript
// ✅ GOOD: Use environment variables
const apiKey = process.env.API_KEY;

// ❌ BAD: Hard-coded credentials
const apiKey = 'sk-ant-api03-xxxxxxxxxxxxxxxxxxxxx';

// ✅ GOOD: Validate environment variables at startup
if (!process.env.API_KEY) {
  throw new Error('API_KEY environment variable is required');
}

// ✅ GOOD: Never log the actual key value
console.log('API Key configured:', process.env.API_KEY ? 'Yes' : 'No');

// ❌ BAD: Logging actual key value
console.log('API Key:', process.env.API_KEY);
```

### Step 5: Create .env.example Template

**Provide template for other developers:**

1. Create `.env.example` file
2. Include all required variables
3. Use placeholder values
4. Add helpful comments
5. Commit .env.example to git (it's safe)

**Example .env.example:**
```bash
# API Keys and Secrets
# Copy this file to .env and fill in your actual values

# Anthropic API Key - Get from https://console.anthropic.com/
ANTHROPIC_API_KEY=sk-ant-api03-your-key-here

# OpenAI API Key - Get from https://platform.openai.com/
OPENAI_API_KEY=sk-proj-your-key-here

# Database connection
DATABASE_URL=postgresql://username:password@localhost:5432/database_name

# Stripe - Get from https://dashboard.stripe.com/
STRIPE_SECRET_KEY=sk_test_your-key-here

# JWT Secret - Generate a random string
JWT_SECRET=your-random-secret-here

# AWS Credentials - Get from AWS IAM Console
AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY
AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY

# SendGrid - Get from https://app.sendgrid.com/
SENDGRID_API_KEY=SG.your-key-here
```

**README documentation:**
```markdown
## Setup

1. Copy `.env.example` to `.env`:
   ```bash
   cp .env.example .env
   ```

2. Fill in your actual API keys in `.env`

3. Never commit the `.env` file to git
```

### Step 6: Guide Claude Without Exposing Keys

**How to work with Claude on API integration:**

**✅ Safe approach - Reference without revealing:**
```
Task for Claude:
"Update the user authentication to use the API key from
process.env.ANTHROPIC_API_KEY instead of the hard-coded value.
The .env file already has the key configured."
```

**✅ Safe approach - Use placeholders:**
```
Task for Claude:
"Add Stripe payment integration. Use process.env.STRIPE_SECRET_KEY
for the secret key. I'll add the actual key to my .env file myself."
```

**❌ Unsafe approach - Pasting actual keys:**
```
"Use this API key: sk-ant-api03-xxxxxxxxxxxxxxxxxxxxx"
```

**✅ Safe approach - Configuration instructions:**
```
"Set up the database connection using these environment variables:
- DATABASE_URL for the connection string
- DB_POOL_SIZE for connection pool size (default: 10)

I've already added the values to .env which you can't access,
but use them in the code via process.env."
```

## Expected Results

After implementing these security measures:

**File structure:**
```
your-project/
├── .env                          # Ignored by git, blocked from Claude
├── .env.example                  # Committed to git, safe template
├── .gitignore                    # Contains .env
├── .claude/
│   └── settings.json             # Denies access to .env
└── src/
    └── config/
        └── environment.js        # Loads env vars safely
```

**Behavior:**
- API keys work in your application
- .env file never appears in git status
- Claude cannot read .env contents
- If Claude tries to access .env, gets permission denied
- No secrets in session logs or chat history
- Team members use .env.example as template

**Security indicators:**
- `git status` never shows .env as tracked
- Claude responses never display actual key values
- Environment variables load correctly in application
- No secrets in repository history

## Troubleshooting

### Common Issue 1
**Problem**: Environment variables not loading in application
**Solution**:
- Verify .env file is in project root directory
- Check .env syntax (no quotes unless needed, no spaces around =)
- Ensure dotenv package is installed (`npm install dotenv` or `pip install python-dotenv`)
- Load dotenv at very start of application: `require('dotenv').config();`
- Check for typos in variable names
- Try logging `process.env` to see what's loaded
- Restart development server after changing .env

### Common Issue 2
**Problem**: Claude can still read .env file despite permissions
**Solution**:
- Verify `.claude/settings.json` exists in project root
- Check JSON syntax is valid (no trailing commas)
- Ensure permissions.deny array includes `"Read(./.env)"`
- Try restarting Claude Code session
- Check file path is relative to project root (use `./`)
- Verify .claude directory is in project root, not subdirectory

### Common Issue 3
**Problem**: Accidentally committed .env to git
**Solution**:
```bash
# Remove from git tracking
git rm --cached .env

# Commit the removal
git commit -m "Remove .env from version control"

# CRITICAL: Rotate all exposed API keys immediately
# - Generate new keys in service dashboards
# - Update .env with new keys
# - Invalidate/delete old exposed keys

# If pushed to remote repository
git push

# Consider using git-secrets or similar tools to prevent future commits
```

### Common Issue 4
**Problem**: Claude needs to work with API integration but can't see credentials
**Solution**:
- Use placeholder values in code examples
- Guide Claude to use `process.env.VARIABLE_NAME` syntax
- Provide .env.example as reference for variable names
- Explain the environment variable structure without showing actual values
- Example: "The API key is available as process.env.STRIPE_KEY and starts with 'sk_live_'"

### Common Issue 5
**Problem**: Different environments need different API keys
**Solution**:
```bash
# Use environment-specific files
.env.development    # Local development keys
.env.staging        # Staging environment keys
.env.production     # Production keys (never commit)

# Load based on environment
# Node.js example:
require('dotenv').config({
  path: `.env.${process.env.NODE_ENV}`
});

# Add all variants to .gitignore
.env.*
```

### Common Issue 6
**Problem**: Team members don't know what environment variables are needed
**Solution**:
- Maintain comprehensive .env.example file
- Add comments explaining each variable
- Document in README.md setup section
- Include links to where to obtain each API key
- List which variables are required vs optional
- Specify format/validation for each variable

## Additional Tips

### API Key Rotation

**Regular rotation schedule:**
```
Best practice: Rotate API keys every 90 days
- Set calendar reminder
- Generate new keys
- Update .env file
- Invalidate old keys
- Test application still works
```

### Environment Variable Naming Conventions

**Use consistent naming:**
```bash
# ✅ GOOD: Clear, uppercase, underscores
ANTHROPIC_API_KEY=xxx
DATABASE_URL=xxx
STRIPE_SECRET_KEY=xxx
JWT_SECRET=xxx

# ❌ AVOID: Lowercase, unclear names
anthropic_key=xxx
db=xxx
secret=xxx
```

### Validation and Defaults

**Validate critical variables at startup:**
```javascript
// config/environment.js
const requiredEnvVars = [
  'ANTHROPIC_API_KEY',
  'DATABASE_URL',
  'JWT_SECRET'
];

requiredEnvVars.forEach(varName => {
  if (!process.env[varName]) {
    throw new Error(`Missing required environment variable: ${varName}`);
  }
});

// Optional variables with defaults
const config = {
  port: process.env.PORT || 3000,
  nodeEnv: process.env.NODE_ENV || 'development',
  logLevel: process.env.LOG_LEVEL || 'info'
};
```

### Cloud Deployment

**Use cloud provider secret management:**
- **Vercel**: Use Environment Variables in project settings
- **Netlify**: Use Environment Variables in site settings
- **AWS**: Use Secrets Manager or Parameter Store
- **Google Cloud**: Use Secret Manager
- **Azure**: Use Key Vault
- **Heroku**: Use Config Vars

**Never commit production keys to any file**

### Multiple API Keys for Same Service

**Separate development and production:**
```bash
# Development keys (test mode)
STRIPE_SECRET_KEY_TEST=sk_test_xxxxxxxxxxxxx
STRIPE_PUBLIC_KEY_TEST=pk_test_xxxxxxxxxxxxx

# Production keys (live mode)
STRIPE_SECRET_KEY_LIVE=sk_live_xxxxxxxxxxxxx
STRIPE_PUBLIC_KEY_LIVE=pk_live_xxxxxxxxxxxxx

# Use based on environment
STRIPE_SECRET_KEY=${NODE_ENV === 'production' ? STRIPE_SECRET_KEY_LIVE : STRIPE_SECRET_KEY_TEST}
```

### Security Scanning

**Tools to detect exposed secrets:**
```bash
# git-secrets - Prevents committing secrets
# https://github.com/awslabs/git-secrets
git secrets --install
git secrets --register-aws

# truffleHog - Scans for secrets in git history
# https://github.com/trufflesecurity/trufflehog
trufflehog git file://. --only-verified

# gitleaks - Detect hardcoded secrets
# https://github.com/gitleaks/gitleaks
gitleaks detect --source .
```

### Emergency Response

**If API keys are exposed:**
1. **Immediately** rotate compromised keys
2. Generate new keys in service dashboard
3. Invalidate/delete exposed keys
4. Update .env with new keys
5. Review access logs for unauthorized usage
6. Check for any damage or unauthorized access
7. Update team on incident
8. Review security practices to prevent recurrence

### Documentation for Team

**Include in project README:**
```markdown
## Environment Variables

Required environment variables:

| Variable | Description | Where to Get |
|----------|-------------|--------------|
| ANTHROPIC_API_KEY | Anthropic API Key | https://console.anthropic.com/ |
| DATABASE_URL | PostgreSQL connection string | Your database provider |
| JWT_SECRET | Secret for JWT signing | Generate random string |

Setup:
1. Copy `.env.example` to `.env`
2. Fill in actual values
3. Never commit `.env` to git
```

## Related Articles
- KB-013: How to view and navigate your repository's file structure
- KB-015: How to view changes made by Claude in your repository
- KB-007: How to describe a task effectively for Claude to understand
- KB-021: How to provide additional context to Claude during a task

## Sources
- Managing API Key Environment Variables in Claude Code - https://support.claude.com/en/articles/12304248-managing-api-key-environment-variables-in-claude-code (Accessed: November 16, 2025)
- API Key Best Practices: Keeping Your Keys Safe and Secure - https://support.claude.com/en/articles/9767949-api-key-best-practices-keeping-your-keys-safe-and-secure (Accessed: November 16, 2025)
- Claude Code Settings Documentation - https://docs.claude.com/en/docs/claude-code/settings (Accessed: November 16, 2025)
- Securing Claude Code: Protecting Your Sensitive Files - https://www.devproblems.com/securing-claude-code-protecting-your-sensitive-files-like-env/ (Accessed: November 16, 2025)
- Claude Code Security Best Practices - https://www.backslash.security/blog/claude-code-security-best-practices (Accessed: November 16, 2025)
- Security Bug Report: Claude Code Environment Variables - https://github.com/anthropics/claude-code/issues/11271 (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
