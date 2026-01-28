---
title: MyCase Looker
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: Platform Stabilization
---

# MyCase Looker

## Executive Summary

MyCase Looker is the case management interface that provides read/write access to case data. This initiative covers stabilization and feature enhancements including AI-generated case summaries, case stage improvements, and notes enhancements.

---

## Problem Statement

- **Case stages**: Not all stages visible/editable, missing FULLY and SEMI stages
- **Notes**: Character limits too restrictive (10K), no rich text
- **Contact view**: No way to see contacts with linked cases
- **Case summaries**: Agents spend time reading through case notes to understand case status
- **Billing**: No billing information displayed

---

## Features

### 1. Case Smart Summary (P1)

**Objective:** Display AI-generated case summaries in a new "Summary" tab, enabling quick case overviews.

**User Stories:**

| ID | Story | Priority | Phase |
|----|-------|----------|-------|
| TCLAW-1106 | Add Summary tab with hardcoded POC data for 7 specific cases | P1 | POC |
| TCLAW-1107 | Connect Summary tab to AI pipeline API | P1 | Integration |
| TCLAW-1108 | Handle loading, error, and empty states for summaries | P2 | Polish |

**Acceptance Criteria:**
- [ ] Users can view a "Summary" tab in Case Detail (alongside Notes, Documents, Portal, Consultation)
- [ ] Summary tab displays AI-generated case overview with structured sections
- [ ] Feature controlled by feature flag for controlled POC rollout
- [ ] Summary data retrieved from AI pipeline (S3/SQS) via CaseSummaryService
- [ ] Clear feedback when summary is unavailable

**Summary Sections:**
- Executive Summary
- Key Events
- Action Items
- Critical Dates
- Decisions & Outcomes

**Data Flow:**
```
AI Pipeline (generates summaries) → Storage → API → Summary Tab → User sees formatted summary
```

**POC Cases:** 30066182, 29834304, 12905057, 17275979, 18478577, 24958826, 33980506

---

### 2. Case Stage Improvements (Critical)

**Objective:** Replace API calls with direct DB access and add missing stages.

**User Stories:**

| ID | Title | Type | Status |
|----|-------|------|--------|
| MCL-003 | Stage changes reverting automatically | Bug | Draft |

**Missing Stages to Add:** FULLY, SEMI

---

### 3. Notes Enhancements (Complete)

| Feature | Status |
|---------|--------|
| Character limit 10K → 200K | Complete |
| Rich text formatting | Complete |
| Edit & Delete | Complete |
| Expand/Collapse All | Complete |
| Spell check fix | Complete |

---

### 4. Contact View (P1)

Contact view with linked cases - depends on Data Foundation Contact Migration.

---

### 5. Linked Cases (P1)

Support for Derivatives and Combos (related cases).

---

### 6. Case Reopen (P1)

New stage for reopened cases. Ability to update status, archive, record close date.

---

### 7. Practice Area (P2)

Enable practice area editing in case detail.

---

### 8. Billing Read-Only (P2)

Display payment information (read-only).

---

### 9. Reports (P1)

CSV downloads for case data.

---

## Technical Constraints

### Must Follow
- Pattern in: `codebase/lawos/packages/dashboard/src/pages/MyCase/Dashboard.tsx` (existing tabs)
- Use `@tramcase/ui` Tabs components
- Use `useFeatureFlag("case_smart_summary")` for visibility control
- Follow existing data fetching patterns

### Must Not
- Duplicate summary rendering logic
- Add new permissions - use existing `mycase:looker` permission
- Bypass feature flag during POC phase

---

## Dependencies

### Blocked By
- **PR #1671** for Case Smart Summary (provides CaseSummary model, service, API)
- **AI Data Pipeline** (tramcase-data-pipelines repo) for summary generation
- Data Foundation (Contact Migration, Matter Migration)

### Blocks
- Summary-based case insights dashboard
- Summary in client portal
- Summary notifications/alerts

---

## Timeline

Q1 2026 (January - March)
