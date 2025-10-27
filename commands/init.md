---
description: Initialize AIVory compliance validation for your project
---

# AIVory Init - Project Setup

Initialize AIVory Guard for your project with configuration, MCP server setup, git hooks, and backend registration. This sets up compliance validation for your development workflow.

## Instructions

You are the AIVory setup assistant. Guide users through project initialization:

## Phase 1: Welcome & Prerequisites

### 1.1 Welcome Message

```markdown
AIVory Guard - Project Initialization
======================================

This wizard will set up AIVory compliance validation for your project.

What we'll configure:
1. MCP server setup (if not installed)
2. Project compliance standards
3. Git hooks (optional)
4. Backend registration (optional)
5. Team configuration

Estimated time: 3-5 minutes

Ready to begin? (yes/no)
```

### 1.2 Check Prerequisites

Verify required tools are installed:

```bash
# Check git
git --version

# Check gh (GitHub CLI) if available
gh --version 2>/dev/null || echo "GitHub CLI not installed (optional)"

# Check if we're in a git repo
git rev-parse --git-dir

# Check current directory
pwd
```

Display results:
```markdown
Prerequisites Check
===================

✓ Git installed: v2.42.0
✓ In git repository: /path/to/project
✓ GitHub CLI: Installed (optional)

Project detected:
- Root: /path/to/project
- Remote: https://github.com/owner/repo.git
- Branch: main

Looks good! Let's continue.
```

If not in git repo:
```markdown
⚠️  Not in a git repository.

AIVory works best with git-tracked projects. Would you like to:
1. Continue anyway (limited features)
2. Exit and run from git repository root

Your choice:
```

## Phase 2: MCP Server Setup

### 2.1 Check MCP Server Status

Check if AIVory MCP server is installed:

```bash
# Check if MCP server is configured
claude mcp list | grep aivory || echo "NOT_FOUND"
```

### 2.2 If MCP Server Not Found

Display setup instructions:

```markdown
MCP Server Not Found
====================

AIVory requires the MCP server to perform compliance scanning.

The MCP server:
- Communicates with AIVory backend
- Handles authentication
- Performs compliance analysis

Would you like to install it now? (yes/no)
```

If user says **yes**, provide installation command:

```markdown
Installing AIVory MCP Server
=============================

I'll guide you through the installation.

First, do you have an AIVory account?
1. Yes, I have an API key (all 17+ standards, unlimited scans)
2. No, use free tier (OWASP only, 15 files/scan, 3 scans/day)

Your choice:
```

**If user chooses option 1 (has API key):**

```markdown
Great! Let's set up with authentication.

You can get your API key from: https://app.aivory.net/tokens

Steps:
1. Visit https://app.aivory.net/tokens
2. Log in to your AIVory account
3. Copy your API key (starts with 'aiv_' or similar)

(If you don't have an account yet, register at https://aivory.net/register/plans)

Please paste your API key below:
Your API key: _______
```

After getting API key, run:

```bash
claude mcp add \
  --transport stdio \
  --env AIVORY_API_KEY=<user-provided-key> \
  --env AIVORY_SERVER_URL=https://app.aivory.net/mcp \
  aivory \
  -- npx -y @aivorynet/guard
```

Show success:
```markdown
✓ MCP server installed with authentication

  Access: All 17+ compliance standards
  Limits: Unlimited scans

  Testing connection...
```

**If user chooses option 2 (free tier):**

```markdown
Setting up free tier access.

Free Tier:
- Standards: OWASP Top 10 only
- Limits: 15 files per scan, 3 scans per day
- Upgrade anytime at https://aivory.net/register/plans

Installing...
```

Run:
```bash
claude mcp add \
  --transport stdio \
  aivory \
  -- npx -y @aivorynet/guard
```

Show success:
```markdown
✓ MCP server installed (free tier)

  Access: OWASP Top 10
  Limits: 15 files/scan, 3 scans/day

  To upgrade: Visit https://aivory.net/register/plans

  Testing connection...
```

### 2.3 Test MCP Connection

Verify MCP server is working:

```bash
# Test with health check
claude mcp list | grep aivory
```

If successful:
```markdown
✓ MCP server connected successfully

Ready to configure project settings.
```

If failed:
```markdown
✗ MCP server installation failed

Please try manually:
1. Run: npx -y @aivorynet/guard
2. Check if npx is installed: npx --version
3. Try again or contact support@aivory.net

Continue without MCP server? (not recommended)
```

### 2.4 If MCP Server Already Installed

```markdown
MCP Server Status
=================

✓ AIVory MCP server is already installed

Current configuration:
- Server: @aivorynet/guard
- Status: Active

Would you like to:
1. Keep current configuration
2. Reconfigure (add/update API key)
3. Continue to project setup

Your choice:
```

If user chooses **2 (Reconfigure)**:

```markdown
Reconfiguring MCP Server
=========================

Do you want to:
1. Add/update API key (get full access to 17+ standards)
2. Remove API key (downgrade to free tier - OWASP only)

Your choice:
```

**If option 1 (Add/update API key):**
```markdown
Get your API key from: https://app.aivory.net/tokens

Steps:
1. Visit https://app.aivory.net/tokens
2. Log in to your AIVory account
3. Copy your API key

Please paste your API key:
Your API key: _______
```

Then:
```bash
# Remove old configuration
claude mcp remove aivory

# Add with new API key
claude mcp add \
  --transport stdio \
  --env AIVORY_API_KEY=<user-provided-key> \
  --env AIVORY_SERVER_URL=https://app.aivory.net/mcp \
  aivory \
  -- npx -y @aivorynet/guard
```

**If option 2 (Remove API key):**
```bash
# Remove old configuration
claude mcp remove aivory

# Add without API key (free tier)
claude mcp add \
  --transport stdio \
  aivory \
  -- npx -y @aivorynet/guard
```

## Phase 3: Configure Compliance Standards

### 3.1 Select Compliance Standards

Ask user which standards to enforce:

**IMPORTANT**: Show different options based on authentication status:

**If user installed MCP with API key (authenticated):**
Show all 17+ standards available.

**If user installed MCP without API key (free tier):**
```markdown
Note: Free tier provides OWASP Top 10 only.
OWASP will be automatically enabled.

To access all 17+ standards:
- Visit https://aivory.net/register/plans
- Get your API key
- Run /aivory-init again to reconfigure

Continue with OWASP only? (yes/no)
```

If authenticated, ask user which standards to enforce:

```markdown
Compliance Standards Selection
===============================

AIVory supports 17+ compliance standards. Which standards apply to your project?

Industry-specific:
[ ] OWASP Top 10 (Application Security) - RECOMMENDED
[ ] PCI-DSS (Payment Card Industry)
[ ] HIPAA (Healthcare)
[ ] SOC 2 (Service Organizations)
[ ] ISO 27001 (Information Security)

Data Protection:
[ ] GDPR (EU General Data Protection)
[ ] CCPA (California Consumer Privacy)
[ ] DSGVO (German Data Protection)

Financial:
[ ] DORA (Digital Operational Resilience)
[ ] AML (Anti-Money Laundering)
[ ] Geldwäschegesetz (German AML)

Other:
[ ] NIS2 (Network and Information Security)
[ ] EU AI Act
[ ] ISO 27017 (Cloud Security)
[ ] ISO 27018 (Cloud Privacy)
[ ] TISAX (Automotive)
[ ] Hinweisgeberschutzgesetz (Whistleblower Protection)

Select all that apply, or leave empty to scan ALL standards.

Your selection:
```

Use AskUserQuestion tool for multi-select if available, or parse user input.

### 2.2 Configure Settings

Ask for additional settings:

```markdown
Additional Configuration
========================

1. Confidence Threshold (0-100):
   Minimum confidence score for reporting violations.
   Recommended: 80 (balance between accuracy and false positives)
   Your value: [80]

2. AI-Generated Code Detection:
   Detect AI-generated code for enhanced security review?
   Recommended: Yes
   Enable? (yes/no): [yes]

3. Auto-scan on Commit:
   Automatically scan code before git commits?
   Warning: May slow down commits
   Enable? (yes/no): [no]

4. Backend URL:
   AIVory backend for dashboard and metrics.
   Default: https://app.aivory.net
   Custom URL (or press Enter for default): []
```

## Phase 4: Create Configuration File

### 4.1 Generate .aivory/config.yml

Create project configuration:

```yaml
# AIVory Guard Configuration
# Generated: 2025-10-26

project:
  name: [Project Name from git config or package.json]
  repository: [Git remote URL]

compliance:
  enabled_standards:
    - OWASP
    - PCI-DSS
    [... user-selected standards ...]

  confidence_threshold: 80

  ai_detection:
    enabled: true

  auto_scan:
    on_commit: false
    on_pr: true

backend:
  url: https://app.aivory.net
  api_key: ${AIVORY_API_KEY}  # Set via environment variable

integration:
  git_hooks:
    pre_commit: false
    pre_push: false

  notifications:
    slack_webhook: ""
    email: ""

# Standard-specific configuration
standards:
  owasp:
    severity_threshold: high

  gdpr:
    data_classification_required: true

  pci_dss:
    scope:
      - src/payment/
      - src/checkout/
```

Write configuration:
```
Write file to: .aivory/config.yml
```

Show success:
```markdown
✓ Configuration created: .aivory/config.yml

You can edit this file later to adjust settings.
```

## Phase 5: Git Hooks (Optional)

### 5.1 Offer Git Hooks

```markdown
Git Hooks Setup (Optional)
===========================

Git hooks can automatically run AIVory scans at key points:

1. Pre-commit hook: Scan staged files before commit
   - Prevents committing code with violations
   - May slow down commits slightly

2. Pre-push hook: Scan before pushing to remote
   - Less intrusive than pre-commit
   - Catches violations before they reach remote

Would you like to install git hooks? (yes/no/later)
```

### 5.2 Install Hooks if Requested

If user agrees, create git hooks:

**Pre-commit hook** (`.git/hooks/pre-commit`):
```bash
#!/bin/sh
# AIVory Guard Pre-commit Hook

echo "Running AIVory compliance scan..."

# Get staged files
STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(java|py|js|ts|go|rs|rb|php|cs|kt)$')

if [ -z "$STAGED_FILES" ]; then
  echo "No source files to scan."
  exit 0
fi

# Run AIVory scan
claude code "/aivory-scan --staged-only --fail-on-critical"

# Capture exit code
EXIT_CODE=$?

if [ $EXIT_CODE -ne 0 ]; then
  echo ""
  echo "❌ AIVory scan found critical violations!"
  echo "Fix violations or use 'git commit --no-verify' to skip."
  exit 1
fi

echo "✓ AIVory scan passed"
exit 0
```

Make executable:
```bash
chmod +x .git/hooks/pre-commit
```

Show success:
```markdown
✓ Git hooks installed

Pre-commit hook will run AIVory scans automatically.
To skip hook: git commit --no-verify
```

## Phase 6: Backend Registration (Optional)

### 6.1 Offer Backend Registration

```markdown
Backend Registration (Optional)
================================

Register your project with AIVory backend for:
- Historical violation tracking
- Dashboard metrics and trends
- Team collaboration
- Compliance reporting

This requires:
- Backend running at https://app.aivory.net
- Valid API key or user account

Register project now? (yes/no/later)
```

### 6.2 Register if Requested

If user agrees:

```bash
# Register project with backend
curl -X POST https://app.aivory.net/api/v1/projects \
  -H "Authorization: Bearer ${AIVORY_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "[Project Name]",
    "repository": "[Git URL]",
    "enabled_standards": ["OWASP", "PCI-DSS", ...],
    "default_branch": "main"
  }'
```

Show result:
```markdown
✓ Project registered with AIVory backend

Project ID: abc-123-def
Dashboard: https://app.aivory.net/dashboard/projects/abc-123-def

Your scans will now be tracked in the dashboard.
```

## Phase 7: Complete Setup

### 7.1 Summary

```markdown
AIVory Guard Setup Complete!
=============================

Configuration Summary:
✓ Standards: OWASP, PCI-DSS, GDPR, SOC2 (4 standards)
✓ Confidence threshold: 80%
✓ AI detection: Enabled
✓ MCP server: Connected
✓ Git hooks: Installed
✓ Backend: Registered

Files created:
- .aivory/config.yml
- .git/hooks/pre-commit

Next Steps:
===========

1. Run your first scan:
   /aivory-scan

2. Scan before committing code:
   /aivory-scan --staged

3. Review pull requests:
   /aivory-review

4. View compliance dashboard:
   /aivory-dashboard

5. Fix violations interactively:
   /aivory-fix

Documentation:
- Plugin README: aivory-claude-plugin/README.md
- Backend docs: documentation/mcp/
- Online: https://aivory.net/docs

Need help? Run: /help aivory

Happy compliant coding!
```

### 7.2 Save Setup Log

Create setup log:
```
Write file to: .aivory/setup-log.txt with:
- Timestamp
- Selected standards
- Configuration values
- MCP server status
- Hook installation status
```

## Error Handling

- **Not in git repo**: Warn user, offer to continue with limited features
- **MCP server exists**: Skip MCP setup, note already configured
- **Backend unavailable**: Continue setup, note backend can be started later
- **Permission errors**: Show error, suggest using sudo or checking permissions
- **Git hooks fail**: Warn user, continue with other setup steps

## Configuration

Create default configuration respecting user choices:
- Selected standards
- Confidence threshold
- AI detection setting
- Auto-scan preferences
- Backend URL

## Notes

- Interactive setup for better user experience
- Graceful degradation if components unavailable
- Create `.aivory/` directory for all AIVory files
- Git hooks are optional but recommended for teams
- Backend registration enables advanced features
- Configuration can be edited manually later
