# AIVory Guard - Claude Code Plugin

Comprehensive compliance validation plugin for Claude Code with support for 17+ compliance standards, AI-generated code detection, interactive remediation, and dashboard metrics.

**Part of the AIVory Guard compliance platform.** Visit [aivory.net](https://aivory.net) for the complete ecosystem including IntelliJ plugin, backend API, and MCP server.

## AIVory Guard Ecosystem

This Claude Code plugin works with other AIVory Guard components:

- **AIVory Guard MCP Server** - Required for compliance scanning
  - Install: `npx -y @aivorynet/guard`
  - NPM: [@aivorynet/guard](https://www.npmjs.com/package/@aivorynet/guard)

- **AIVory Guard Backend** - Optional for dashboard features
  - Download: [aivory.net](https://aivory.net)
  - Provides: Historical metrics, violation tracking, compliance scores

- **AIVory Guard IntelliJ Plugin** - IDE integration
  - Install: [JetBrains Marketplace](https://plugins.jetbrains.com) (search "AIVory Guard")
  - Provides: Real-time code validation in IntelliJ IDEA

All components can be used independently or together for comprehensive compliance coverage.

## Authentication & Limits

### Free Tier (Unauthenticated)
- **Standards Available**: OWASP Top 10 only
- **Rate Limits**: 15 files per scan, 3 scans per day
- **No registration required** - Start scanning immediately with OWASP compliance

### Authenticated Users
- **Standards Available**: All 17+ compliance standards (GDPR, HIPAA, PCI-DSS, SOC2, ISO27001, etc.)
- **No rate limits** - Unlimited scanning
- **Dashboard access** - Historical metrics and violation tracking
- **Team features** - Shared configurations and reporting

**Get authenticated access**: Register at [aivory.net/register/plans](https://aivory.net/register/plans) and add your API key during `/aivory-init` setup.

## Features

### Multi-Standard Compliance Scanning

Validate code against 17+ compliance standards:
- **Security**: OWASP Top 10
- **Healthcare**: HIPAA
- **Finance**: PCI-DSS, SOC 2, AML, DORA, Geldwäschegesetz
- **Privacy**: GDPR, CCPA, DSGVO
- **Information Security**: ISO 27001, ISO 27017, ISO 27018, TISAX
- **Regulatory**: NIS2, EU AI Act, Hinweisgeberschutzgesetz

### AI-Generated Code Detection

Unique capability to detect AI-generated code patterns for enhanced security review:
- Pattern-based detection with confidence scoring
- Flags AI code for stricter compliance validation
- Helps maintain audit trails for regulatory compliance
- Not punitive - ensures appropriate review rigor

### Interactive Remediation

Fix violations with AI-powered, compliance-aware suggestions:
- Multi-standard fix generation
- Before/after code comparison
- Verification through re-scanning
- Batch fix capabilities

### Dashboard Integration

View compliance metrics and trends:
- Violation hotspots
- Compliance scores by standard
- Historical trends
- Branch comparison
- Recent scan activity

## Commands

### `/aivory-init` - Project Setup

Initialize AIVory for your project with guided setup:
- Select compliance standards
- Configure MCP server
- Install git hooks (optional)
- Register with backend

```bash
/aivory-init
```

**Example Output:**
```
AIVory Guard Setup Complete

Standards: OWASP, GDPR, HIPAA, PCI-DSS
Confidence threshold: 80%
AI detection: Enabled
MCP server: Connected
Git hooks: Installed

Next steps:
1. /aivory-scan - Run first scan
2. /aivory-dashboard - View metrics
3. /aivory-fix - Fix violations
```

### `/aivory-scan` - Quick Compliance Scan

Scan files or directories for compliance violations:

```bash
# Scan current directory
/aivory-scan

# Scan specific file
/aivory-scan src/UserService.java

# Scan specific directory
/aivory-scan src/auth/

# Scan with specific standards (requires authentication for non-OWASP)
/aivory-scan --standards=OWASP,GDPR
```

**Limits**:
- **Unauthenticated**: OWASP only, max 15 files/scan, 3 scans/day
- **Authenticated**: All standards, unlimited scans

**Example Output:**
```
AIVory Compliance Scan Results
===============================

Scanned: 12 files
Total Violations: 8 (confidence ≥80)

By Standard:
- OWASP: 5 violations (3 critical, 2 high)
- GDPR: 2 violations (1 high, 1 medium)
- PCI-DSS: 1 violation (1 critical)

Top Violations:
--------------

1. [CRITICAL] OWASP-A03-01: SQL Injection
   File: UserService.java:142
   Confidence: 95%
   Remediation: Use parameterized queries

2. [HIGH] GDPR-32: Missing Data Encryption
   File: DatabaseConfig.java:78
   Confidence: 88%
   Remediation: Implement field-level encryption

Run /aivory-fix to remediate violations interactively.
```

### `/aivory-review` - Deep PR Review

Comprehensive PR compliance review with multi-standard analysis:

```bash
# Review current PR
/aivory-review

# Review specific PR
/aivory-review 123
```

**What it does:**
1. Validates PR eligibility (skips closed/draft/trivial)
2. Runs parallel compliance scan on changed files
3. Detects AI-generated code
4. Launches 3 specialized agents:
   - **compliance-scanner**: Multi-standard analysis
   - **ai-code-detector**: AI pattern detection
   - **compliance-architect**: Design review
5. Posts formatted review comment to GitHub

**Example Review Comment:**
```markdown
## AIVory Compliance Review

**PR #123**: Add user authentication
**Files Changed**: 8 files (+240, -15 lines)

### Compliance Summary

| Standard | Violations | Critical | High |
|----------|-----------|----------|------|
| OWASP    | 3         | 1        | 2    |
| GDPR     | 1         | 0        | 1    |
| **Total**| **4**     | **1**    | **3**|

### Critical Violations

#### 1. [OWASP-A02-02] Hardcoded Credentials
**File**: `AuthConfig.java:45`
**Confidence**: 92%

[Detailed description and fix...]

### AI-Generated Code Detection

**Files Flagged**: 1 file
- `PasswordUtil.java` (95% confidence)

Run /aivory-fix for interactive remediation.
```

**Better than `/code-review`:**
- 17+ compliance standards (not just code quality)
- AI-generated code detection
- Compliance scoring and trends
- Multi-standard awareness
- Dashboard integration

**Note**: Free tier scans OWASP only. Authenticate at [aivory.net/register/plans](https://aivory.net/register/plans) for access to all 17+ standards.

### `/aivory-fix` - Interactive Remediation

Fix compliance violations interactively:

```bash
/aivory-fix
```

**Interactive Workflow:**
1. Shows violations by severity
2. Generates compliance-aware fixes
3. Explains fix rationale
4. Shows before/after comparison
5. Asks for approval
6. Applies fix and verifies
7. Creates compliance commit

**Example Session:**
```
AIVory Violations Found
=======================

Critical: 3 violations
High: 5 violations

[1] CRITICAL - OWASP-A03-01: SQL Injection
    File: UserService.java:142

Fix #1: SQL Injection in UserService.java:142
========================================

Current Code:
```java
String query = "SELECT * FROM users WHERE email = '" + userEmail + "'";
```

Proposed Fix:
```java
String query = "SELECT * FROM users WHERE email = ?";
PreparedStatement pstmt = connection.prepareStatement(query);
pstmt.setString(1, userEmail);
```

Standards Satisfied: OWASP-A03, PCI-DSS-6.5.1

Apply this fix? (yes/no/edit): yes

Fix applied successfully.
Verification scan: No violations found.
```

### `/aivory-dashboard` - Compliance Metrics

View compliance metrics and trends:

```bash
/aivory-dashboard
```

**Example Output:**
```
AIVory Compliance Dashboard
===========================

Overall Compliance Score: 75/100 (Needs Improvement)
Trend: -5 points (last 7 days)

Compliance by Standard
=====================

Standard      Score   Violations   Trend
----------    -----   ----------   -----
OWASP         68/100  12          -8
GDPR          85/100   3          0
PCI-DSS       60/100   5          -12
SOC2          78/100   2          +5

Violation Hotspots
==================

1. src/UserService.java
   - 8 violations (3 critical)
   - OWASP: 5, PCI-DSS: 3

2. src/DatabaseConfig.java
   - 5 violations (1 critical)
   - GDPR: 3, ISO27001: 2

Recent Scans
============

2025-10-26  feature-auth  8 new violations
2025-10-25  main          2 fixed
```

## Specialized Agents

### compliance-scanner

Multi-standard compliance analysis agent:
- Checks code against 17+ standards
- Assigns confidence scores (0-100)
- Provides remediation guidance
- Cross-references standards

**Used by:** `/aivory-scan`, `/aivory-review`

### ai-code-detector

AI-generated code detection agent:
- Identifies AI-generated code patterns
- Confidence scoring
- Flags for enhanced review
- Transparent reporting

**Indicators:**
- Boilerplate-heavy code
- Generic variable naming
- Perfect code structure
- Tutorial-style patterns

**Used by:** `/aivory-review`

### security-remediator

Compliance-aware fix generation:
- Multi-standard fixes
- Safe remediation
- Before/after comparison
- Verification through re-scanning

**Used by:** `/aivory-fix`

### compliance-architect

Architecture and design review:
- Analyzes system design
- Identifies missing controls
- Pre-emptive recommendations
- Standards alignment

**Used by:** `/aivory-review`

## Git Hooks (Optional)

Automated compliance scanning at key points:

### Pre-commit Hook
- Scans staged files before commit
- Blocks commit if critical violations found
- Can bypass with `git commit --no-verify`

### Pre-push Hook
- Scans changed files before push
- Medium confidence threshold (less strict)
- Catches violations before remote

### Install Hooks

```bash
# Via /aivory-init wizard
/aivory-init

# Or manually enable in .aivory/config.yml
integration:
  git_hooks:
    pre_commit: true
    pre_push: true
```

## Installation

### Prerequisites

- Claude Code installed (`npm install -g @anthropic-ai/claude-code`)
- Git repository
- AIVory Guard MCP server (`npx -y @aivorynet/guard`)
- **Optional**: AIVory account for full access to 17+ standards ([aivory.net/register/plans](https://aivory.net/register/plans))
- **Optional**: AIVory backend for dashboard features

### Recommended: Install from AIVory Marketplace

```bash
# Add AIVory marketplace
/plugin marketplace add aivorynet/claude-marketplace

# Install AIVory compliance plugin
/plugin install aivory
```

### Alternative: Direct GitHub Installation

```bash
# Install directly from GitHub repository
/plugin marketplace add https://github.com/aivorynet/aivory-claude-plugin
/plugin install aivory
```

### For Development: Local Installation

```bash
# Install from local directory
/plugin marketplace add file:///path/to/aivory-claude-plugin
/plugin install aivory
```

### Verify Installation

```bash
# Check installed plugins
/plugin list

# Run initialization
/aivory-init

# Test scan
/aivory-scan
```

## Configuration

### Plugin Configuration (`.aivory/config.yml`)

Created by `/aivory-init`:

```yaml
compliance:
  enabled_standards:
    - OWASP
    - GDPR
    - HIPAA
    - PCI-DSS

  confidence_threshold: 80

  ai_detection:
    enabled: true

backend:
  url: http://localhost:8080
  api_key: ${AIVORY_API_KEY}

integration:
  git_hooks:
    pre_commit: false
    pre_push: true
```

### MCP Server Setup

AIVory plugin requires AIVory Guard MCP server:

```bash
# Without authentication (OWASP only, 15 files/scan, 3 scans/day)
claude mcp add \
  --transport stdio \
  aivory \
  -- npx -y @aivorynet/guard

# With authentication (all 17+ standards, unlimited)
# Register and get your API key from aivory.net/register/plans
claude mcp add \
  --transport stdio \
  --env AIVORY_API_KEY=your-api-key \
  --env AIVORY_SERVER_URL=http://localhost:8080 \
  aivory \
  -- npx -y @aivorynet/guard

# Verify
claude mcp list
```

## Use Cases

### Use Case 1: Pre-commit Compliance Check

```bash
# Developer makes changes
git add .

# Run compliance scan
/aivory-scan --staged-only

# Fix violations
/aivory-fix

# Commit with clean code
git commit -m "feat: Add user authentication"
```

### Use Case 2: Pull Request Review

```bash
# Create PR
gh pr create

# Run comprehensive review
/aivory-review

# Review finds violations
# Fix interactively
/aivory-fix

# Update PR
git push

# Re-review
/aivory-review
```

### Use Case 3: Compliance Audit

```bash
# View current compliance status
/aivory-dashboard

# Identify hotspots
# Fix top violations
/aivory-fix

# Track improvement
/aivory-dashboard
```

### Use Case 4: New Project Setup

```bash
# Initialize AIVory
/aivory-init

# Select standards: OWASP, GDPR, SOC2
# Enable AI detection: Yes
# Install git hooks: Yes

# Scan existing code
/aivory-scan

# Review architecture
/aivory-review --architecture-only

# Fix violations systematically
/aivory-fix
```

## Comparison with Other Plugins

### vs `/security-review`

| Feature | `/security-review` | `/aivory-review` |
|---------|-------------------|------------------|
| Standards | OWASP only | 17+ standards |
| AI Detection | No | Yes |
| Dashboard | No | Yes |
| Remediation | Manual | Interactive |
| Metrics | No | Yes |

### vs `/code-review`

| Feature | `/code-review` | `/aivory-review` |
|---------|---------------|------------------|
| Focus | Code quality | Compliance |
| Standards | None | 17+ standards |
| Agents | 3 (general) | 4 (specialized) |
| Scoring | Confidence | Compliance score |
| Tracking | No | Yes (dashboard) |

## Troubleshooting

### MCP Server Not Found

```bash
# Check MCP server status
claude mcp list

# Re-add if missing
claude mcp add aivory ...
```

### Backend Unavailable

The plugin requires AIVory Guard backend for dashboard features. The backend is available at:
- **Download**: [aivory.net/downloads](https://aivory.net)
- **Docker**: `docker pull aivory/guard-backend`
- **Source**: Available on [aivory.net](https://aivory.net)

Default backend URL: `http://localhost:8080`

To configure a different backend URL, edit `.aivory/config.yml`:
```yaml
backend:
  url: https://your-backend-url.com
```

### No Violations Found (Unexpected)

```bash
# Check configuration
cat .aivory/config.yml

# Verify MCP health
/aivory-dashboard

# Lower confidence threshold
# Edit .aivory/config.yml: confidence_threshold: 60
```

### Git Hooks Not Working

```bash
# Check hook installation
ls -la .git/hooks/

# Re-install via init
/aivory-init

# Or manually enable
chmod +x .git/hooks/pre-commit
```

## Documentation

- **Plugin README**: This file
- **Plugin Development**: `DEVELOPMENT.md` (in this repository)
- **AIVory Guard Platform**: [aivory.net/docs](https://aivory.net)
- **MCP Server** (`@aivorynet/guard`): [npmjs.com/package/@aivorynet/guard](https://www.npmjs.com/package/@aivorynet/guard)
- **IntelliJ Plugin**: [plugins.jetbrains.com](https://plugins.jetbrains.com) (search "AIVory Guard")
- **Backend API**: [aivory.net/api-docs](https://aivory.net)

## Support

- **Issues**: https://github.com/aivorynet/aivory-claude-plugin/issues
- **Website**: https://aivory.net
- **Email**: support@aivory.net

## License

MIT License - See LICENSE file for details

## Contributing

Contributions welcome. See CONTRIBUTING.md for guidelines.

---

© 2025 AIVory, Inc.
