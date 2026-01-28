---
domain: platform
type: user-journeys
last_updated: 2026-01-27
source: lawos-platform
---

# LawOS User Journeys

User flows and personas for TramCase/LawOS - from lead acquisition through case closure.

## 1. User Types & Roles

| Role | Primary Goal | Modules They Use | Access Level |
|------|-------------|------------------|--------------|
| **Opener Agent** | Convert leads into qualified prospects | Lead Center, Softphone, SMS Messaging | Leads (R/W), Calls (R/W), Reports (RO) |
| **DVS Agent** | Schedule and conduct client intakes | DVS Prospects Module, Documents | Prospects (R/W), Scheduling, Documents |
| **Legal Advisor** | Review cases and provide legal analysis | Prospects (Under Review), Case Consultation | Case Review, Legal Recommendations |
| **Retainer Specialist** | Convert qualified prospects to paying clients | Retainer Module, Not Converted follow-up | Conversion tracking, Contract management |
| **Paralegal** | Manage case documentation and workflow | MyCase Looker, Documents, Case Notes | Case Data (R/W), Documents (R/W) |
| **Agent Specialist** | Execute case-specific tasks | Case Module, Documents, Forms, Questionnaires | Case Data (R/W), Documents (R/W), Assigned Tasks |
| **Attorney** | Handle legal case work | MyCase Looker, Documents, Court dates | Full case access |
| **Supervisor** | Monitor team performance and assist agents | Supervisor Dashboard, Call monitoring | Full access to team data, Reports (R/W) |
| **Manager** | Configure system and manage operations | All modules including Configuration | Complete system access |
| **Team Lead** | Coordinate daily team activities | Lead distribution, Team metrics | Team-specific access |

### Permission Matrix

| Capability | Agent | Supervisor | Manager |
|-----------|-------|------------|---------|
| View assigned leads | Yes | All leads | All leads |
| Make/receive calls | Yes | + Monitor | + Monitor |
| Send SMS | Yes | Yes | Yes |
| Opt-out override | No | Yes | Yes |
| View reports | Read-only | Read/Write | Full access |
| User management | No | Read-only | Read/Write |
| System configuration | No | No | Full access |
| Lead reassignment | No | Yes | Yes |
| Call recording access | Own calls | Team calls | All calls |

---

## 2. Lead-to-Case Journey

The complete client journey flows through three main stages: **Lead -> Prospect -> Open Case**

### Stage 1: Lead (Lead Center Module)

**Duration:** 13-15 days maximum
**Responsible Team:** Opener Agents

#### Lead Sources

| Source | Mechanism | Volume |
|--------|-----------|--------|
| **Meta/Facebook campaigns** | API integration, automatic ingestion | Primary source |
| **Website forms** | Direct submission to LawOS | Secondary source |
| **Inbound calls** | Auto-created lead on call receipt | Organic + paid |
| **Referrals** | Manual lead creation by agents | Internal referrals |

#### Lead Distribution (Round-Robin)

- Only agents with status "Available" receive new leads
- Rotation maintains fairness across all available agents
- Recycled leads (not contacted) go to priority queue and skip previous agent
- System tracks `lastAssignedIndex` for rotation state

#### Follow-up Cadence

| Day | Action |
|-----|--------|
| Day 0 | Initial call + SMS (automated) |
| Day 1 | Second call + SMS |
| Day 3 | Third call + SMS |
| Day 5 | Fourth call + SMS |
| Day 13 | Final call + SMS |

#### Lead Qualification Outcomes

**Successful Outcomes - Move to Prospect Stage:**

| Status | Description | Next Step |
|--------|-------------|-----------|
| **Consult Booked** | Lead agrees to consultation | Direct conversion to prospect |
| **Preclosed** | Qualified, requests follow-up | 7-day window, then DVS |
| **Awaiting Docs** | Qualified but needs documentation | 7-day window for doc collection |

**Final Outcomes (Exit Lead Stage):**

| Status | Description | Action |
|--------|-------------|--------|
| **Unable to Contact** | No response after full cadence | Archive |
| **Disqualified** | Doesn't meet criteria or opted out | Archive with reason |
| **Wrong/Bad Number** | Invalid contact info | Archive |

---

### Stage 2: Prospect (Intake & Consultation Module)

**Duration:** Variable (typically 1-4 weeks)
**Responsible Teams:** DVS Agents -> Legal Advisors -> Retainer Specialists

#### Intake Process
1. Schedule initial consultation meeting
2. Collect preliminary case information
3. Upload required documents
4. Conduct detailed intake interview
5. Document case details for legal review

#### Legal Advisory Review
- Review intake documentation
- Assess case viability and legal strategy
- Determine case value and complexity
- Provide internal recommendation

#### Conversion Decision

| Status | Description | Next Step |
|--------|-------------|-----------|
| **Converted** | Client agrees to proceed | Assigned to Case Manager |
| **Retained** | Client signed and paid retainer | Open Case created |
| **Not Converted** | Client declines service | May enter re-engagement flow |

---

### Stage 3: Open Case (Case Manager Module)

**Duration:** Months to years (case lifecycle)
**Responsible Teams:** Agent Specialists, Paralegals, Attorneys

#### Case Workflow Stages

| Stage | Activities | Key Agent Specialists |
|-------|------------|----------------------|
| **Sale Process** | Initial case setup after conversion | SCC (Sales Case Coordinator) |
| **Gathering** | Document collection, interviews, evidence gathering | FU, ATTY, PSYCH |
| **Processing** | Case analysis, declaration writing, packet assembly | WAE, PCC |
| **Review Process** | Case review, attorney sign-off | Case Review, Attorney Review |

#### Agent Specialist Types

| Specialist Type | Typical Responsibilities | Outputs |
|-----------------|-------------------------|---------|
| **Follow-Up (FU)** | Collect required documents from clients, complete questionnaires | Documents, Questionnaires |
| **Attorney (ATTY)** | Complete legal questionnaire, work condition checklist | Legal forms, WC Checklist |
| **Psychologist (PSYCH)** | Conduct client interviews, gather supporting evidence | Interview transcripts, Evidence |
| **Writer Analyst Expert (WAE)** | Analyze transcripts, write declarations and cover letters | DCL (Declarations), Evidence packages |
| **Packet Coordinator (PCC)** | Compile final packet with all forms and documents | Full Packet, PCO Form |

---

## 3. Supervisor/Admin Journeys

### Supervisor Daily Activities

#### Real-Time Team Monitoring

| Activity | Description | Frequency |
|----------|-------------|-----------|
| **Agent Status Review** | View all agents' current status | Continuous (5-second refresh) |
| **Call Monitoring** | Listen to live calls for quality assurance | As needed |
| **Queue Management** | Monitor lead queue depth | Throughout shift |
| **Lead Reassignment** | Manually redistribute leads when needed | As needed |

#### Intervention Scenarios

1. **Opt-Out Recovery** - Only supervisors can click "Opt Back In"
2. **Lead Escalation** - Agent unable to handle complex inquiry
3. **Quality Issues** - Review call recordings, provide coaching

### Manager Daily Activities

#### System Configuration
- User Management: Add/remove users, adjust roles
- Team Configuration: Manage team assignments, SIP trunks
- Distribution Settings: Adjust round-robin parameters
- Phone Number Management: Assign numbers to teams/agents

---

## 4. Edge Cases & Error Handling

### Lead Can't Be Reached
- Auto-status: "Unable to Contact" after full cadence
- Lead archived with full attempt history
- Available for future retargeting campaigns

### Agent is Unavailable
- Lead enters FIFO Queue
- Priority Queue (recycled leads) processed first
- First agent to set status "Available" receives oldest queued lead

### System Errors & Recovery

| Issue | Solution |
|-------|----------|
| No audio | Check browser microphone permissions |
| Call won't connect | Verify network stability, try again |
| SMS send failure | System auto-retry (3 attempts), then escalate |

### Opt-Out Handling

When lead responds "STOP":
1. System auto-marks as "Opted Out"
2. Yellow warning banner displayed
3. Agent cannot send additional SMS
4. Only Supervisor can reactivate

---

## 5. Key Metrics

### Lead Stage Metrics

| Metric | Target |
|--------|--------|
| Response Time | < 60 seconds |
| Contact Success Rate | > 80% |
| Pipeline Velocity | < 15 days in lead stage |

### Prospect Stage Metrics

| Metric | Description |
|--------|-------------|
| Intake Completion Rate | % of scheduled intakes completed |
| Consultation Show Rate | % of scheduled consultations attended |
| Conversion Rate | Prospect to Case conversion |

### Case Stage Metrics

| Metric | Description |
|--------|-------------|
| Case Duration | Average time to case closure |
| Compliance Rate | % of deadlines met |
| Document Completion | % of required docs collected |

---

## 6. Data Continuity & Compliance

### Audit Trail
- All SMS messages with timestamps and delivery status
- Call recordings with duration
- Agent notes with author attribution
- Status changes with timestamps
- Document uploads with metadata

### Communication Continuity

| Transition | Data Migrated |
|-----------|---------------|
| Lead to Prospect | Full contact history, notes, documents |
| Prospect to Case | All prospect data, intake records, legal analysis |

### Compliance Features
- TCPA Compliance: Automated consent tracking, opt-out management
- Data Security: Centralized secure storage, role-based access
- Access Controls: Permissions enforced at API and UI levels
- Audit Requirements: Complete interaction history retained
