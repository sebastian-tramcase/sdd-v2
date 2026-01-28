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

Omnichannel provides unified communication across SMS, calls, and other channels. This initiative covers critical fixes for messaging and supervisor features, plus new routing logic for shared phone numbers.

---

## Problem Statement

- **SMS character limit**: Messages >999 characters fail silently
- **Multiple phone numbers**: Selection broken when client has multiple
- **Supervisor features**: Call monitoring needs RBAC
- **Shared phone routing**: When multiple cases share the same phone number, inbound SMS routes unpredictably (first match wins)
- **Client name display**: Phone numbers shown instead of names

---

## Features

### 1. SMS Routing for Shared Phone Numbers (P1)

**Objective:** Enable accurate SMS routing when multiple matters share the same phone number using session-based routing with a broadcast-and-claim mechanism.

**Problem Example:** Jane Doe is a witness in Case #123, #456, and #789. When she texts back, which case receives it? Currently: unknown (first match wins).

**Solution:**
1. **Session-based routing**: If we sent an outbound SMS within 48h, route replies to that case
2. **Broadcast for ambiguity**: If no recent outbound (or multiple), show in ALL relevant inboxes as "unclaimed"
3. **Explicit claim**: Agents click "Claim for this case" to take ownership (replying does NOT auto-claim)
4. **Dismiss option**: Agents can hide messages without affecting other cases
5. **Reassign capability**: Claimed messages can be moved to a different case

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| OMNI-001 | Track last outbound SMS per contact+case for routing decisions | P1 |
| OMNI-002 | Route inbound SMS to recent session or broadcast if ambiguous | P1 |
| OMNI-003 | Display unclaimed messages with visual indicator in inbox | P1 |
| OMNI-004 | Agent explicitly claims message for their case | P1 |
| OMNI-005 | Update/remove message from other inboxes when claimed | P1 |
| OMNI-006 | Agent dismisses message from their inbox without claiming | P2 |
| OMNI-007 | Move claimed message to a different case | P2 |

**Routing Decision Flow:**
```
INBOUND SMS ARRIVES
       │
       ▼
┌─────────────────────┐
│ Find all cases with │
│ this phone number   │
└──────────┬──────────┘
           │
     ┌─────┴─────┐
     │           │
   1 case    Multiple
     │           │
     ▼           ▼
  Route      ┌─────────────────────┐
  there      │ Recent outbound     │
             │ within 48h?         │
             └──────────┬──────────┘
                        │
              ┌─────────┴─────────┐
              │                   │
            YES                   NO
              │                   │
              ▼                   ▼
        Route to that       Broadcast to
        case                all cases as
                            "unclaimed"
```

**Key Design Decisions:**
- 48-hour session window: Balances conversation continuity with avoiding stale routing
- Broadcast over triage queue: Faster agent response
- Explicit claim over auto-claim: Agents can ask clarifying questions without taking ownership
- Dismiss is personal: Only hides from that agent's view

**Out of Scope:**
- Inbound call routing (SMS only)
- Per-case phone numbers (Twilio number pool)
- AI/NLP intent detection for routing
- Auto-reply asking contact which case they mean

---

### 2. SMS Character Limit Fix (P1)

Auto-split messages exceeding 999 characters into multiple segments.

---

### 3. Multiple Phone Number Selector (P1)

Fix phone number selection when client has multiple numbers. Currently broken.

---

### 4. Call Supervisor Features with RBAC (P1)

Implement Listen, Whisper, and Barge-in capabilities with proper permission checks.

| Mode | Description | Requires |
|------|-------------|----------|
| Listen | Silent observation | `calls:3` |
| Whisper | Coach agent (only agent hears) | `calls:3` |
| Barge | Join conversation (all parties hear) | `calls:3` |

---

### 5. Primary Phone Designation (P2)

Option to designate a primary phone number for contacts with multiple numbers.

---

### 6. Client Name Display (P2)

Show client names instead of phone numbers in inbox for easier identification.

---

## Technical Constraints

### Must Follow
- Existing SMS patterns in: `codebase/lawos/src/tramcase/apps/calls/sms/`
- E.164 phone normalization via existing `normalize_phone_to_e164()`
- Existing `SMSHistory` model structure (extend, don't replace)
- WebSocket patterns for real-time inbox updates
- Multi-tenancy patterns (organization-scoped)

### Must Not
- Break existing SMS routing for single-case contacts
- Auto-claim on reply (explicit claim only)
- Create orphaned messages (always visible somewhere)
- Store PII in routing logs (use IDs, not phone numbers)

---

## Dependencies

### Blocked By
- Existing `SMSHistory` model with `case_number` field (Complete)
- Existing `Contact` model with `ContactPhone` (Complete)
- Omnichannel inbox WebSocket infrastructure (Complete)
- Data Foundation (Teams, Roles) for RBAC

### Blocks
- Case Contacts PRD (inbound routing for case contacts)
- Full conversation threading feature

---

## Timeline

Q1 2026 (January)
