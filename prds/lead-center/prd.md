---
title: Lead Center
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: Platform Stabilization
---

# Lead Center

## Executive Summary

Lead Center is the core lead management and distribution system. This initiative covers critical infrastructure fixes and feature enhancements for handling inbound/outbound calls and lead distribution, leading up to hard launch.

---

## Problem Statement

Lead Center needs infrastructure improvements before hard launch:
- Distributor race conditions cause missed assignments
- Inbound calls require separate trunks
- WebSocket notifications need refactoring
- Auto-dialer with AMD needed for efficiency

---

## Proposed Solution

Fix infrastructure issues and enhance capabilities:
1. Separate SIP trunks for inbound calls
2. Refactor WebSocket notifications
3. Fix distributor race conditions
4. Implement auto-dialer with AMD
5. Complete hard launch

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Agent | reliable lead distribution | I don't miss assignments due to race conditions |
| P1 | Agent | working inbound call routing | I can receive calls on dedicated numbers |
| P1 | Agent | real-time notifications | I'm immediately notified of new leads |
| P2 | Agent | auto-dialer functionality | outbound calling is more efficient |
| P2 | Supervisor | downloadable reports | I can analyze call and consultation data |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Inbound Calls Fix | Separate trunks for inbound calls | Critical | Not Started |
| Web Sockets Refactor | Inbound/Outbound calls notifications refactor | Critical | In Progress |
| Lead Distributor | Prioritization and routing system | Critical | In Progress |
| Call Cadences Fix | Fix call cadences functionality | High | Not Started |
| Auto-dialer | New auto-dialer with AMD | High | In Progress |
| Hard Launch | Full production launch | Critical | Not Started |
| Reports: CSV Download | CSV download for Calls & Consultations | Medium | Not Started |
| Intake & Consultation Reports | CSV: Consultations & Cases sold | High | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Distributor race conditions | 0 |
| Inbound call routing | Working |
| Hard launch | Complete |

---

## Technical Approach

- **Stack:** Django, PostgreSQL, database queues
- **Integrates with:** Softphone, Omnichannel, Mendoza Express

---

## Dependencies

| Dependency | Status |
|------------|--------|
| SIP trunk separation | Required |
| WebSocket refactor | Required |

---

## Timeline

Q1 2026 (January - February hard launch)
