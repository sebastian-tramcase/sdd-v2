---
domain: users
type: user-roles
last_updated: 2026-01-27
source: lawos-platform
---

# LawOS User Roles

User personas and role definitions for TramCase/LawOS.

## Role Hierarchy

```
Manager
  └── Supervisor
       └── Team Lead
            └── Agent (various types)
```

## Role Definitions

### Opener Agent
**Primary Goal:** Convert leads into qualified prospects

| Attribute | Value |
|-----------|-------|
| Modules | Lead Center, Softphone, SMS Messaging |
| Leads | Read/Write |
| Calls | Read/Write |
| Reports | Read-only |
| Users | None |
| Settings | None |

### DVS Agent
**Primary Goal:** Schedule and conduct client intakes

| Attribute | Value |
|-----------|-------|
| Modules | DVS Prospects Module, Documents |
| Prospects | Read/Write |
| Scheduling | Full access |
| Documents | Read/Write |

### Legal Advisor
**Primary Goal:** Review cases and provide legal analysis

| Attribute | Value |
|-----------|-------|
| Modules | Prospects (Under Review), Case Consultation |
| Case Review | Full access |
| Legal Recommendations | Full access |

### Retainer Specialist
**Primary Goal:** Convert qualified prospects to paying clients

| Attribute | Value |
|-----------|-------|
| Modules | Retainer Module, Not Converted follow-up |
| Conversion Tracking | Full access |
| Contract Management | Full access |

### Paralegal
**Primary Goal:** Manage case documentation and workflow

| Attribute | Value |
|-----------|-------|
| Modules | MyCase Looker, Documents, Case Notes |
| Case Data | Read/Write |
| Documents | Read/Write |

### Agent Specialist
**Primary Goal:** Execute case-specific tasks

| Attribute | Value |
|-----------|-------|
| Modules | Case Module, Documents, Forms, Questionnaires |
| Case Data | Read/Write |
| Documents | Read/Write |
| Assigned Tasks | Full access |

**Specialist Types:**
- Follow-Up (FU)
- Attorney (ATTY)
- Psychologist (PSYCH)
- Writer Analyst Expert (WAE)
- Packet Coordinator (PCC)

### Attorney
**Primary Goal:** Handle legal case work

| Attribute | Value |
|-----------|-------|
| Modules | MyCase Looker, Documents, Court dates |
| Case Access | Full |

### Supervisor
**Primary Goal:** Monitor team performance and assist agents

| Attribute | Value |
|-----------|-------|
| Modules | Supervisor Dashboard, All team leads, Call monitoring |
| Team Data | Full access |
| Reports | Read/Write |
| User Status | Read-only |
| Call Monitoring | Listen/Whisper/Barge |
| Opt-out Override | Yes |
| Lead Reassignment | Yes |

### Manager
**Primary Goal:** Configure system and manage operations

| Attribute | Value |
|-----------|-------|
| Modules | All modules including Configuration |
| System Access | Complete |
| User Management | Read/Write |
| Configuration | Read/Write |

### Team Lead
**Primary Goal:** Coordinate daily team activities

| Attribute | Value |
|-----------|-------|
| Modules | Lead distribution, Team metrics |
| Access | Team-specific with distribution controls |

---

## Permission Levels

| Level | Name | Description |
|-------|------|-------------|
| 0 | None | No access |
| 1 | Read | View only |
| 2 | Write | Create and modify |
| 3 | Admin | Full control including delete |

## Default Role Permissions

| Role | leads | calls | reports | users | settings |
|------|-------|-------|---------|-------|----------|
| **Agent** | 2 | 2 | 1 | 0 | 0 |
| **Supervisor** | 3 | 3 | 2 | 1 | 0 |
| **Manager** | 3 | 3 | 3 | 2 | 1 |
