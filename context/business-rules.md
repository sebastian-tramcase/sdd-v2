---
domain: platform
type: business-rules
last_updated: 2026-01-27
source: lawos-platform
---

# LawOS Business Rules & Logic

Core business logic for TramCase/LawOS - a legal case management platform with integrated call center operations.

## 1. Lead Distribution Rules

### Assignment Model

LawOS uses a **dual-assignment model** to separate temporary call routing from permanent lead ownership:

| Field | Purpose | Persistence | Visibility |
|-------|---------|-------------|------------|
| `currently_assigned_agent` | Temporary assignment for active call routing | Clears after call ends | Hidden from UI |
| `assigned_agent` | Permanent lead ownership | Persists across all calls | Visible in UI |

### Assignment Triggers

#### Automatic Assignment
Leads are automatically assigned to an agent when:
1. **Sales Stage Advancement**: Lead's sales stage changes to `PREQUALIFIED` or `PITCHED`
2. **Call Outcome**: Agent selects a qualifying outcome during wrap-up

#### Manual Assignment
Agents can claim unassigned leads via:
- **"Claim Lead" checkbox** in the wrap-up modal
- Only visible when `lead.assigned_agent` is `None`

### Priority & Routing Logic

1. **Callback Queue**: Scheduled callbacks take highest priority
2. **New Leads**: Fresh leads distributed round-robin
3. **Recycled Leads**: Leads that were previously declined or timed out

### Non-Response Handling

| Scenario | Timeout | Action |
|----------|---------|--------|
| No Accept/Decline | 30 seconds | Lead recycled to queue, agent marked BUSY |
| Wrap-up timeout | 10 minutes | Agent auto-transitioned to BUSY status |
| Call drops | Immediate | Lead marked for callback |

---

## 2. Qualification Criteria

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

## 3. Status Transitions

### Sales Stage Progression

| Stage | Description | Entry Trigger | Requirements |
|-------|-------------|---------------|--------------|
| `NOT_SET` | Initial state | Lead creation | None |
| `PREQUALIFIED` | Lead qualified | `QUALIFIED` outcome | Successful contact |
| `PITCHED` | Price presented | Manual selection | Price amount entered |

### Agent Status Transitions

| Status | Description | Entry Triggers | Exit Triggers |
|--------|-------------|----------------|---------------|
| `OFFLINE` | Not logged in | Logout, session end | Login |
| `AVAILABLE` | Ready for calls | Go available, submit outcome | Accept call, go busy, logout |
| `ON_CALL` | Active call | Accept lead | Call ends |
| `WRAP_UP` | Post-call work | Call ends | Submit outcome, 10 min timeout |
| `BUSY` | Unavailable | Manual, timeout, decline | Go available |

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

---

## 4. Time-Based Rules

### Timeout Durations

| Context | Duration | Action on Timeout |
|---------|----------|-------------------|
| **Call Accept Window** | 30 seconds | Lead recycled, agent -> BUSY |
| **Wrap-Up Phase** | 10 minutes (600s) | Agent -> BUSY (not AVAILABLE) |
| **Wrap-Up Warning** | 9 minutes (540s) | Warning notification shown |
| **Session Idle** | 30 minutes | Auto-logout |
| **Callback Retry** | 24 hours | Lead re-enters queue |

**Important**: On timeout, outcome defaults to `NOT_SET` which does NOT trigger any sales stage transition, preserving the lead's current stage.

### Performance SLAs

| Operation | Target | Notes |
|-----------|--------|-------|
| WebSocket event handling | < 50ms | Agent status updates |
| LiveKit token generation | < 100ms | Call connection setup |
| Call connection time | < 2 seconds | End-to-end |
| UI responsiveness | < 100ms | User interactions |
| Lead distribution | < 5 seconds | Queue to assignment |

### Escalation Triggers

| Condition | Escalation Action |
|-----------|-------------------|
| Lead in queue > 5 minutes | Alert supervisors |
| Agent BUSY > 30 minutes | Manager notification |
| Failed calls > 3 consecutive | Lead flagged for review |
| Callback overdue > 48 hours | Escalate to supervisor queue |

---

## 5. Permission Model

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

### Permission Definitions

#### Leads Permissions
| Level | Capabilities |
|-------|--------------|
| 0 | No access to leads |
| 1 | View assigned leads only |
| 2 | View all leads, update assigned leads, claim unassigned |
| 3 | Full CRUD, reassign leads, bulk operations |

#### Calls Permissions
| Level | Capabilities |
|-------|--------------|
| 0 | No call access |
| 1 | View call history |
| 2 | Make calls, record outcomes |
| 3 | Monitor calls (Listen/Whisper/Barge), access recordings |

#### Reports Permissions
| Level | Capabilities |
|-------|--------------|
| 0 | No report access |
| 1 | View personal metrics |
| 2 | View team metrics, export reports |
| 3 | View all metrics, create custom reports |

### Supervisor Call Monitoring

Requires `calls:3` (admin) permission to access monitoring features:

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

## 6. Prospect Stage Rules

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

- [ ] Prospect stage timeout rules (how long can a prospect sit in each substage?)
- [ ] Intake scheduling SLAs
- [ ] Under Review timeout and escalation
- [ ] Consultation no-show handling
- [ ] Re-engagement flow triggers and cadence
- [ ] DVS Agent assignment rules

---

## 7. Case Stage Rules

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

- [ ] Case stage transition triggers (what moves a case from Gathering to Processing?)
- [ ] Compliance checkpoint rules (what must pass before advancing?)
- [ ] Agent Specialist assignment rules
- [ ] Task assignment and handoff rules
- [ ] RFE (Request for Evidence) handling rules
- [ ] Case stage timeout and escalation rules
- [ ] Case close/reopen rules
- [ ] SLA definitions per stage

---

## Quick Reference

| Rule | Value | Notes |
|------|-------|-------|
| Wrap-up timeout | 10 minutes | Agent -> BUSY on expiry |
| Call accept window | 30 seconds | Lead recycled on expiry |
| Auto-assignment trigger | PREQUALIFIED, PITCHED | Stage-based |
| Monitoring permission | calls:3 | Supervisor+ only |
| Default outcome | NOT_SET | No stage change |
| Session timeout | 30 minutes | Auto-logout |
