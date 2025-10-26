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
1. Project compliance standards
2. MCP server connection
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

## Phase 2: Configure Compliance Standards

### 2.1 Select Compliance Standards

Ask user which standards to enforce:

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
   Default: http://localhost:8080
   Custom URL (or press Enter for default): []
```

## Phase 3: Create Configuration File

### 3.1 Generate .aivory/config.yml

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
  url: http://localhost:8080
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

## Phase 4: MCP Server Setup

### 4.1 Check MCP Server Status

Check if AIVory MCP server is already configured:

```bash
# Check current MCP configuration
claude mcp list | grep aivory || echo "Not configured"
```

### 4.2 Configure MCP Server

If not configured:

```markdown
MCP Server Setup
================

AIVory uses Model Context Protocol (MCP) to integrate with Claude Code.

Installing AIVory Guard MCP server...
```

```bash
# Add AIVory MCP server
claude mcp add \
  --transport stdio \
  --env AIVORY_API_KEY=${AIVORY_API_KEY:-demo-key} \
  --env AIVORY_SERVER_URL=${AIVORY_SERVER_URL:-http://localhost:8080} \
  aivory \
  -- npx -y @aivorynet/guard
```

Show result:
```markdown
✓ AIVory MCP server configured

The MCP server provides these tools:
- scan_code: Scan single file
- batch_scan: Scan multiple files
- get_config: Get backend configuration
- get_rules: List compliance rules
- health_check: Check backend status

Test connection:
```

Test MCP connection:
```
mcp__aivory__health_check
```

If successful:
```markdown
✓ MCP server connected successfully!
  Backend: http://localhost:8080
  Version: [version]
  Standards: 17 loaded
```

If failed:
```markdown
⚠️  Could not connect to backend at http://localhost:8080

To start the backend:
```bash
cd aivory-backend
../venv/Scripts/python app.py
```

You can continue setup and start the backend later.
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
- Backend running at http://localhost:8080
- Valid API key or user account

Register project now? (yes/no/later)
```

### 6.2 Register if Requested

If user agrees:

```bash
# Register project with backend
curl -X POST http://localhost:8080/api/v1/projects \
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
Dashboard: http://localhost:8080/dashboard/projects/abc-123-def

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
