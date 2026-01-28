---
title: Case Status Updates
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: 90 Days Quick Wins
---

# Case Status Updates

## Executive Summary

Automated SMS notifications triggered by case stage changes. This is a 90-day quick win initiative to keep clients informed automatically when their case progresses through stages.

---

## Problem Statement

Clients aren't automatically notified of case progress:
- **Manual notifications are inconsistent**: Operations team manually sends SMS when case stages change
- **Clients call to check status**: High inbound call volume for status inquiries
- **No automated communication**: Stage changes require manual intervention to notify clients
- **Tracking gaps**: No visibility into which clients have been notified

---

## Features

### 1. SMS Case Status Update (Critical)

**Objective:** Enable automatic SMS notifications when cases reach specific stages.

**Key Flow:**
1. Case manager updates case stage in MyCase Looker (e.g., "Filing" → "Processing")
2. System detects stage change via Django signals
3. System matches case against notification rules using normalized data
4. SMS automatically sent to client's primary phone number
5. Message logged in Omnichannel inbox for visibility

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| CASE-001 | Detect case stage changes | P1 |
| CASE-002 | Match stage changes against notification rules | P1 |
| CASE-003 | Send SMS on rule match | P1 |
| CASE-004 | Log all SMS to Omnichannel | P1 |
| CASE-005 | Configure notification templates per stage | P2 |

**Acceptance Criteria:**
- [ ] System detects case stage changes in MyCase Looker
- [ ] Notifications match cases using `normalized_practice_area` and `normalized_case_stage` (clean data)
- [ ] SMS messages automatically sent when cases match notification rules
- [ ] All SMS messages logged in Omnichannel for visibility
- [ ] Admin can configure SMS templates per practice area + stage combination
- [ ] SMS sending uses existing `_send_sms_with_history` pattern

---

## Technical Constraints

### Must Follow
- SMS sending pattern in: `codebase/lawos/src/tramcase/apps/mycase/portal_services.py:1193` (_send_sms_with_history)
- Model patterns in: `codebase/lawos/src/tramcase/apps/mycase/models.py`
- Use `normalized_practice_area` and `normalized_case_stage` for matching

### Must Not
- Match on dirty/original `practice_area` and `case_stage` values
- Send SMS without logging to SMSHistory (Omnichannel visibility required)
- Create new SMS infrastructure (use existing patterns)

---

## Success Metrics

| Metric | Current | Target |
|--------|---------|--------|
| Manual status update SMS | 100% manual | < 10% manual |
| Client status inquiry calls | High | 50% reduction |
| SMS notification delivery | N/A | > 95% delivered |

---

## Out of Scope

- SMS deduplication (preventing re-send if case returns to same stage)
- Email notifications (future feature)
- Push notifications (future feature)
- Multi-language SMS templates
- SMS scheduling for later delivery

---

## Dependencies

### Blocked By

| Dependency | Description | Status |
|------------|-------------|--------|
| MyCase Looker Case Stage | Need working case stage editing to trigger changes | Required |
| Normalized Data Columns | `normalized_practice_area` and `normalized_case_stage` in Aurora | Complete |
| Omnichannel SMS Infrastructure | Existing SMS sending and history logging | Complete |

### Blocks

| Feature | Description |
|---------|-------------|
| Document Workflows SMS | Client Portal document workflow SMS automation builds on this pattern |
| SMS Analytics | Reporting on notification effectiveness |

---

## Related Features

**Client Portal - Document Workflows with SMS Automation (TCLAW-1119):**
This Case Status Updates feature shares SMS patterns with the Document Workflows feature in Client Portal. Both use:
- Normalized practice area and case stage for matching
- Same `_send_sms_with_history` infrastructure
- Omnichannel logging for visibility

The difference: Case Status Updates is triggered by stage changes, while Document Workflows is triggered by document requirements matching.

---

## Timeline

Q1 2026 (January)
