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

MyCase Looker is the case management interface that provides read/write access to case data. This initiative covers stabilization and feature enhancements to replace API calls with direct DB access and add missing functionality.

---

## Problem Statement

MyCase Looker needs improvements:
- Not all case stages visible/editable
- Notes have insufficient character limits
- No contact view with linked cases
- Billing information not displayed

---

## Proposed Solution

Enhance MyCase Looker with:
1. All case stages visible and editable
2. Extended notes with rich text (200K character limit)
3. Contact view with linked cases
4. Read-only billing information display

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Paralegal | to see and edit all case stages | I can track full case progression |
| P1 | Agent | rich text notes with 200K limit | I can document complex case details |
| P1 | Case Manager | to view contacts with linked cases | I can see full contact history |
| P2 | Case Manager | to reopen closed cases | I can handle case extensions |
| P2 | Agent | billing information display | I can see payment status |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Case Stage | Replace API calls with DB + add missing stages | Critical | In Progress |
| Notes: Character Limit | Extend from 10,000 to 200,000 characters | High | Complete |
| Notes: Rich Text | Rich text formatting support | High | Complete |
| Notes: Edit & Delete | Basic editing functionality | High | Complete |
| Notes: Expand/Collapse All | Button to expand/collapse all notes | High | Complete |
| Practice Area | Enable practice area editing | Medium | In Progress |
| Case Status | Display Open/Closed status | Medium | Complete |
| Spell Check | Fix bug in notes and messages | Medium | Complete |
| Reports | CSV downloads | High | Not Started |
| Contact View | Contact view (depends on data model) | High | Not Started |
| Linked Cases | Derivatives and Combos support | High | Not Started |
| Case Stage Reopen | New stage for reopened cases | High | Not Started |
| Close & Reopen Cases | Update status, archive, record close date | High | Not Started |
| Billing (Read-only) | Display payment info | Medium | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| All case stages visible | Yes |
| Notes character limit | 200K |
| Contact view functional | Yes |

---

## Technical Approach

- **Stack:** Django ORM, PostgreSQL, React
- **Integrates with:** MyCase data model, LawOS authentication

---

## Dependencies

| Dependency | Status |
|------------|--------|
| Data Foundation (Contact Migration) | Required |
| Data Foundation (Matter Migration) | Required |

---

## Timeline

Q1 2026 (January - March)
