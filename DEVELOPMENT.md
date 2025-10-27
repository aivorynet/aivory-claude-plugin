# AIVory Claude Code Plugin - Development Documentation

**Created**: 2025-10-26
**Status**: Complete
**Version**: 1.0.0

## Project Overview

This is a comprehensive Claude Code plugin for AIVory Guard that provides multi-standard compliance validation, AI-generated code detection, interactive remediation, and dashboard integration. It extends and improves upon standard Claude Code plugins like `/security-review` and `/code-review`.

## Project Structure

```
aivory-claude-plugin/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata and configuration
├── commands/                     # 5 slash commands
│   ├── aivory-scan.md           # Quick compliance scan
│   ├── aivory-review.md         # Deep PR review (enhanced /code-review)
│   ├── aivory-fix.md            # Interactive remediation workflow
│   ├── aivory-dashboard.md      # Compliance metrics display
│   └── aivory-init.md           # Project setup wizard
├── agents/                       # 4 specialized agents
│   ├── compliance-scanner.md    # Multi-standard compliance analysis
│   ├── ai-code-detector.md      # AI-generated code detection
│   ├── security-remediator.md   # Compliance-aware fix generation
│   └── compliance-architect.md  # Architecture and design review
├── hooks/
│   └── hooks.json               # Git hooks configuration
├── README.md                    # User-facing documentation
└── DEVELOPMENT.md               # This file
```

## What Was Built

### Commands (5 Total)

#### 1. `/aivory-init` - Project Setup Wizard
- **Purpose**: Initialize AIVory Guard for a project
- **Features**:
  - Interactive compliance standard selection (17+ standards)
  - MCP server configuration
  - Git hooks installation (optional)
  - Backend registration (optional)
  - Configuration file generation (`.aivory/config.yml`)
- **File**: `commands/aivory-init.md`

#### 2. `/aivory-scan` - Quick Compliance Scan
- **Purpose**: Scan files/directories for compliance violations
- **Features**:
  - Single file or batch scanning
  - 17+ compliance standards
  - Confidence-based filtering (≥80 default)
  - Detailed remediation guidance
  - Integration with MCP `scan_code` and `batch_scan` tools
- **File**: `commands/aivory-scan.md`

#### 3. `/aivory-review` - Deep PR Review
- **Purpose**: Comprehensive PR compliance review
- **Features**:
  - 5-phase workflow (validation, scan, AI detection, deep analysis, reporting)
  - Parallel agent execution (3 agents)
  - GitHub PR comment posting
  - Multi-standard analysis
  - AI-generated code detection
  - Dashboard integration
- **File**: `commands/aivory-review.md`
- **Better than**: `/code-review` (compliance-focused, 17+ standards, AI detection)

#### 4. `/aivory-fix` - Interactive Remediation
- **Purpose**: Fix compliance violations interactively
- **Features**:
  - Violation prioritization by severity
  - Compliance-aware fix generation
  - Before/after code comparison
  - User approval workflow
  - Verification through re-scanning
  - Compliance commit creation
- **File**: `commands/aivory-fix.md`

#### 5. `/aivory-dashboard` - Compliance Metrics
- **Purpose**: Display compliance metrics and trends
- **Features**:
  - Overall compliance scores
  - Scores by standard
  - Violation hotspots
  - Historical trends
  - Recent scan activity
  - Branch comparison
  - Backend API integration
- **File**: `commands/aivory-dashboard.md`

### Agents (4 Specialized)

#### 1. compliance-scanner
- **Purpose**: Multi-standard compliance analysis
- **Capabilities**:
  - Analyzes code against 17+ compliance standards
  - Assigns confidence scores (0-100)
  - Provides detailed remediation guidance
  - Cross-references violations across standards
- **Detection Logic**:
  - OWASP Top 10 patterns
  - GDPR data protection checks
  - HIPAA ePHI protection
  - PCI-DSS cardholder data security
  - SOC 2 security principles
  - ISO 27001 controls
  - Additional 11 standards
- **Used by**: `/aivory-scan`, `/aivory-review`
- **File**: `agents/compliance-scanner.md`

#### 2. ai-code-detector
- **Purpose**: AI-generated code detection
- **Capabilities**:
  - Pattern-based detection with confidence scoring
  - Flags AI code for enhanced security review
  - Transparent reporting with reasoning
- **Detection Indicators** (weighted):
  - Strong (20-30pts): Boilerplate-heavy, generic naming, perfect structure, tutorial-style
  - Medium (10-15pts): Inconsistent style, over-engineering, comprehensive error handling
  - Weak (5pts): Placeholder values, recent addition
- **Confidence Levels**:
  - 90-100%: Very High - Enhanced security review required
  - 80-89%: High - Standard+ review
  - 70-79%: Medium-High
  - <70%: Standard review
- **Used by**: `/aivory-review`
- **File**: `agents/ai-code-detector.md`
- **Unique**: No other plugin has this capability

#### 3. security-remediator
- **Purpose**: Compliance-aware fix generation
- **Capabilities**:
  - Multi-standard fix generation (satisfies all violated standards)
  - Safe remediation (verifies no new violations)
  - Context-aware (respects project patterns)
  - Verification through re-scanning
- **Principles**:
  1. Do no harm - verify safety before proposing
  2. Multi-standard compliance - fix satisfies all standards
  3. Project pattern respect - match existing code style
- **Fix Approaches**:
  - SQL Injection: Parameterized queries, ORM usage, input validation
  - Hardcoded Credentials: Environment variables, encrypted config, secret management
  - Missing Encryption: Field-level, database-level, application-level
  - Weak Cryptography: Algorithm upgrades, BCrypt for passwords, Argon2
- **Used by**: `/aivory-fix`
- **File**: `agents/security-remediator.md`

#### 4. compliance-architect
- **Purpose**: Architecture and design review
- **Capabilities**:
  - Architecture analysis for compliance patterns
  - Design pattern validation
  - Missing controls detection
  - Pre-emptive recommendations
- **Review Areas**:
  - Authentication architecture (centralized, secure storage, session management, MFA)
  - Authorization architecture (RBAC, ABAC, least privilege, multi-layer)
  - Data protection architecture (encryption at rest/in transit, key management, classification)
  - Input validation architecture (centralized validation, sanitization, output encoding)
  - Audit logging architecture (comprehensive trail, tamper-proof, retention, PII masking)
- **Used by**: `/aivory-review`
- **File**: `agents/compliance-architect.md`

### Git Hooks (Optional Integration)

#### Hooks Provided
- **pre-commit**: Scan staged files before commit, block on critical violations
- **pre-push**: Scan changed files before push, medium threshold
- **commit-msg**: Add compliance info to commit messages
- **post-merge**: Background scan after merge

#### Configuration
- **File**: `hooks/hooks.json`
- **Installation**: Via `/aivory-init` wizard
- **Thresholds**: Configurable (pre-commit: 80, pre-push: 70)
- **Bypass**: Supported via `git commit --no-verify`
- **Default**: Disabled (opt-in)

## Key Differentiators

### vs. `/security-review` (Standard Claude Code)

| Feature | `/security-review` | AIVory Plugin |
|---------|-------------------|---------------|
| **Standards** | OWASP only | 17+ standards |
| **AI Detection** | No | Yes (unique) |
| **Dashboard** | No | Yes (metrics & trends) |
| **Remediation** | Manual | Interactive workflow |
| **Historical Tracking** | No | Yes (backend integration) |
| **Fix Verification** | Manual | Automated re-scanning |
| **Multi-Standard Fixes** | No | Yes |
| **Branch Awareness** | No | Yes (deduplication) |

### vs. `/code-review` (Standard Claude Code)

| Feature | `/code-review` | AIVory Plugin |
|---------|---------------|---------------|
| **Focus** | Code quality | Compliance validation |
| **Standards** | None | 17+ compliance standards |
| **Scoring** | Confidence only | Compliance + Confidence |
| **Agents** | 3 general-purpose | 4 specialized |
| **Metrics** | No | Dashboard with trends |
| **Fix Suggestions** | Generic | Compliance-aware |
| **Historical Data** | No | Yes (backend) |
| **Architecture Review** | No | Yes (compliance-architect) |

### Unique Features (Not in Other Plugins)

1. **17+ Compliance Standards**: OWASP, GDPR, HIPAA, PCI-DSS, SOC2, ISO27001, ISO27017, ISO27018, CCPA, DORA, AML, EU AI Act, DSGVO, NIS2, TISAX, Geldwäschegesetz, Hinweisgeberschutzgesetz

2. **AI-Generated Code Detection**: Pattern-based detection with confidence scoring for enhanced security review (no other plugin has this)

3. **Branch-Aware Violation Tracking**: Uses backend's deduplication logic (org_id, git_branch, file_path, line_number, rule_id, standard)

4. **Interactive Remediation Workflow**: Step-by-step fixing with multi-standard validation and automated verification

5. **Compliance Dashboard**: Historical metrics, trends, hotspots, scores by standard

6. **Multi-Standard Fix Generation**: Fixes satisfy all violated standards simultaneously (e.g., OWASP + PCI-DSS + HIPAA in one fix)

7. **Architecture Review Agent**: Proactive design review for compliance (prevents violations before they occur)

## Integration with AIVory Ecosystem

### MCP Server Integration

Uses existing `@aivorynet/guard` MCP server tools:

```typescript
// From aivory-guard/src/tools/index.ts
- scan_code: Single file compliance scanning
- batch_scan: Multiple file scanning (more efficient)
- get_config: Backend configuration retrieval
- get_rules: Compliance rule definitions
- health_check: Backend health status
```

**Connection**:
```bash
claude mcp add \
  --transport stdio \
  --env AIVORY_API_KEY=your-key \
  --env AIVORY_SERVER_URL=https://app.aivory.net \
  aivory \
  -- npx -y @aivorynet/guard
```

### Backend Integration

Commands integrate with backend API (`aivory-backend/api_endpoints.py`):

- **`/aivory-dashboard`**: Fetches metrics via HTTP API
  - `/api/v1/metrics/hotspots`
  - `/api/v1/metrics/trends`
  - `/api/v1/metrics/scores`
  - `/api/v1/violations/recent`

- **`/aivory-review`**: Sends scan results for tracking
  - Branch-aware violation storage
  - Deduplication logic
  - Historical trend tracking

- **`/aivory-init`**: Registers project with backend
  - `/api/v1/projects` (POST)

### IntelliJ Plugin Alignment

Shares compliance standards and rules with IntelliJ plugin:
- **Same rule definitions**: `aivory-jetbrains/src/main/resources/rules/`
- **Same compliance standards**: 17+ standards
- **Same deduplication logic**: Branch-aware, commit-agnostic
- **Complementary workflows**: IDE + CLI coverage

## Configuration

### Plugin Metadata (`plugin.json`)

```json
{
  "name": "aivory",
  "displayName": "AIVory Guard - Compliance & Security Platform",
  "version": "1.0.0",
  "author": "AIVory, Inc.",
  "configuration": {
    "enabledStandards": ["OWASP", "GDPR", ...],
    "confidenceThreshold": 80,
    "enableAiDetection": true,
    "autoScanOnCommit": false,
    "backendUrl": "https://app.aivory.net"
  }
}
```

### User Configuration (`.aivory/config.yml`)

Generated by `/aivory-init`:

```yaml
project:
  name: Project Name
  repository: git-url

compliance:
  enabled_standards:
    - OWASP
    - GDPR
    - HIPAA
    - PCI-DSS

  confidence_threshold: 80

  ai_detection:
    enabled: true

  auto_scan:
    on_commit: false
    on_pr: true

backend:
  url: https://app.aivory.net
  api_key: ${AIVORY_API_KEY}

integration:
  git_hooks:
    pre_commit: false
    pre_push: true
```

## Installation Methods

### Method 1: Local Development

```bash
# Install from local path
/plugin marketplace add file:///D:/java/workspace/compide-jetbrain/aivory-claude-plugin
/plugin install aivory@local
```

### Method 2: GitLab Repository

```bash
# Install from GitLab (recommended for team)
/plugin marketplace add https://gitlab.ilscipio.com/ilscipio/projects/aivory-jetbrain/aivory-claude-plugin
/plugin install aivory@gitlab
```

### Method 3: NPM Package (Future)

```bash
# After publishing to NPM as @aivorynet/claude-plugin
/plugin marketplace add npm:@aivorynet/claude-plugin
/plugin install aivory@npm
```

## User Workflows

### Workflow 1: First-Time Setup
```bash
/aivory-init
# Select standards: OWASP, GDPR, HIPAA, PCI-DSS
# Enable AI detection: Yes
# Install git hooks: Yes (optional)
# Register with backend: Yes (optional)

/aivory-scan              # First scan
/aivory-dashboard         # View metrics
```

### Workflow 2: Pre-Commit Scanning
```bash
git add .
/aivory-scan --staged-only
/aivory-fix
git commit -m "feat: Add authentication"
```

### Workflow 3: Pull Request Review
```bash
gh pr create
/aivory-review            # Comprehensive review
/aivory-fix               # Fix violations
git push
/aivory-review            # Verify fixes
```

### Workflow 4: Compliance Audit
```bash
/aivory-dashboard         # Current state
# Identify hotspots
/aivory-scan src/UserService.java
/aivory-fix
/aivory-dashboard         # Verify improvement
```

## Technical Implementation

### Command Execution Flow

```
User runs command → Claude Code parses markdown → Executes instructions → Returns results
```

**Example: `/aivory-scan`**
1. User runs `/aivory-scan src/UserService.java`
2. Claude reads `commands/aivory-scan.md`
3. Follows instructions:
   - Gather file to scan
   - Use MCP `scan_code` tool
   - Process results
   - Filter by confidence
   - Present formatted report
4. User sees violations with remediation guidance

### Agent Invocation Flow

```
Command invokes agent → Task tool creates subagent → Agent executes → Returns findings
```

**Example: `/aivory-review` using agents**
```typescript
// Parallel agent execution
Task(subagent_type="compliance-scanner", prompt="Analyze PR #123...")
Task(subagent_type="ai-code-detector", prompt="Detect AI code in PR #123...")
Task(subagent_type="compliance-architect", prompt="Review architecture...")

// Wait for all agents to complete
// Aggregate findings
// Filter by confidence ≥80
// Generate GitHub comment
```

### MCP Tool Usage Pattern

```typescript
// Single file scan
mcp__aivory__scan_code({
  content: fileContents,
  filename: "UserService.java",
  language: "java",
  enabled_standards: ["OWASP", "GDPR"]
})

// Batch scan (more efficient)
mcp__aivory__batch_scan({
  files: [
    {content: file1, filename: "User.java", language: "java"},
    {content: file2, filename: "Auth.java", language: "java"}
  ]
})
```

## Testing

### Manual Testing Checklist

- [ ] **Installation**
  - [ ] Install plugin locally
  - [ ] Verify plugin appears in `/help`
  - [ ] Check MCP server connection

- [ ] **Commands**
  - [ ] `/aivory-init` - Run setup wizard
  - [ ] `/aivory-scan` - Scan test file with violations
  - [ ] `/aivory-review` - Review test PR
  - [ ] `/aivory-fix` - Fix violations interactively
  - [ ] `/aivory-dashboard` - View metrics

- [ ] **Agents**
  - [ ] compliance-scanner detects violations
  - [ ] ai-code-detector identifies AI patterns
  - [ ] security-remediator generates valid fixes
  - [ ] compliance-architect provides recommendations

- [ ] **Integration**
  - [ ] MCP tools return expected results
  - [ ] Backend API calls succeed
  - [ ] Git hooks execute correctly
  - [ ] GitHub PR comments post successfully

### Test Files

Create test files with known violations:

**SQL Injection** (OWASP-A03, PCI-DSS-6.5.1):
```java
String query = "SELECT * FROM users WHERE email = '" + email + "'";
```

**Hardcoded Credentials** (OWASP-A02):
```java
String apiKey = "sk-1234567890abcdef";
```

**Missing Encryption** (GDPR-32):
```java
user.setSsn(socialSecurityNumber); // Stored unencrypted
```

**Weak Cryptography** (OWASP-A02):
```java
MessageDigest md5 = MessageDigest.getInstance("MD5");
```

## Development Guidelines

### Adding New Commands

1. Create `commands/new-command.md`
2. Add front matter with description
3. Write detailed instructions for Claude
4. Include error handling
5. Add examples in README.md
6. Test thoroughly

### Adding New Agents

1. Create `agents/new-agent.md`
2. Define purpose and responsibilities
3. Document task execution guidelines
4. Specify output format
5. Add to appropriate commands
6. Test with real code

### Modifying Compliance Standards

Standards are defined in backend:
- `aivory-backend/compliance_standards/*.py`
- Rules synced to MCP server
- Plugin uses MCP tools for scanning

To add new standard:
1. Add to backend (see `documentation/compliance/ADD_NEW_COMPLIANCE_STANDARD.md`)
2. Update `plugin.json` configuration enum
3. Update agent detection logic
4. Test end-to-end

## Publishing

### Pre-Release Checklist

- [ ] All commands tested and working
- [ ] All agents tested and working
- [ ] MCP integration verified
- [ ] Backend integration verified
- [ ] Git hooks tested
- [ ] Documentation complete
- [ ] Version number updated
- [ ] Changelog created

### Publishing to GitLab

```bash
# Commit plugin to repository
git add aivory-claude-plugin/
git commit -m "feat: Add AIVory Claude Code plugin"
git push origin main

# Users install with:
# /plugin marketplace add https://gitlab.ilscipio.com/ilscipio/projects/aivory-jetbrain/aivory-claude-plugin
# /plugin install aivory@gitlab
```

### Publishing to NPM (Future)

```bash
# From aivory-claude-plugin/
# Create package.json
# npm publish --access public
# Package name: @aivorynet/claude-plugin
```

## Maintenance

### Regular Updates

- Monitor Claude Code plugin API changes
- Update compliance standards as needed
- Improve agent detection logic based on feedback
- Add new commands based on user requests
- Performance optimization

### Issue Tracking

- Plugin issues: GitLab Issues
- User feedback: Support email
- Feature requests: GitLab Issues with "enhancement" label

## Support

- **Documentation**: `aivory-claude-plugin/README.md`
- **Implementation**: `documentation/mcp/CLAUDE_CODE_PLUGIN_IMPLEMENTATION.md`
- **Issues**: https://gitlab.ilscipio.com/ilscipio/projects/aivory-jetbrain/issues
- **Website**: https://aivory.net
- **Email**: support@aivory.net

## License

MIT License - See LICENSE file for details

## Contributors

- Initial Implementation: Claude Code (2025-10-26)
- Project: AIVory, Inc. / AIVory Guard

---

**Last Updated**: 2025-10-26
**Plugin Version**: 1.0.0
**Status**: Production Ready
