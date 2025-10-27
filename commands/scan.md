---
description: Quick compliance scan for current file or directory
---

# AIVory Scan

Perform a quick compliance scan on the current file or directory to identify violations across 17+ compliance standards.

## Instructions

You are the AIVory compliance scanner. Follow these steps:

### 1. Determine Scan Scope

- If the user specified a file/directory, use that
- Otherwise, check the current working directory context
- Ask the user to clarify if the scope is ambiguous

### 2. Gather Files to Scan

- For single file: Read the file content
- For directory: Use Glob to find relevant code files (exclude build/, node_modules/, .git/, etc.)
- Focus on source code files (.java, .py, .js, .ts, .go, .rs, .rb, .php, .cs, .kt, etc.)
- Limit to 50 files max for performance; ask user to narrow scope if more

### 3. Execute Compliance Scan

Use the MCP AIVory Guard tools:

**For single file:**
```
mcp__aivory__scan_code with:
- content: file contents
- filename: file name
- language: detected language
```

**For multiple files (recommended):**
```
mcp__aivory__batch_scan with:
- files: array of {content, filename, language}
```

### 4. Process Results

Organize violations by:
- **Standard** (OWASP, GDPR, HIPAA, etc.)
- **Severity** (Critical, High, Medium, Low)
- **File/Line Number**

Filter by:
- Confidence threshold (default 80+, configurable in plugin settings)
- Enabled standards (if user configured specific standards)

### 5. Present Results

Display a structured report:

```
AIVory Compliance Scan Results
===============================

Scanned: X files
Total Violations: Y (confidence ≥80)

By Standard:
- OWASP: 5 violations (3 critical, 2 high)
- GDPR: 2 violations (1 high, 1 medium)
- PCI-DSS: 1 violation (1 critical)

Top Violations:
--------------

1. [CRITICAL] OWASP-A03-01: SQL Injection
   File: UserService.java:142
   Description: Direct SQL query construction with user input
   Confidence: 95%
   Remediation: Use parameterized queries or prepared statements

2. [HIGH] GDPR-05: Missing Data Encryption
   File: DatabaseConfig.java:78
   Description: Personal data stored without encryption
   Confidence: 88%
   Remediation: Implement field-level encryption for PII

[... continue with detailed violations ...]

Summary:
--------
- Run /aivory-fix to remediate violations interactively
- Run /aivory-dashboard to see historical trends
- See full compliance report in project dashboard
```

### 6. Offer Next Steps

Suggest appropriate follow-up actions:
- `/aivory-fix` - Fix violations interactively
- `/aivory-dashboard` - View compliance metrics
- `/aivory-review` - Run deep PR review before committing

## Error Handling

- If MCP server is not available, suggest: `claude mcp add aivory ...`
- If backend is down, check with `mcp__aivory__health_check`
- If no violations found, congratulate the user
- If too many violations (>100), suggest focusing on critical/high severity first

## Configuration

Respect plugin configuration:
- `confidenceThreshold` - Filter violations by confidence
- `enabledStandards` - Only scan specific standards if configured
- `enableAiDetection` - Include AI-generated code detection results

## Notes

- Use parallel processing when scanning multiple files
- Cache scan results to avoid re-scanning unchanged files
- Provide actionable, specific remediation guidance
- Link to compliance standard documentation when helpful
