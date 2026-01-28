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

### Current System Limitations (Law Ruler)

| Problem | Impact |
|---------|--------|
| **Slow response time** | 2.8 hours average response to leads (industry standard is < 60 seconds) |
| **No automatic distribution** | No lead distribution based on agent shifts or performance |
| **No intelligent prioritization** | Cannot prioritize leads by contact probability or conversion likelihood |
| **Limited context for agents** | Agents have little information available when calling |
| **Limited customization** | Restricted options for filters, reports, and basic automations |

### Financial Impact

- Law Ruler costs approximately **$34K per month**
- Contract renewal: **February 26, 2026**
- One of the highest-cost systems alongside MyCase and 8x8

### Expected Value

| Metric | Current | Target | Improvement |
|--------|---------|--------|-------------|
| Response time | 2.8 hours | < 60 seconds | 98% faster |
| Lead capacity | Limited | 500+ leads/day | Automatic distribution |
| Monthly savings | - | $34,000/month | Operational costs |

---

## Features

### 1. Lead Distributor (Critical)

**Objective:** A prioritization and routing system that determines which leads should be called and in what order based on configurable cadences and priorities.

#### Two Operating Modes

**Mode 1: Auto-dialer ON**
```
Lead → Call Cadence → Queue → Auto-dialer → Dials Lead → AMD → Transfer to Agent → Outcome → Status Change
```

**Mode 2: Auto-dialer OFF**
```
Lead → Call Cadence → Queue → Notification to Agent → Agent Accepts → Agent Dials → Outcome → Status Change
```

#### Queue Ordering Rules

| Position | Lead Type | Sorting Rule |
|----------|-----------|--------------|
| 1st | Recycled leads | First recycled, first out (FIFO) |
| 2nd | Priority 0 leads | Oldest last contact time first |
| 3rd | All other leads | Next attempt date, then priority as tiebreaker |

#### Call Cadence

Every lead type must have a cadence configured defining:
- **Attempt timing:** When to retry (e.g., 1 hour, 1 day, 4 days, 7 days)
- **Priority:** Relative importance when leads have same next attempt time
- **Final trigger:** What happens when cadence completes (usually status change)

#### Priority 0 Override

Special mode for scenarios like "clear all pre-closed leads today":
- Supervisor sets a lead TYPE to Priority 0 in Call Cadence configuration
- ALL leads of that type immediately jump to top of queue
- After attempt, status MUST change (configured requirement prevents infinite loops)

#### Agent States

| State | Available for Calls? | How It's Triggered |
|-------|---------------------|-------------------|
| Online | Yes | Login, finish wrap-up |
| Offline | No | Logout |
| On Call | No | Receives/makes call |
| Wrap-up | No | Call ends (max 10 min, then auto-returns to Online) |
| Busy | No | Recycle call, ignore notification timeout, manual |
| On a Break | No | Manual |

---

### 2. Inbound Calls Fix (Critical)

**Objective:** Separate SIP trunks for inbound calls with proper routing.

Inbound calls bypass the queue and take priority over outbound:
- Inbound rings ALL available agents simultaneously
- First agent to answer takes the call
- Lead is matched by phone number (or new lead created if unknown)

**Inbound/Outbound Collision:**
- Cancel the outbound dial
- Route the inbound call to an agent
- Log as "Outbound cancelled - inbound collision"

---

### 3. WebSocket Notifications Refactor (Critical)

**Objective:** Refactor inbound/outbound call notifications for reliability.

| Aspect | Behavior |
|--------|----------|
| Notifications per agent | One at a time |
| Who sees notification | Configurable (currently 1 agent) |
| Agent ignores notification | Lead recycled to top of queue, agent status → Busy |
| Notification timeout | Configurable |

---

### 4. Auto-dialer with AMD (High)

**Objective:** Enable automatic outbound dialing with voicemail detection.

Uses Telnyx Call Control API with AMD (Answering Machine Detection):
- Human answers → Transfer to available agent
- Machine/no answer → Tag outcome, return lead to cadence

**Dial Ratio Presets:**

| Ratio | Profile | Use Case |
|-------|---------|----------|
| 1.5:1 | Conservative | Low abandon rate, some agent idle time |
| 2.0:1 | Balanced | Good productivity, acceptable abandon rate |
| 3.0:1 | Aggressive | Maximum productivity, higher abandon risk |

---

### 5. Call Cadences Fix (High)

Fix call cadences functionality to properly calculate next attempt dates.

---

### 6. Reports: CSV Download (Medium)

CSV download for Calls & Consultations data.

---

## Features Summary

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Lead Distributor | Prioritization and routing system | Critical | In Progress |
| Inbound Calls Fix | Separate trunks for inbound calls | Critical | Not Started |
| WebSocket Refactor | Inbound/Outbound calls notifications refactor | Critical | In Progress |
| Call Cadences Fix | Fix call cadences functionality | High | Not Started |
| Auto-dialer | New auto-dialer with AMD | High | In Progress |
| Hard Launch | Full production launch | Critical | Not Started |
| Reports: CSV Download | CSV download for Calls & Consultations | Medium | Not Started |
| Intake & Consultation Reports | CSV: Consultations & Cases sold | High | Not Started |

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

## Success Metrics

| Metric | Current | Target |
|--------|---------|--------|
| Response time | 2.8 hours | < 60 seconds |
| Distributor race conditions | Unknown | 0 |
| Lead capacity | Limited | 500+ leads/day |
| Inbound call routing | Not working | Working |

---

## Technical Constraints

### Must Follow
- Agent state management patterns
- E.164 phone normalization
- DNC API integration
- Existing lead model structure

### Must Not
- Create separate agent assignment logic (use existing distribution system)
- Store call recordings in auto-dialer (separate concern)
- Build custom voicemail detection (use Telnyx AMD)

---

## Configuration Options

**Who can configure:** Supervisors, managers, admins

| Setting | Description | Default |
|---------|-------------|---------|
| Cadence timing | When to retry per lead status | Per status |
| Priority per status | Relative importance | Per status |
| Final trigger | Status change when cadence completes | Per status |
| Auto-dialer ratio | Calls per available agent | Configurable |
| Notification recipients | How many agents receive notification | 1 |
| Notification timeout | Time before Busy status | Configurable |
| Wrap-up timeout | Max time in wrap-up | 10 minutes |
| Queue depletion alert | When to notify supervisors | Per supervisor |
| Assignment duration | How long lead stays assigned | 30 days |

---

## Edge Cases

### Unanswered Call Outcomes

| Outcome | Detection | Behavior |
|---------|-----------|----------|
| No answer (rings out) | Auto-dialer | Counts as attempt → Back to cadence |
| Voicemail/machine | AMD | Counts as attempt → Back to cadence |
| Busy signal | Cannot detect | Counts as attempt → Back to cadence |
| Disconnected number | Cannot detect | Cycles through cadence until agent manually identifies |

**AMD Limitation:** Auto-dialer can only detect human vs machine. Cannot distinguish between no answer, busy, or disconnected.

### Service Failures

| Scenario | Behavior |
|----------|----------|
| Auto-dialer service fails | Manual dial still available |
| Outbound service fails | Inbound still works (independent) |
| Inbound service fails | Outbound still works (independent) |

---

## Out of Scope

- Specific call outcome definitions (tenant-configurable)
- Specific lead status list (tenant-configurable)
- Commission splitting logic
- Auto-dialer on/off toggle (managed by external service)

---

## Future Additions

| Feature | Description |
|---------|-------------|
| Estimated wait time | Show inbound callers their position in queue |
| Callback request | Let callers request callback instead of holding |
| Skill-based routing | Route to Spanish speakers, specialists, etc. |
| Timezone filtering | Filter call timing by lead area code |
| A/B testing cadences | Test different configurations |

---

## Dependencies

| Dependency | Type | Status |
|------------|------|--------|
| SIP trunk separation | Technical | Required |
| WebSocket infrastructure | Technical | Required |
| Softphone infrastructure | Technical | Complete |
| Telnyx Call Control API | External | Complete |

---

## Timeline

Q1 2026 (January - February hard launch)
