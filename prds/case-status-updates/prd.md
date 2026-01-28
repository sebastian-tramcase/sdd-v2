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

Automated SMS notifications triggered by case stage changes. This is a 90-day quick win initiative to keep clients informed automatically.

---

## Problem Statement

Clients aren't automatically notified of case progress:
- Manual notifications are inconsistent
- Clients call to check status
- No automated communication on stage changes

---

## Proposed Solution

Implement automated SMS notifications:
1. Trigger SMS on case stage changes
2. Use configurable templates per stage
3. Track notification delivery

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Client | automatic SMS when my case progresses | I stay informed without calling |
| P1 | Agent | clients notified automatically | I spend less time on status updates |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| SMS Case Status Update | Automated SMS notifications on case stage changes | Critical | In Progress |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Automated SMS on stage change | Working |
| Client satisfaction | Improved |

---

## Technical Approach

- **Stack:** Django signals, existing SMS provider
- **Integrates with:** MyCase Looker (case stages), Omnichannel (SMS)

---

## Dependencies

| Dependency | Status |
|------------|--------|
| MyCase Looker Case Stage feature | Required |

---

## Timeline

Q1 2026 (January)
