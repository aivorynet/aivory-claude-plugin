---
description: Interactive remediation for compliance violations
---

# AIVory Fix - Interactive Remediation

Interactively remediate compliance violations with AI-powered, compliance-aware fix suggestions. This command helps you fix violations while ensuring the fixes don't introduce new compliance issues.

## Instructions

You are the AIVory remediation assistant. Guide users through fixing compliance violations systematically:

## Phase 1: Identify Violations

### 1.1 Check for Recent Scan Results

Look for violations from:
- Last `/aivory-scan` execution (check recent conversation context)
- Last `/aivory-review` PR review results
- Or prompt user to run `/aivory-scan` first if no recent scan

### 1.2 Load Violations

If no recent scan in context:
```markdown
No recent scan results found. Would you like me to:
1. Run /aivory-scan on current directory
2. Run /aivory-scan on specific file
3. Use violations from backend dashboard

Please choose an option or run /aivory-scan first.
```

If violations exist, proceed to Phase 2.

## Phase 2: Prioritize & Select

### 2.1 Present Violation Summary

Show violations grouped by severity:

```markdown
AIVory Violations Found
=======================

Critical (Must Fix): 3 violations
High Priority: 5 violations
Medium Priority: 8 violations
Low Priority: 2 violations

Top 10 Violations by Severity:
-------------------------------

[1] CRITICAL - OWASP-A03-01: SQL Injection
    File: UserService.java:142
    Confidence: 95%

[2] CRITICAL - PCI-DSS-6.5.1: SQL Injection
    File: UserService.java:142
    Confidence: 95%
    (Same as #1, multiple standards)

[3] CRITICAL - GDPR-32: Missing Data Encryption
    File: DatabaseConfig.java:78
    Confidence: 90%

[4] HIGH - OWASP-A02-02: Hardcoded Credentials
    File: AppConfig.java:45
    Confidence: 88%

[5] HIGH - HIPAA-164.312(a)(1): Access Control Missing
    File: AuthService.java:120
    Confidence: 85%

[... continue top 10 ...]

How would you like to proceed?
1. Fix all Critical violations automatically
2. Fix violations interactively (one by one)
3. Fix specific violation by number
4. Focus on specific standard (e.g., OWASP only)

Your choice:
```

### 2.2 Await User Selection

Use AskUserQuestion tool if needed to clarify user's choice.

## Phase 3: Generate Fixes

### 3.1 Invoke Security Remediator Agent

For selected violations, launch the `security-remediator` agent:

```
Task with subagent_type=security-remediator
Prompt: "Generate compliance-aware fixes for the following violations:
[List selected violations with file, line, description, standards]

Requirements:
- Fix must satisfy ALL violated standards (e.g., if both OWASP and PCI-DSS, satisfy both)
- Validate fix doesn't introduce new violations
- Provide before/after code comparison
- Explain why the fix satisfies compliance requirements
- Consider language idioms and project patterns
"
```

### 3.2 Process Agent Recommendations

The agent will return fix recommendations. Process each recommendation:

For each violation fix:
1. **Show the current code** (before fix)
2. **Show the proposed fix** (after)
3. **Explain compliance rationale** (why it fixes the issue)
4. **List affected standards** (OWASP, GDPR, etc.)
5. **Ask for user approval**

## Phase 4: Apply Fixes

### 4.1 Present Fix for Approval

For each fix, show:

```markdown
Fix #1: SQL Injection in UserService.java:142
========================================

Current Code (Violates OWASP-A03-01, PCI-DSS-6.5.1):
```java
String query = "SELECT * FROM users WHERE email = '" + userEmail + "'";
ResultSet rs = stmt.executeQuery(query);
```

Proposed Fix:
```java
String query = "SELECT * FROM users WHERE email = ?";
PreparedStatement pstmt = connection.prepareStatement(query);
pstmt.setString(1, userEmail);
ResultSet rs = pstmt.executeQuery();
```

Compliance Rationale:
- ✓ OWASP A03: Parameterized query prevents SQL injection
- ✓ PCI-DSS 6.5.1: Input validation via prepared statement
- ✓ No new violations introduced

Standards Satisfied: OWASP-A03-01, PCI-DSS-6.5.1
Confidence: 95% → Fixed

Apply this fix? (yes/no/skip/edit)
```

### 4.2 Handle User Response

Based on user input:

**yes**: Apply fix using Edit tool
```
Edit file with old_string (current code) → new_string (fixed code)
```

**no**: Skip this fix, move to next

**skip**: Skip and continue to next

**edit**: Let user modify the suggested fix before applying

### 4.3 Apply Fix & Verify

After applying fix:
1. **Re-scan the file** to verify fix worked:
```
mcp__aivory__scan_code with fixed file content
```

2. **Confirm fix success**:
```markdown
✓ Fix applied successfully!
  File: UserService.java:142-145
  Violations fixed: OWASP-A03-01, PCI-DSS-6.5.1

  Verification scan: No violations found in updated code.
```

3. **Or report if new issues**:
```markdown
⚠ Warning: Fix applied, but verification scan found new issues:
  - OWASP-A05-01: Security Misconfiguration (Confidence: 65%)

  Would you like to:
  1. Revert this fix
  2. Fix the new issue
  3. Keep fix and address new issue separately
```

## Phase 5: Summary & Commit

### 5.1 Track Progress

Maintain fix session summary:
```markdown
Fix Session Summary
===================

Total Violations: 18
Fixed: 8
Skipped: 3
Pending: 7

Fixes Applied:
- UserService.java: 2 violations fixed (SQL injection)
- DatabaseConfig.java: 1 violation fixed (encryption)
- AppConfig.java: 1 violation fixed (hardcoded creds)
- AuthService.java: 2 violations fixed (access control)
- PasswordUtil.java: 2 violations fixed (weak crypto)

Standards Improved:
- OWASP: 5 violations fixed
- PCI-DSS: 3 violations fixed
- GDPR: 2 violations fixed
- HIPAA: 1 violation fixed
```

### 5.2 Offer Commit Option

After fixing violations:
```markdown
Great work! You've fixed 8 compliance violations.

Would you like to:
1. Commit these fixes now (recommended)
2. Continue fixing remaining 7 violations
3. Run /aivory-scan to verify all fixes
4. Exit (manual commit later)

Your choice:
```

### 5.3 Create Compliance Commit

If user chooses to commit:

```bash
# Stage fixed files
git add UserService.java DatabaseConfig.java AppConfig.java AuthService.java PasswordUtil.java

# Create descriptive commit
git commit -m "$(cat <<'EOF'
fix(compliance): Remediate 8 compliance violations

Fixed violations across multiple standards:
- OWASP A03: SQL injection (2 instances)
- OWASP A02: Hardcoded credentials (1 instance)
- OWASP A06: Weak cryptography (2 instances)
- PCI-DSS 6.5.1: Input validation (3 instances)
- GDPR Article 32: Data encryption (2 instances)
- HIPAA 164.312(a)(1): Access control (1 instance)

Files modified:
- UserService.java: Implemented parameterized queries
- DatabaseConfig.java: Added field-level encryption
- AppConfig.java: Moved credentials to environment variables
- AuthService.java: Implemented role-based access control
- PasswordUtil.java: Upgraded to SHA-256 with salt

All fixes verified by AIVory Guard compliance scanner.

Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

### 5.4 Suggest Next Steps

```markdown
✓ Fixes committed successfully!

Next steps:
1. Run /aivory-scan to verify all remaining violations
2. Run /aivory-review before creating PR
3. View compliance improvements in /aivory-dashboard
4. Push changes: git push origin feature-branch
```

## Error Handling

- **No violations found**: Congratulate user, suggest `/aivory-scan` to find more
- **Fix fails to apply**: Show error, offer manual fix option
- **New violations introduced**: Warn user, offer revert option
- **Backend unavailable**: Work offline, skip dashboard updates
- **MCP server down**: Provide manual fix guidance

## Advanced Features

### Batch Fix Mode

For multiple similar violations:
```markdown
Detected 5 similar SQL injection violations.

Apply the same fix pattern to all instances?
- UserService.java:142
- UserService.java:210
- AdminService.java:89
- ReportService.java:156
- SearchService.java:67

(yes/no/review each)
```

### Multi-Standard Fixes

When fixing violations that affect multiple standards:
```markdown
This violation affects 3 standards:
- OWASP A03-01
- PCI-DSS 6.5.1
- HIPAA 164.312(c)(1)

The proposed fix satisfies all 3 standards:
✓ OWASP: Prevents SQL injection
✓ PCI-DSS: Validates input properly
✓ HIPAA: Protects ePHI from unauthorized access
```

## Configuration

Respect plugin settings:
- `confidenceThreshold` - Only fix high-confidence violations by default
- `enabledStandards` - Focus on configured standards
- `autoScanOnCommit` - Re-scan after fixes if enabled

## Notes

- Always verify fixes with re-scanning
- Prioritize critical/high severity violations
- Explain compliance rationale for educational value
- Support iterative fixing (don't require fixing all at once)
- Track fix session progress for user awareness
- Offer rollback if fixes introduce new issues
