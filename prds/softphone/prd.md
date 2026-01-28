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

The Softphone provides voice communication capabilities for agents via WebRTC. This initiative covers critical fixes and feature enhancements including conference calls for payment processing, dial pad for free-form calling, auto-dialer with AMD, and extended call time limits.

---

## Problem Statement

Agents need enhanced voice communication tools:
- **Conference calls**: 15-20% of leads require third-party payment assistance but agents can't add parties to calls
- **Free-form calling**: Agents can't dial unsaved contacts or access recordings for non-lead calls
- **Auto-dialer**: Current 2.8-hour average response time (industry standard < 60 seconds)
- **Call limits**: 3-hour call time limit too short for complex cases
- **Psychology team**: Lacks dedicated phone numbers

---

## Features

### 1. Conference Calls (P1)

**Objective:** Enable Sales Agents to add a third party to an active call for payment processing.

**Business Impact:**
- Payment consultation calls average 8-12 minutes
- ~15-20% of leads require third-party payment assistance
- Common third parties: spouse, parent, sibling, employer
- Directly impacts conversion rate by removing payment friction

**User Stories:**

| ID | Story | Type | Priority |
|----|-------|------|----------|
| SOFT-001 | LiveKit Conference Feasibility Spike | spike | P0 |
| SOFT-002 | Conference Backend Infrastructure | feature | P1 |
| SOFT-003 | Add Party UI & Dial-Out Flow | feature | P1 |
| SOFT-004 | Conference UI & Participant Lifecycle | feature | P1 |
| SOFT-005 | Recording Compliance for Conferences | feature | P2 |

**Acceptance Criteria:**
- [ ] Agent can add a third party to any active call
- [ ] All three parties can communicate simultaneously
- [ ] Conference persists when any single participant leaves
- [ ] All conference audio is recorded with compliance notification
- [ ] Conference metadata is captured in call logs

**Out of Scope:**
- Video conferencing (voice only)
- More than 3 participants
- Third party joining via link (dial-out only)
- Muting individual participants

---

### 2. Free-Form Calling (P1)

**Objective:** Enable users to make calls to any phone number and access recordings for all their calls.

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| SOFT-003 | Dialpad for manual dialing from idle state | P1 |
| SOFT-007 | Call history view with recording access | P1 |

**Acceptance Criteria:**
- [ ] Users can dial any phone number from the softphone idle state
- [ ] Users can view a paginated list of all their historical calls
- [ ] Users can download recordings for any call (with or without lead association)
- [ ] Both features respect RBAC permissions (`calls:1` for history, `calls:2` for dialing)

**Why grouped:** Without call history, recordings from dialpad calls would be inaccessible (omnichannel only shows lead-associated calls).

**Out of Scope:**
- Filtering/searching call history
- Contact lookup as user types
- International dialing with country code picker
- Speed dial / favorites

---

### 3. Auto-Dialer with AMD (P1)

**Objective:** Enable automatic outbound dialing with voicemail detection so agents maximize talk time.

**User Stories:**

| ID | As a... | I want... | Priority |
|----|---------|-----------|----------|
| US-001 | Manager | system to automatically dial multiple leads | P1 |
| US-002 | Agent | answered calls to connect automatically | P1 |
| US-003 | Manager | voicemails detected and tagged automatically | P1 |
| US-004 | Manager | on/off switch for auto-dialer | P1 |
| US-005 | Manager | auto-dialer to pause when no agents available | P1 |
| US-006 | Manager | dial ratio presets to select from | P1 |

**Dial Ratio Presets:**

| Ratio | Profile | Use Case |
|-------|---------|----------|
| 1.5:1 | Conservative | Low abandon rate, some agent idle time |
| 2.0:1 | Balanced | Good productivity, acceptable abandon rate |
| 3.0:1 | Aggressive | Maximum productivity, higher abandon risk |

**Key Rules:**
- `enabled` is manager-controlled (UI toggle)
- `paused` is system-controlled (agent availability)
- Both must be favorable for dialing to occur
- Uses Telnyx Call Control API with AMD (Answering Machine Detection)

**Architecture:**
```
Manager UI → Auto-Dialer Service → Telnyx Call Control API → Lead Phone
                  ↓                        ↓
          Agent Status Service      AMD Detection (Human/Machine)
          (pause if 0 available)           ↓
                                    Human → Distribution → LiveKit Room
                                    Machine → Tag Outcome (no agent)
```

---

### 4. Redial During Wrap-Up (P2)

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| SOFT-002 | Redial button in wrap-up state | P2 |

---

### 5. Call Time Extension (P2)

Extend call time limit from 3 to 6 hours for complex cases.

---

### 6. Psychology Phone Numbers (Critical)

Activate dedicated phone numbers for Psychology team so clients see consistent caller ID.

---

### 7. Internal Communications (P3)

Internal calls between team members without using external lines.

---

## Technical Constraints

### Must Follow
- LiveKit room with multiple SIP participants pattern
- Call management patterns in: `codebase/lawos/apps/calls/services/`
- Softphone UI patterns in: `codebase/lawos/packages/dashboard/src/components/softphone/`
- Telnyx/Apeiron SIP trunk for PSTN dial-out
- Existing Keypad component: `codebase/lawos/packages/ui/src/components/ui/keypad/keypad.tsx`

### Must Not
- Build custom voicemail detection (use Telnyx AMD)
- Create separate agent assignment logic (use existing distribution system)
- Store call recordings in auto-dialer (separate concern)

---

## Dependencies

### Blocked By
- Existing softphone/WebRTC implementation (Complete)
- LiveKit integration for calls (Complete)
- Telnyx/Apeiron SIP trunk (Complete)
- Lead distribution queue system (Complete)

### Blocks
- Conference call with supervisor for escalation
- Multi-party payment processing workflow
- Advanced predictive dialing
- Auto-dialer analytics dashboard

---

## Timeline

Q1 2026 (January - March)
