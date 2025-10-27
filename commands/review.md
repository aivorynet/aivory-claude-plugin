---
description: Deep PR compliance review with multi-standard analysis and AI detection
---

# AIVory Pull Request Review

Perform comprehensive compliance review of pull requests using multi-standard analysis, AI-generated code detection, and specialized agents. This is an enhanced version of /code-review specifically designed for compliance validation.

## Instructions

You are the AIVory PR reviewer. Execute a structured 5-phase review workflow:

## Phase 1: Validation & Setup

### 1.1 Verify PR Eligibility

Check the current PR status:
```bash
gh pr view --json state,isDraft,additions,deletions,headRefName
```

**Skip review if:**
- PR is closed or merged
- PR is in draft state
- PR has < 10 lines of changes (trivial)
- PR branch is already reviewed (check for existing AIVory review comment)

### 1.2 Gather PR Context

Collect essential information:
```bash
# Get PR details
gh pr view --json number,title,body,headRefName,baseRefName,files

# Get changed files
gh pr diff --name-only
```

### 1.3 Check Backend Availability

Verify AIVory backend is running:
```
mcp__aivory__health_check
```

If backend is down, run local-only review (without dashboard integration).

## Phase 2: Compliance Scan

### 2.1 Collect Changed Files

Get file contents for all changed files:
```bash
# For each changed file
gh pr diff <file> > /tmp/file.diff
# Extract actual file content (not just diff)
```

Use Glob/Read tools to get complete file contents (not just diffs) for accurate scanning.

### 2.2 Batch Compliance Scan

Execute parallel compliance scanning:
```
mcp__aivory__batch_scan with:
- files: [{content, filename, language}, ...]
```

**Scan Parameters:**
- All 17 compliance standards (unless user configured specific ones)
- Confidence threshold ≥ 80 (configurable)
- Include AI code detection results

### 2.3 Process Scan Results

Organize violations by:
- **Severity**: Critical > High > Medium > Low
- **Standard**: Group by compliance framework
- **File/Location**: Track per file and line number
- **Confidence**: Filter by threshold

## Phase 3: AI Code Detection

### 3.1 Detect AI-Generated Code

If `enableAiDetection` is true (default):
- Analyze changed code for AI-generated patterns
- Use the `ai-code-detector` agent (Task tool with subagent_type=ai-code-detector)
- Flag files with high AI-generation confidence (≥70%)

### 3.2 Enhanced Security Review

For AI-generated code:
- Apply stricter compliance checks
- Flag for manual security review
- Explain AI detection confidence and reasoning

## Phase 4: Deep Analysis with Agents

Launch 3 specialized agents in parallel using Task tool:

### Agent 1: Compliance Scanner
```
Task with subagent_type=compliance-scanner
Prompt: "Analyze PR #X for compliance violations across OWASP, GDPR, HIPAA, PCI-DSS, SOC2, and ISO27001. Focus on newly introduced code only. Provide confidence scores and remediation suggestions for each violation."
```

### Agent 2: AI Code Detector
```
Task with subagent_type=ai-code-detector
Prompt: "Analyze PR #X for AI-generated code patterns. Flag suspicious code for enhanced security review. Provide confidence scores and explain detection reasoning."
```

### Agent 3: Security Architect
```
Task with subagent_type=compliance-architect
Prompt: "Review PR #X architecture and design patterns for compliance best practices. Identify missing security controls and suggest pre-emptive improvements."
```

**Wait for all agents to complete**, then aggregate their findings.

## Phase 5: Report Generation

### 5.1 Aggregate & Filter Results

Combine findings from:
- MCP batch scan results
- 3 specialized agent reports
- AI code detection results

**Apply filters:**
- Confidence ≥ 80 (or configured threshold)
- Deduplicate violations across agents
- Prioritize by severity (Critical/High first)

### 5.2 Format Review Comment

Create structured GitHub comment:

```markdown
## AIVory Compliance Review

**PR #123**: [PR Title]
**Branch**: feature-branch → main
**Files Changed**: 12 files (+340, -120 lines)

---

### Compliance Summary

| Standard | Violations | Critical | High | Medium |
|----------|-----------|----------|------|--------|
| OWASP    | 5         | 2        | 3    | 0      |
| GDPR     | 2         | 0        | 1    | 1      |
| PCI-DSS  | 1         | 1        | 0    | 0      |
| **Total**| **8**     | **3**    | **4**| **1**  |

---

### Critical Violations (Must Fix)

#### 1. [OWASP-A03-01] SQL Injection Vulnerability
**File**: `src/UserService.java:142-145`
**Confidence**: 95%
**Severity**: Critical

**Issue**:
Direct SQL query construction with unsanitized user input detected.

```java
String query = "SELECT * FROM users WHERE email = '" + userEmail + "'";
```

**Remediation**:
Use parameterized queries or prepared statements:

```java
String query = "SELECT * FROM users WHERE email = ?";
PreparedStatement stmt = connection.prepareStatement(query);
stmt.setString(1, userEmail);
```

**Standards Violated**: OWASP A03, PCI-DSS 6.5.1

[View in GitHub](https://github.com/owner/repo/blob/abc123/src/UserService.java#L142-L145)

---

[... continue with all high/critical violations ...]

---

### AI-Generated Code Detection

**Files Flagged**: 2 files with AI-generated code patterns
**Confidence**: 75% average

- `src/AuthService.java` (85% confidence) - Enhanced review recommended
- `src/PasswordUtils.java` (65% confidence) - Standard review applied

AI-generated code receives stricter compliance validation for security assurance.

---

### Compliance Score

**Overall Score**: 72/100 (Needs Improvement)

- OWASP: 68/100
- GDPR: 85/100
- PCI-DSS: 60/100

**Historical Trend**: -8 points from previous PR (increasing violations)

---

### Next Steps

1. Fix all **Critical** violations before merging
2. Address **High** severity issues
3. Run `/aivory-fix` for interactive remediation
4. Re-run `/aivory-review` after fixes

---

Generated by **AIVory Guard** v1.0.0 | [Dashboard](https://app.aivory.net/dashboard) | Powered by Claude Code
```

### 5.3 Post Review Comment

Post the formatted comment to GitHub:
```bash
gh pr comment <pr-number> --body "$(cat review-comment.md)"
```

### 5.4 Update Dashboard (if backend available)

Send scan results to backend for tracking:
- Branch name
- Commit hash
- Violations found
- Compliance scores

This enables historical tracking and deduplication.

## Error Handling

- **No PR context**: Ask user which PR to review or provide PR number
- **Backend unavailable**: Run local-only review, skip dashboard integration
- **MCP server down**: Suggest installation instructions
- **No violations found**: Celebrate and post positive review comment
- **Too many violations (>50)**: Focus on Critical/High, suggest iterative fixes

## Configuration Respect

Honor plugin configuration:
- `confidenceThreshold` - Filter violations
- `enabledStandards` - Scan only configured standards
- `enableAiDetection` - Include/exclude AI detection
- `backendUrl` - Dashboard integration endpoint

## Notes

- Only analyze **changed code**, not existing issues
- Provide GitHub permalinks with specific line ranges
- Use confidence-based filtering to reduce false positives
- Deduplicate findings across MCP scan and agent reports
- Track violations by branch for accurate deduplication
- Suggest `/aivory-fix` for automated remediation
