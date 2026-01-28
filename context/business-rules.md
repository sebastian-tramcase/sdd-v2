---
domain: platform
type: business-rules
last_updated: 2026-01-27
source: lawos-platform
---

# LawOS Business Rules & Logic

Core business logic for TramCase/LawOS - a legal case management platform with integrated call center operations.

## 1. Lead Distributor

The Lead Distributor is a prioritization and routing system that determines which leads should be called and in what order. It feeds leads to either the auto-dialer (when ON) or directly to agents via notifications (when OFF).

### Operating Modes

#### Mode 1: Auto-dialer ON

```
Lead → Call Cadence → Queue → Auto-dialer → Dials Lead → AMD → Transfer to Agent → Outcome → Status Change
```

- Auto-dialer automatically dials leads based on queue order
- AMD (Answering Machine Detection) determines if human or machine answers
- Human answers are transferred to available agents
- Machine/no answer returns lead to cadence for retry

#### Mode 2: Auto-dialer OFF

```
Lead → Call Cadence → Queue → Notification to Agent → Agent Accepts → Agent Dials → Outcome → Status Change
```

- Agents receive notifications for leads ready to call
- Agent accepts notification and manually dials
- If agent ignores notification, lead is recycled to top of queue

### Queue Ordering Rules

Leads in the queue are ordered by:

| Position | Lead Type | Sorting Rule |
|----------|-----------|--------------|
| 1st | Recycled leads | First recycled, first out (FIFO) |
| 2nd | Priority 0 leads | Oldest last contact time first |
| 3rd | All other leads | Next attempt date, then priority as tiebreaker |

**Key Rule:** If a lead's status has no cadence configured, the lead exits the distributor (terminal status).

### Call Cadence

Every lead type must have a cadence configured. The cadence defines:

- **Attempt timing:** When to retry (e.g., 1 hour, 1 day, 4 days, 7 days)
- **Priority:** Relative importance when leads have the same next attempt time
- **Final trigger:** What happens when cadence completes (usually status change)

**After any unanswered call:**
1. Attempt counter increments
2. Lead returns to Call Cadence
3. Next attempt date is calculated
4. Lead re-enters queue at appropriate position

**Unanswered includes:** No answer, voicemail, busy signal, disconnected (AMD cannot distinguish these - all count as attempts).

### Priority 0 Override

Priority 0 is a special mode for scenarios like "clear all pre-closed leads today."

**How it works:**
- Supervisor sets a lead TYPE (not individual lead) to Priority 0 in Call Cadence configuration
- ALL leads of that type immediately jump to top of queue
- Each lead gets one attempt while in Priority 0
- After attempt, status MUST change (configured requirement prevents infinite loops)
- Queue self-depletes as leads are attempted

**When multiple lead types are Priority 0:** Sorted together by last contact time (oldest first).

**Validation Rule:** Priority 0 cadences must have a different final trigger status (prevents infinite loops).

---

## 2. Inbound Call Handling

Inbound calls bypass the queue entirely and take priority over outbound.

### When Agents Are Available

- Inbound rings ALL available agents simultaneously
- First agent to answer takes the call
- Lead is matched by phone number (or new lead created if unknown)

### When No Agents Are Available

- Caller hears IVR: "All agents are busy, please hold"
- Caller waits in hold queue with music and periodic messages
- When agent becomes available, waiting inbound caller takes priority over outbound
- If caller hangs up while waiting, a new lead is created with "New Lead" status

### Inbound/Outbound Collision

If a lead is being dialed outbound and simultaneously calls inbound:
- Cancel the outbound dial
- Route the inbound call to an agent
- Log as "Outbound cancelled - inbound collision"

If outbound connects before system can cancel:
- Drop inbound from queue
- Notify agent: "Inbound was queued, already connected"
- Log single interaction

---

## 3. Agent States

| State | Available for Calls? | How It's Triggered |
|-------|---------------------|-------------------|
| **Online** | Yes | Login, finish wrap-up |
| **Offline** | No | Logout |
| **On Call** | No | Receives/makes call |
| **Wrap-up** | No | Call ends (max 10 min, then auto-returns to Online) |
| **Busy** | No | Recycle call, ignore notification timeout, manual |
| **On a Break** | No | Manual |

**Busy vs On a Break:** Both make agent unavailable. Distinction helps supervisors understand agent activity for reporting.

**Recycle a Call:** When an agent can't take a call (forgot to set Busy), they click "Recycle" → lead returns to top of queue → agent status auto-changes to Busy.

---

## 4. Agent Assignment

| Aspect | Behavior |
|--------|----------|
| Multiple agents available | Round robin (alternates fairly) |
| Auto-dialer ratio | Configurable (e.g., 2:1 = 2 calls per available agent) |
| Notification recipients | Configurable - how many agents receive the notification (currently 1) |
| All agents offline | Auto-dialer pauses automatically |

### "Assigned" vs "Call Transfer To"

Two different concepts:

| Variable | What It Means | Duration | Affects Routing? |
|----------|---------------|----------|------------------|
| **Call Transfer To** | Agent receiving THIS call right now | Temporary (null when call ends) | Yes |
| **Assigned** | Agent who worked/pitched the lead | 30 days (or until another agent claims) | No |

**Key Rule:** "Assigned" is for tracking and commission purposes only. It does NOT affect call routing.

---

## 5. Agent Notification Rules (Auto-dialer OFF)

| Aspect | Behavior |
|--------|----------|
| Notifications per agent | One at a time |
| Who sees notification | Configurable (currently 1 agent) |
| Agent ignores notification | Lead recycled to top of queue, agent status → Busy |
| Notification timeout | Configurable |

---

## 6. DNC (Do Not Call) Handling

| When | What Happens |
|------|--------------|
| Lead created | DNC API checked → If on list, status = "Do Not Call" (terminal) |
| Before every auto-dial | DNC API checked again → If on list, call blocked |
| Manual dial (auto-dialer OFF) | DNC check when agent accepts → Alert shown if on list |

---

## 7. Qualification Criteria

### What Makes a Lead "Qualified"

A lead is considered qualified when:
1. **Contact Made**: Agent successfully spoke with the lead
2. **Interest Confirmed**: Lead expressed interest in services
3. **Basic Criteria Met**: Lead meets firm's intake criteria (case type, jurisdiction, etc.)
4. **Call Outcome**: Agent selects `QUALIFIED` as the call outcome

### Call Outcomes

| Outcome | Description | Triggers Stage Change |
|---------|-------------|----------------------|
| `QUALIFIED` | Lead meets criteria, interested | Yes -> PREQUALIFIED |
| `NOT_INTERESTED` | Lead declined services | No |
| `CALLBACK_REQUESTED` | Lead wants to be called back | No |
| `VOICEMAIL` | Left voicemail message | No |
| `NO_ANSWER` | No response from lead | No |
| `NOT_SET` | Default/no outcome selected | No (preserves current stage) |

---

## 8. Status Transitions

### Sales Stage Progression

| Stage | Description | Entry Trigger | Requirements |
|-------|-------------|---------------|--------------|
| `NOT_SET` | Initial state | Lead creation | None |
| `PREQUALIFIED` | Lead qualified | `QUALIFIED` outcome | Successful contact |
| `PITCHED` | Price presented | Manual selection | Price amount entered |

### Lead Status Transitions

| Status | Description | Entry Triggers |
|--------|-------------|----------------|
| `NEW` | Fresh lead | Lead ingestion |
| `CONTACTED` | Call attempted | Call initiated |
| `QUALIFIED` | Meets criteria | `QUALIFIED` outcome |
| `NOT_INTERESTED` | Declined | `NOT_INTERESTED` outcome |
| `CALLBACK` | Needs follow-up | `CALLBACK_REQUESTED` outcome |
| `CONVERTED` | Became case | Case created |
| `DEAD` | No longer viable | Manual mark, excessive attempts |
| `DO_NOT_CALL` | On DNC list | DNC API check |

---

## 9. Time-Based Rules

### Timeout Durations

| Context | Duration | Action on Timeout |
|---------|----------|-------------------|
| **Notification Accept Window** | Configurable | Lead recycled, agent -> BUSY |
| **Wrap-Up Phase** | 10 minutes (600s) | Agent -> Online (auto-return) |
| **Session Idle** | 30 minutes | Auto-logout |
| **Callback Retry** | 24 hours | Lead re-enters queue |
| **Assignment Duration** | 30 days | Agent ownership expires |

**Important**: On wrap-up timeout, outcome defaults to `NOT_SET` which does NOT trigger any sales stage transition, preserving the lead's current stage.

### Performance SLAs

| Operation | Target | Notes |
|-----------|--------|-------|
| Response time | < 60 seconds | Industry standard (current: 2.8 hours) |
| WebSocket event handling | < 50ms | Agent status updates |
| LiveKit token generation | < 100ms | Call connection setup |
| Call connection time | < 2 seconds | End-to-end |
| UI responsiveness | < 100ms | User interactions |
| Lead distribution | < 5 seconds | Queue to assignment |

### Queue Empty Behavior

| Aspect | Behavior |
|--------|----------|
| Queue empty | Auto-dialer sleeps (event-driven) |
| New lead enters | Distributor wakes auto-dialer automatically |
| All leads in cadence cooldown | Notify supervisors in advance |
| Alert threshold | Configurable by supervisor |

---

## 10. Compliance

| Aspect | Current Behavior | Future |
|--------|------------------|--------|
| Calling hours | Based on agent availability (call center hours) | Configurable windows |
| Timezone handling | Not implemented | Filter by lead area code |
| Consent | Opt-in required before contact | - |
| Call recording | All calls recorded, agent gets verbal consent | - |
| Geographic scope | US only | Worldwide |

---

## 11. Permission Model

### RBAC Structure

LawOS uses **Schema-Aware Role-Based Access Control** with permission levels:

| Level | Name | Description |
|-------|------|-------------|
| 0 | None | No access |
| 1 | Read | View only |
| 2 | Write | Create and modify |
| 3 | Admin | Full control including delete |

### Default Roles

| Role | leads | calls | reports | users | settings |
|------|-------|-------|---------|-------|----------|
| **Agent** | 2 (write) | 2 (write) | 1 (read) | 0 (none) | 0 (none) |
| **Supervisor** | 3 (admin) | 3 (admin) | 2 (write) | 1 (read) | 0 (none) |
| **Manager** | 3 (admin) | 3 (admin) | 3 (admin) | 2 (write) | 1 (read) |

### Supervisor Call Monitoring

Requires `calls:3` (admin) permission:

| Mode | Description | Supervisor Audio | Agent Hears | Lead Hears |
|------|-------------|------------------|-------------|------------|
| **Listen** | Silent observation | Receive only | No | No |
| **Whisper** | Coach agent | Send + Receive | Yes | No |
| **Barge** | Join conversation | Send + Receive | Yes | Yes |

### Multi-Tenant Isolation

All permissions are **schema-aware**:
- Users can only access data within their tenant schema
- Cross-tenant operations require system-level access
- Audit logs track all permission-based actions

---

## 12. Configuration Options

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
| Lead statuses | Available statuses | Per tenant |
| Assignment duration | How long lead stays assigned | 30 days |

---

## 13. Edge Cases

### Unanswered Call Outcomes

| Outcome | Detection | Behavior |
|---------|-----------|----------|
| No answer (rings out) | Auto-dialer | Counts as attempt → Back to cadence |
| Voicemail/machine | AMD | Counts as attempt → Back to cadence |
| Busy signal | Cannot detect | Counts as attempt → Back to cadence |
| Disconnected number | Cannot detect | Cycles through cadence until agent manually identifies |

**AMD Limitation:** Auto-dialer can only detect human vs machine. Cannot distinguish between no answer, busy, or disconnected.

### Concurrency

| Scenario | Behavior |
|----------|----------|
| Manager changes priority while lead is in queue | Lead is reprioritized |
| Manager changes priority while lead is being dialed | No effect - lead is "locked" |
| Manager changes priority while lead is connected | No effect - lead is locked |

**Lead Lock Point:** A lead becomes "locked" (immune to reprioritization) once dialing begins.

### Service Failures

| Scenario | Behavior |
|----------|----------|
| Auto-dialer service fails | Manual dial still available |
| Outbound service fails | Inbound still works (independent) |
| Inbound service fails | Outbound still works (independent) |

**Degraded mode:** Services operate independently.

---

## 14. Prospect Stage Rules

> **DOCUMENTATION GAP**: Detailed business rules for the Prospect stage need to be documented. The high-level workflow is in `user-journeys.md`.

### Known Substages

| Substage | Owner | Next Step |
|----------|-------|-----------|
| Intake Scheduled | DVS Agent | Intake Completed |
| Intake Completed | DVS Agent | Under Review |
| Under Review | Legal Advisor | Consultation Scheduled |
| Consultation Scheduled | Legal + Retainer | Consultation Completed |
| Consultation Completed | Retainer Team | Converted / Not Converted |

### Conversion Outcomes

| Status | Description | Next Step |
|--------|-------------|-----------|
| **Converted** | Client agrees to proceed | Assign to Case Manager |
| **Retained** | Signed and paid retainer | Open Case created |
| **Not Converted** | Client declines | Re-engagement flow |

### Rules to Document

- [ ] Prospect stage timeout rules
- [ ] Intake scheduling SLAs
- [ ] Under Review timeout and escalation
- [ ] Consultation no-show handling
- [ ] Re-engagement flow triggers and cadence
- [ ] DVS Agent assignment rules

---

## 15. Case Stage Rules

> **DOCUMENTATION GAP**: Detailed business rules for the Case stage need to be documented. The high-level workflow is in `user-journeys.md`.

### Known Workflow Stages

```
SALE PROCESS → GATHERING → PROCESSING → REVIEW → COMPLETE
     ↓              ↓           ↓           ↓
 Compliance    Compliance   Compliance  Compliance
  Revision      Revision     Revision    Revision
```

### Agent Specialist Types

| Specialist | Responsibilities |
|------------|------------------|
| Follow-Up (FU) | Document collection, questionnaires |
| Attorney (ATTY) | Legal questionnaire, WC checklist |
| Psychologist (PSYCH) | Client interviews, evidence gathering |
| Writer (WAE) | Declaration writing, evidence packages |
| Coordinator (PCC) | Packet compilation |

### Rules to Document

- [ ] Case stage transition triggers
- [ ] Compliance checkpoint rules
- [ ] Agent Specialist assignment rules
- [ ] Task assignment and handoff rules
- [ ] RFE handling rules
- [ ] Case stage timeout and escalation rules
- [ ] Case close/reopen rules
- [ ] SLA definitions per stage

---

## Quick Reference

| Rule | Value | Notes |
|------|-------|-------|
| Wrap-up timeout | 10 minutes | Agent -> Online on expiry |
| Notification timeout | Configurable | Lead recycled on expiry |
| Auto-assignment trigger | PREQUALIFIED, PITCHED | Stage-based |
| Monitoring permission | calls:3 | Supervisor+ only |
| Default outcome | NOT_SET | No stage change |
| Session timeout | 30 minutes | Auto-logout |
| Assignment duration | 30 days | Lead ownership |
| Response time target | < 60 seconds | Industry standard |
| Queue priority | Recycled > Priority 0 > Normal | By next attempt date |
