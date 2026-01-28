---
title: Case Contacts Management
status: Draft
created: 2026-01-19
updated: 2026-01-27
owner: Product
---

# Case Contacts Management

## Executive Summary

Case Contacts Management enables law firm agents to associate multiple contacts (witnesses, opposing parties, attorneys, etc.) with a case and track all communications through LawOS's existing Omnichannel inbox. Currently, agents must use external tools to contact anyone besides the primary client, creating gaps in case history and compliance tracking. This initiative extends Omnichannel to bring all case-related communications into a single auditable view.

---

## Problem Statement

### The Problem

When specialized agents need to contact case-related parties (witnesses, opposing counsel, medical providers), they must:
1. Dial phone numbers directly or use external software
2. Manually document the interaction in case notes
3. Hope recordings/transcripts are stored somewhere accessible

This creates:
- **Compliance risk**: Communications not properly logged or retained
- **Lost context**: No link between calls/texts and the specific case
- **Inefficiency**: Agents switching between tools, double-entering data
- **Audit gaps**: Cannot reconstruct full case communication history

### Who Feels It

| Persona | Pain Level | Impact |
|---------|------------|--------|
| **Specialized Agents** | High | Must use workarounds to contact witnesses, opposing parties |
| **Case Managers** | Medium | Cannot see full communication picture for a case |
| **Compliance Officers** | High | Cannot audit all case-related communications |
| **Firm Administrators** | Medium | Fragmented reporting, potential liability exposure |

### Current State

**What exists:**
- `Contact` model with `contact_type` enum (CLIENT, PROSPECT, LEAD, OPPOSING_PARTY, WITNESS, ATTORNEY, OTHER)
- `ContactPhone` model supports multiple phone numbers per contact
- `CallHistory.matter_id` - stores MyCase case ID as string
- `SMSHistory.case_number` - stores MyCase case ID as string (indexed)

**The gap:**
- MyCase Looker inline display only shows communications for the **primary client**
- Omnichannel inbox cannot be used in MyCase Looker - it only queries by `lead_id`, not by case
- No way to associate **other contacts** (witnesses, opposing counsel, experts) with a case
- If an agent calls a witness, that call doesn't appear anywhere in the case context

---

## Features

### 1. Case Contacts Data Model (P1 - Foundation)

**Objective:** Create the data models (CaseContact junction table, ContactRole) and extend existing CallHistory/SMSHistory models to support linking contacts to cases with defined roles.

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| US-001 | Create CaseContact junction model | P1 |
| US-002 | Create ContactRole model with platform defaults | P1 |
| US-003 | Extend CallHistory with case_contact_id | P1 |
| US-004 | Extend SMSHistory with case_contact_id | P1 |

**Platform Default Roles:** Witness, Expert Witness, Opposing Party, Opposing Counsel, Adjuster, Medical Provider, Other

**Acceptance Criteria:**
- [ ] Contacts can be associated with cases via CaseContact model
- [ ] Each association has a defined role from ContactRole
- [ ] Platform default roles exist
- [ ] Call and SMS history can be attributed to specific case contacts
- [ ] Queries by case ID are performant (indexed)

---

### 2. Case Contact Panel (P1)

**Objective:** Build a UI panel within MyCase Looker case detail view that displays all contacts associated with a case, allows agents to add/remove contacts with roles, and provides click-to-call/text functionality.

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| US-001 | View all contacts for a case in panel | P1 |
| US-002 | Add contact to case with role | P1 |
| US-003 | Click-to-call case contact | P1 |
| US-004 | Click-to-text case contact | P1 |
| US-005 | Remove contact from case | P2 |

**API Endpoints:**
```
GET  /api/cases/{mycase_case_id}/contacts/
POST /api/cases/{mycase_case_id}/contacts/
DELETE /api/cases/{mycase_case_id}/contacts/{id}/
GET  /api/contact-roles/
```

**Acceptance Criteria:**
- [ ] Agents can see all contacts associated with a case in MyCase Looker
- [ ] Agents can add new contacts to a case with a defined role
- [ ] Agents can initiate calls to case contacts directly from the panel
- [ ] Agents can send SMS to case contacts directly from the panel
- [ ] All communications are logged with case_contact_id
- [ ] Panel loads in under 500ms with up to 50 contacts

---

### 3. Omnichannel Case Extension (P1)

**Objective:** Extend the existing Omnichannel inbox to support case-based querying and auto-scope to the current case when viewed within MyCase Looker, displaying all communications across all case contacts.

**User Stories:**

| ID | Story | Priority |
|----|-------|----------|
| US-001 | Query Omnichannel by case ID | P1 |
| US-002 | Auto-scope inbox when viewing case | P1 |
| US-003 | Display contact name and role for each communication | P1 |
| US-004 | Maintain backwards compatibility for old records | P1 |

**Query Strategy:**
- Lead-based (existing): Query by `lead_id`
- Case-based (new): Query by `mycase_case_id` using `CallHistory.matter_id` and `SMSHistory.case_number`

**API Extension:**
```
GET /api/omnichannel/conversations/?case_id={mycase_case_id}
```

**Acceptance Criteria:**
- [ ] Omnichannel inbox can query by case ID (in addition to lead ID)
- [ ] Inbox auto-scopes to current case when viewing in MyCase Looker
- [ ] Communications show contact name and role (e.g., "Jane Smith - Witness")
- [ ] Old records without case_contact_id still appear (backwards compatible)
- [ ] Existing lead-based Omnichannel functionality unchanged

---

## Dependency Structure

```
Data Model (Foundation)
    ├── blocks → Contact Panel (needs CaseContact model)
    └── blocks → Omnichannel Extension (needs case_contact_id on history models)

Contact Panel ←→ Omnichannel Extension (can be developed in parallel after Data Model)
```

---

## Key Capabilities Summary

| Capability | Description | Priority |
|------------|-------------|----------|
| **Case Contact Association** | Link any contact to a case with a defined role | P1 |
| **Case Contact Panel** | UI within case view showing all associated contacts with click-to-call | P1 |
| **Contact Communication Logging** | All calls/texts to case contacts logged with case reference | P1 |
| **Omnichannel Extension** | Extend inbox to query by case (not just lead); auto-scope when viewing a case | P1 |
| **Dynamic Contact Types** | Platform defaults + tenant-custom contact types | P2 |
| **Follow-up Task Creation** | Create tasks from contact interactions | P2 |
| **Document Linking** | Associate documents with specific contact interactions | P3 |

### User Journey

**Scenario: Agent needs to contact a witness**

1. Agent opens case #12345 in LawOS (MyCase Looker)
2. Omnichannel inbox within case view is automatically scoped to this case
3. Agent sees all communications for case #12345 (client + witness + opposing counsel)
4. Agent clicks "Contacts" panel to see list of case contacts with roles
5. Agent clicks phone icon next to "Jane Smith - Witness"
6. Softphone initiates call, pre-populated with case context
7. Call is logged with: contact name, role, case reference, recording, duration
8. Call immediately appears in the case's Omnichannel inbox alongside other communications

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Specialized Agent | to see all contacts associated with a case | I know who to reach out to without searching multiple systems |
| P1 | Specialized Agent | to call/text case contacts directly from LawOS | all communications are automatically logged to the case |
| P1 | Case Manager | to see all case contact communications in Omnichannel inbox | I understand the full history regardless of which contact was involved |
| P2 | Firm Admin | to define custom contact types for our practice area | our workflow matches how we categorize case parties |
| P2 | Compliance Officer | to audit all communications for a case | I can verify retention and documentation requirements are met |
| P2 | Specialized Agent | to create follow-up tasks from contact interactions | I don't forget promised callbacks or document requests |
| P3 | Case Manager | to see which documents were shared with which contacts | I can track information flow for discovery/privilege purposes |

---

## User Permissions

| Role | Can View Case Contacts | Can Add Contacts | Can Call/Text | Can Delete |
|------|------------------------|------------------|---------------|------------|
| Agent Specialist | Yes (assigned cases) | Yes | Yes | No |
| Paralegal | Yes (assigned cases) | Yes | Yes | No |
| Attorney | Yes (all cases) | Yes | Yes | Yes |
| Supervisor | Yes (team cases) | Yes | Yes | Yes |
| Manager | Yes (all cases) | Yes | Yes | Yes |

**Rule:** If user can access the case, they can see all its contacts (no row-level permissions on CaseContact)

---

## Test Scenarios

| Scenario | User Action | Expected Outcome |
|----------|-------------|------------------|
| **Add witness to case** | Agent clicks "Add Contact", selects existing contact, chooses "Witness" role | Contact appears in Case Contacts panel with Witness badge |
| **Call case contact** | Agent clicks phone icon on contact in panel | Softphone opens, call is pre-tagged with case ID and contact role |
| **View case communications** | Agent opens case with 3 contacts | Omnichannel inbox shows all communications across all 3 contacts |
| **Filter by contact** | Agent clicks specific contact in panel | Inbox filters to show only that contact's communications |
| **No contacts state** | Agent opens case with no associated contacts | "No contacts" message with "Add Contact" button |
| **Duplicate prevention** | Agent tries to add same contact twice | Error: "Contact already associated with this case" |
| **Contact in multiple cases** | Same person is witness in Case A and opposing party in Case B | Shows correct role in each case's contact panel |

---

## Success Metrics

| Metric | Current | Target | Measurement |
|--------|---------|--------|-------------|
| % of case communications logged in LawOS | ~60% (client only) | 95%+ | Audit sample vs external records |
| Time to locate witness contact info | Manual lookup | < 3 seconds | Task timing studies |
| Calls made outside LawOS for case contacts | Unknown | 0 | Agent workflow surveys |

---

## Out of Scope

- **New timeline UI** - Use existing Omnichannel inbox
- **Contact-level notes** - Use existing case notes
- **Portal access for case contacts** - Witnesses don't get accounts
- **Inbound call routing to case contacts** - Outbound-focused
- **MyCase sync** - Manual add only (decoupling from MyCase)
- **Automated contact discovery** - No AI extraction from documents
- **SMS consent management for witnesses** - Assume agent discretion

---

## Dependencies

| Dependency | Type | Status |
|------------|------|--------|
| Existing Contact model | Technical | Complete |
| CallHistory.matter_id | Technical | Complete |
| SMSHistory.case_number | Technical | Complete |
| Softphone infrastructure | Technical | Complete |
| Omnichannel inbox | Technical | Complete (needs extension) |

---

## Risks

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Contact type proliferation | Medium | Medium | Provide sensible defaults; limit custom types per tenant |
| Performance with many contacts | Medium | Low | Pagination, lazy loading, indexed queries |
| Agent adoption | Medium | Medium | Training, make it easier than workaround |

---

## Appendix: Data Model

```
CaseContact (junction table - NEW)
├── mycase_case_id (CharField - indexed)
├── contact_id (FK to Contact)
├── contact_role_id (FK to ContactRole)
├── added_by_user_id
├── created_at / updated_at
├── Unique constraint: (mycase_case_id, contact_id)

ContactRole (tenant-scoped - NEW)
├── name (e.g., "Witness", "Expert Witness", "Adjuster")
├── organization_id (nullable for platform defaults)
├── is_default (platform-provided types)
├── display_order
├── is_active

CallHistory (extend existing)
├── matter_id (EXISTING)
├── case_contact_id (NEW - nullable FK to CaseContact)

SMSHistory (extend existing)
├── case_number (EXISTING)
├── case_contact_id (NEW - nullable FK to CaseContact)
```
