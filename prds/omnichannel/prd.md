---
title: Omnichannel
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: Platform Stabilization
---

# Omnichannel

## Executive Summary

Omnichannel provides unified communication across SMS, calls, and other channels. This initiative covers fixes and enhancements to the messaging and supervisor features.

---

## Problem Statement

Omnichannel has several issues:
- SMS character limit not handled (messages >999 characters fail)
- Multiple phone number selection broken
- Call supervisor features need RBAC
- Client phone numbers shown instead of names

---

## Proposed Solution

Fix and enhance Omnichannel with:
1. SMS character limit handling (auto-split)
2. Working multiple phone number selector
3. Call supervisor features with RBAC
4. Primary phone designation
5. Client name display

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Agent | SMS messages to auto-split at 999 chars | long messages don't fail |
| P1 | Agent | to select which number to message | I reach clients on correct numbers |
| P1 | Supervisor | listen/barge-in with RBAC | I can monitor calls appropriately |
| P2 | Agent | to designate primary phone | default number is correct |
| P2 | Agent | client names instead of numbers | conversations are easier to identify |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| SMS Character Limit Fix | Split messages >999 characters | High | Not Started |
| Multiple Phone Number Selector | Select number when client has multiple | High | In Progress |
| Call Supervisor Features | RBAC based Listen & Barge-in | High | In Progress |
| Primary Phone | Option to designate primary number | Medium | Not Started |
| Client Name Display | Show name instead of phone number | Medium | Not Started |
| SMS Shared Phone Routing | Route SMS when multiple cases share same phone | High | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| SMS character limit handled | Auto-split |
| Multiple phone selector working | Yes |
| Supervisor features with RBAC | Yes |

---

## Technical Approach

- **Stack:** Existing SMS provider, LiveKit for calls
- **Integrates with:** Lead Center, Softphone, RBAC system

---

## Dependencies

| Dependency | Status |
|------------|--------|
| Data Foundation (Teams, Roles) | Required |

---

## Timeline

Q1 2026 (January)
