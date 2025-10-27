---
description: View compliance metrics, trends, and violation analytics
---

# AIVory Dashboard - Compliance Metrics

Display comprehensive compliance metrics, violation trends, hotspots, and project health from the AIVory backend dashboard. This provides visibility into compliance posture over time.

## Instructions

You are the AIVory dashboard reporter. Fetch and display compliance metrics from the backend:

## Phase 1: Check Backend Availability

### 1.1 Verify Backend Connection

Check if AIVory backend is available:
```
mcp__aivory__health_check
```

If backend is down:
```markdown
AIVory Dashboard Unavailable
============================

The AIVory backend is not currently accessible.

To start the backend:
```bash
cd aivory-backend
../venv/Scripts/python app.py
```

Or check the configured backend URL:
- Current: [backendUrl from config, default https://app.aivory.net]
- Update in: aivory-claude-plugin/.claude-plugin/plugin.json

Once backend is running, try /aivory-dashboard again.
```

Return early if backend unavailable.

### 1.2 Get Current Configuration

Fetch backend configuration:
```
mcp__aivory__get_config
```

Display enabled standards and AI detection status.

## Phase 2: Fetch Metrics

### 2.1 Determine Metrics Scope

Ask user for scope (if not specified):
- **Project-wide metrics** (default): All violations across all branches
- **Branch-specific metrics**: Current branch only
- **Time-based metrics**: Last 7 days, 30 days, 90 days
- **Standard-specific metrics**: Focus on one compliance standard

Use AskUserQuestion if clarification needed.

### 2.2 Fetch Dashboard Data

Since we don't have direct MCP tools for metrics (yet), use backend HTTP API via Bash:

```bash
# Get project metrics (requires authentication if backend has it)
curl -s https://app.aivory.net/api/v1/metrics/hotspots

curl -s https://app.aivory.net/api/v1/metrics/trends

curl -s https://app.aivory.net/api/v1/metrics/scores

curl -s https://app.aivory.net/api/v1/violations/recent

curl -s https://app.aivory.net/api/v1/projects
```

**Note**: Adapt these endpoint calls based on actual backend API structure. Check `aivory-backend/api_endpoints.py` for exact routes.

## Phase 3: Process & Display Metrics

### 3.1 Overall Compliance Score

Display project compliance summary:

```markdown
AIVory Compliance Dashboard
===========================

Project: [Project Name]
Branch: [Current Branch or "All Branches"]
Last Scan: [Timestamp]
Backend: https://app.aivory.net

Overall Compliance Score: 75/100 ⚠️
Trend: ↓ -5 points (last 7 days)

┌──────────────────────────────────────────┐
│ Compliance Rating: NEEDS IMPROVEMENT     │
│ Status: 23 Active Violations             │
│ Critical Issues: 4                       │
└──────────────────────────────────────────┘
```

### 3.2 Score by Standard

Break down compliance by standard:

```markdown
Compliance by Standard
=====================

Standard      Score   Violations   Trend
----------    -----   ----------   -----
OWASP         68/100  12          ↓ -8
GDPR          85/100   3          → 0
HIPAA         N/A     0           -
PCI-DSS       60/100   5          ↓ -12
SOC2          78/100   2          ↑ +5
ISO 27001     82/100   1          ↑ +3
CCPA          90/100   0          ↑ +5
DORA          N/A     0           -
Other (10)    N/A     0           -

Top Priority: PCI-DSS (largest recent decline)
Best Performer: CCPA (90/100)
Needs Attention: OWASP, PCI-DSS
```

### 3.3 Violation Hotspots

Identify files/areas with most violations:

```markdown
Violation Hotspots
==================

Top 10 Files by Violation Count:

1. src/UserService.java
   - 8 violations (3 critical, 5 high)
   - OWASP: 5, PCI-DSS: 3
   - Last updated: 2 days ago
   [Run /aivory-scan on this file]

2. src/DatabaseConfig.java
   - 5 violations (1 critical, 3 high, 1 medium)
   - GDPR: 3, ISO27001: 2
   - Last updated: 5 days ago

3. src/AuthService.java
   - 4 violations (0 critical, 2 high, 2 medium)
   - OWASP: 2, HIPAA: 1, SOC2: 1
   - Last updated: 1 day ago

[... continue top 10 ...]

Top Violation Types:
- SQL Injection (OWASP-A03): 5 instances
- Missing Encryption (GDPR-32): 4 instances
- Weak Crypto (OWASP-A02): 3 instances
- Hardcoded Credentials (OWASP-A02-02): 2 instances
- Missing Access Control (HIPAA-164.312): 2 instances
```

### 3.4 Recent Scans

Show recent scan activity:

```markdown
Recent Scan Activity
====================

Date        Branch          Files   Violations   Standards
----------  -------------   -----   ----------   ---------
2025-10-26  feature-auth    12      8 new        OWASP, PCI-DSS
2025-10-25  feature-auth    10      3 new        GDPR
2025-10-24  main            45      0 new        All
2025-10-23  feature-api     8       5 new        OWASP, SOC2
2025-10-22  main            40      2 fixed      OWASP

Latest scan detected 8 new violations in feature-auth branch.
Run /aivory-fix to remediate.
```

### 3.5 Violation Trends

Visualize trends over time (using ASCII charts):

```markdown
Violation Trends (Last 30 Days)
================================

Total Violations:
30 │
   │     ●
25 │    ● ●
   │   ●   ●
20 │  ●     ●
   │ ●       ●
15 │●         ●
   │           ●
10 │            ●
   └─────────────────────────────
    Oct 1     Oct 15    Oct 26

New vs Fixed Violations:
- New: 18 violations
- Fixed: 12 violations
- Net: +6 violations (increasing)

Trend: Violations increasing over last 30 days
Action: Prioritize fixing critical violations
```

### 3.6 Branch Comparison

Compare violations across branches:

```markdown
Branch Comparison
=================

Branch          Violations   Critical   Status
--------------  ----------   --------   ------
main            5            1          ✓ Baseline
feature-auth    13           3          ⚠️ +8 new
feature-api     8            2          ⚠️ +3 new
bugfix-login    2            0          ✓ -3 fixed

Recommendation:
- Feature-auth branch needs review before merging
- Run /aivory-review on feature-auth PR
```

### 3.7 AI-Generated Code Stats

Show AI detection metrics (if enabled):

```markdown
AI-Generated Code Detection
============================

Files with AI-generated code: 8 files
Total AI-generated lines: ~450 lines
Average confidence: 78%

Files flagged for enhanced review:
- src/PasswordUtil.java (95% confidence)
- src/CryptoHelper.java (88% confidence)
- src/JwtTokenService.java (82% confidence)

AI-generated code receives stricter compliance validation.
All flagged files have been scanned for security issues.
```

## Phase 4: Actionable Insights

### 4.1 Provide Recommendations

Based on metrics, suggest actions:

```markdown
Recommended Actions
===================

Priority 1 (Critical):
1. Fix 4 critical violations in UserService.java
   → Run: /aivory-fix
   → Focus on OWASP-A03 SQL injection

Priority 2 (High):
2. Review feature-auth branch before merging (13 violations)
   → Run: /aivory-review on PR
   → Address PCI-DSS compliance issues

Priority 3 (Medium):
3. Improve OWASP compliance score (currently 68/100)
   → Target: 80/100
   → Focus areas: Input validation, authentication

Ongoing:
4. Monitor PCI-DSS compliance (declining trend)
5. Review AI-generated code with high confidence scores
```

### 4.2 Offer Quick Actions

```markdown
Quick Actions
=============

What would you like to do?

1. Fix critical violations → /aivory-fix
2. Scan specific file → /aivory-scan <file>
3. Review current PR → /aivory-review
4. View detailed violation list → [Show full list]
5. Open dashboard in browser → [Open https://app.aivory.net/dashboard]
6. Export report → [Generate markdown/PDF report]

Your choice:
```

## Phase 5: Optional Detailed Views

### 5.1 Detailed Violation List

If user requests full violation list:

```markdown
All Active Violations (23 total)
================================

Critical (4):
-------------
[1] OWASP-A03-01: SQL Injection
    File: UserService.java:142
    Branch: feature-auth
    First seen: 2025-10-24
    Scans: 3 times
    Confidence: 95%

[2] PCI-DSS-6.5.1: Input Validation Failure
    File: UserService.java:142
    Branch: feature-auth
    First seen: 2025-10-24
    Scans: 3 times
    Confidence: 95%
    (Same location as #1, different standard)

[... continue all critical ...]

High (12):
----------
[5] OWASP-A02-02: Hardcoded Credentials
    File: AppConfig.java:45
    Branch: feature-auth
    First seen: 2025-10-25
    Scans: 2 times
    Confidence: 88%

[... continue ...]
```

### 5.2 Export Options

If user wants to export:

```bash
# Generate markdown report
cat > aivory-compliance-report-$(date +%Y-%m-%d).md <<EOF
[Full dashboard content as markdown]
EOF

echo "Report saved to: aivory-compliance-report-$(date +%Y-%m-%d).md"
```

## Error Handling

- **Backend unavailable**: Show instructions to start backend
- **No metrics available**: Suggest running `/aivory-scan` first
- **Authentication required**: Provide login instructions
- **API errors**: Show error details, suggest checking backend logs

## Configuration

Respect plugin settings:
- `backendUrl` - Dashboard API endpoint
- `enabledStandards` - Filter metrics by configured standards
- `enableAiDetection` - Show/hide AI detection metrics

## Notes

- Dashboard data comes from backend database (historical tracking)
- Metrics use branch-aware deduplication logic
- Trends show changes over time for informed decision-making
- Hotspots identify where to focus remediation efforts
- Integration with `/aivory-fix` for action-oriented workflow
- Consider adding `/aivory-dashboard --json` for programmatic access
