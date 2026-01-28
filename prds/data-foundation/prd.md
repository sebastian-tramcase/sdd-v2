---
title: Data Foundation
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: Core Infrastructure
---

# Data Foundation

## Executive Summary

Data Foundation covers the core data model migrations and infrastructure improvements that enable other features. This is foundational work for the entire platform.

---

## Problem Statement

Core data models need migration and enhancement:
- Matter and Contact data models need migration
- Global search missing across modules
- Teams and Roles system not implemented
- Conversion flow (Lead → Prospect → Case) incomplete

---

## Proposed Solution

Build data foundation with:
1. Matter and Contact data model migration
2. Task and Communications data model migration
3. Global search across all modules
4. Teams and Roles system
5. Complete conversion flow

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Developer | migrated data models | features have consistent data |
| P1 | Agent | global search | I can find anything quickly |
| P1 | Administrator | teams and roles | I can manage permissions properly |
| P1 | Agent | complete conversion flow | leads become cases seamlessly |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Matter Migration | Migrate Matter data model | Critical | In Progress |
| Contact Migration | Migrate Contact data model | Critical | In Progress |
| Search Infrastructure | Improve search infrastructure | Medium | Not Started |
| Task Migration | Migrate Task data model | High | Not Started |
| Communications Migration | Migrate Communications data model | High | Not Started |
| Conversion Flow | Lead → Prospect → Open Case → Closed Case | High | Not Started |
| Global Search | Global search across all modules | High | Not Started |
| Search in Notes | Full-text search in notes | High | Not Started |
| Search in Omnichannel | Full-text search in SMS/communications | High | Not Started |
| Search History | Recent searches and favorites | Medium | Not Started |
| Teams | User groups, SIP trunk, Phone numbers, Reports | High | Not Started |
| Roles | Access levels: Agent, Supervisor, Team Lead, Manager | High | Not Started |
| Follow-up Team Permissions | Fix recordings & documents access | High | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Matter and Contact migrated | Complete |
| Global search functional | Yes |
| Teams and Roles implemented | Yes |

---

## Technical Approach

- **Stack:** Django ORM, PostgreSQL, database migrations
- **Integrates with:** All modules (foundational)

---

## Dependencies

| Dependency | Status |
|------------|--------|
| None (foundational) | - |

**Enables:**
- MyCase Looker Contact View
- Linked Cases
- All search features

---

## Timeline

Q1 2026 (January - March, ongoing)
