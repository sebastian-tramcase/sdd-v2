---
title: Document Center
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: 90 Days Quick Wins
---

# Document Center

## Executive Summary

Document Center provides document management, collection portal with OCR, and workflow automation. This is a 90-day quick win initiative focused on streamlining document collection and processing.

---

## Problem Statement

Document management is fragmented:
- Documents scattered across Dropbox and LawOS
- No OCR for processing Letters of Evidence and Testimonies
- Folder structure needs redesign
- No document workflows per practice area

---

## Proposed Solution

Build a unified Document Center with:
1. Document tracker with OCR for Letters of Evidence and Testimonies
2. Complete Dropbox to LawOS migration
3. Redesigned folder structure
4. Document workflows configured per practice area

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Agent Specialist | document tracker with OCR | I can process testimonies efficiently |
| P1 | Administrator | all documents in LawOS | we're not split across systems |
| P2 | Administrator | organized folder structure | documents are easy to find |
| P2 | Case Manager | practice area workflows | document processes match our needs |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Document Tracker + Intelligence | Document collection portal with OCR | Critical | In Progress |
| Dropbox to LawOS Migration | Migration from Dropbox | High | Not Started |
| Folder Structure Redesign | Pre-migration folder structure redesign | High | Not Started |
| Documents Workflows by Practice Area | Configure document workflows per practice area | High | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Document tracker with OCR | Functional |
| Dropbox migration | Complete |
| Practice area workflows | Configured |

---

## Technical Approach

- **Stack:** Django, PostgreSQL, OCR service
- **Integrates with:** Client Portal, Workflows

---

## Dependencies

| Dependency | Status |
|------------|--------|
| Client Portal (for document uploads) | Required |

---

## Timeline

Q1 2026 (January - March)
