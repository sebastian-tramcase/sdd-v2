---
title: Softphone
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: Platform Stabilization
---

# Softphone

## Executive Summary

The Softphone provides voice communication capabilities for agents. This initiative covers critical fixes and feature enhancements for call handling, including conference calls, dial pad functionality, and extended call time limits.

---

## Problem Statement

Agents need reliable voice communication tools to handle calls effectively. Current limitations include:
- No conference call capability
- Cannot dial unsaved contacts
- 3-hour call time limit too short for complex cases
- Psychology team lacks dedicated phone numbers

---

## Proposed Solution

Enhance the softphone with:
1. Psychology team dedicated phone numbers
2. Conference call support for multiple parties
3. Dial pad for calling unsaved contacts
4. Extended call time limit (6 hours)

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Psychology Agent | dedicated phone numbers for my team | clients see consistent caller ID |
| P1 | Agent | to add multiple people to a call | I can conduct conference calls |
| P1 | Agent | a dial pad to call unsaved numbers | I can reach contacts not in the system |
| P2 | Agent | to redial from wrap-up state | I can quickly retry failed calls |
| P2 | Agent | longer call time limits | complex calls don't get cut off |
| P3 | Agent | to call team members internally | I can collaborate without external calls |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Psychology Phone Numbers | Activate specific numbers for Psychology team | Critical | In Progress |
| Conferences | Add multiple people to calls | High | Not Started |
| Dialer/Dial Pad | Call unsaved contacts | High | Not Started |
| Redial During Wrap-Up | Retry calls from wrap-up state | High | Not Started |
| Auto-Dialer | Automatic outbound dialing with AMD and agent distribution | High | Draft |
| Call Time Limit | Extend from 3 to 6 hours | Medium | Not Started |
| Internal Comms | Internal calls between team members | Medium | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Psychology team has dedicated numbers | 100% |
| Conference calls supported | Yes |
| Call time limit | 6 hours |

---

## Technical Approach

- **Stack:** LiveKit WebRTC, existing SIP infrastructure
- **Integrates with:** Lead Center, Omnichannel

---

## Dependencies

| Dependency | Status |
|------------|--------|
| SIP trunk configuration | Required |

---

## Timeline

Q1 2026 (January - March)
