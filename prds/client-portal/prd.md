---
title: Client Portal
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: 90 Days Quick Wins
---

# Client Portal

## Executive Summary

Client Portal provides a secure interface for clients to upload documents and view case status. This initiative includes the core portal v1 functionality and document workflows with automated SMS notifications.

---

## Problem Statement

- **No self-service**: Clients have no way to upload documents or check status
- **Manual notifications**: Operations team manually sends SMS when case stages change
- **Security**: Need secure, authenticated access for sensitive documents

---

## Features

### 1. Portal v1 (Critical)

**Objective:** Core portal with document upload, status view, and secure authentication.

**User Stories:**

| ID | Title | Type | Status |
|----|-------|------|--------|
| PORT-001 | Documents not visible when >3 files | Bug | Draft |
| PORT-002 | Independent portals for derived clients | Enhancement | Draft |
| PORT-003 | Wrong document requirements shown | Bug | Draft |

**Acceptance Criteria:**
- [ ] Secure document upload functional
- [ ] Case status view available
- [ ] Secure link with 2FA/SMS authentication

---

### 2. Document Workflows with SMS Automation (P1)

**Objective:** Enable document workflows to trigger automated SMS when cases reach specific stages.

**Linear:** [TCLAW-1119](https://linear.app/tramcase/issue/TCLAW-1119)

**User Stories:**

| ID | Story | Priority | Status |
|----|-------|----------|--------|
| [TCLAW-1117](https://linear.app/tramcase/issue/TCLAW-1117) | Use Normalized Practice Area and Case Stage for Document Workflows | P1 | Draft |
| [TCLAW-1118](https://linear.app/tramcase/issue/TCLAW-1118) | SMS Automation for Document Workflows | P1 | Draft |

**Dependency Structure:**
```
TCLAW-1117 (Foundation)
    └── blocks → TCLAW-1118 (SMS Automation)
```

**Acceptance Criteria:**
- [ ] Workflows match cases using `normalized_practice_area` and `normalized_case_stage` (clean data)
- [ ] Workflow creation UI shows only clean practice area and case stage values
- [ ] SMS automations can be configured per workflow (toggle + template)
- [ ] SMS messages automatically sent when cases match workflows with SMS enabled
- [ ] All SMS messages logged in Omnichannel for visibility

**Key Flow:**
1. Admin creates workflow: "Immigration - T-Visa" + "Filing" stage + SMS template
2. Case manager updates case stage to "Filing" in MyCase Looker
3. System matches case against workflows using normalized data
4. SMS automatically sent to client's phone
5. Message appears in Omnichannel inbox for visibility

**Business Context:** Operations team needs to send automated SMS when cases reach specific stages. Currently requires manual intervention. This enables "set and forget" automations.

---

## Technical Constraints

### Must Follow
- Workflow matching pattern in: `codebase/lawos/src/tramcase/apps/mycase/portal_services.py` (WorkflowRequirementService)
- SMS sending pattern in: `codebase/lawos/src/tramcase/apps/mycase/portal_services.py:1193` (_send_sms_with_history)
- Model pattern in: `codebase/lawos/src/tramcase/apps/mycase/models.py` (WorkflowDocumentRequirement)

### Must Not
- Match workflows on dirty/original `practice_area` and `case_stage` values
- Send SMS without logging to SMSHistory (Omnichannel visibility required)
- Create new SMS infrastructure (use existing _send_sms_with_history)

---

## Out of Scope

- MyCase Looker UI changes for normalized fields (separate story: TCLAW-1116)
- SMS deduplication (preventing re-send if case returns to same stage)
- Email automations (future feature)
- Task automations (future feature)
- Multi-language SMS templates
- SMS scheduling for later delivery

---

## Dependencies

### Blocked By
- Aurora normalized columns deployed (Complete)
- Omnichannel (SMS infrastructure)

### Blocks
- Document Center intake
- Client self-service
- Future workflow automations (email, tasks)
- SMS analytics and reporting
- TCLAW-1116: Display Normalized Practice Area and Case Stage (parallel work)

---

## Timeline

Q1 2026 (January - February)
