---
domain: platform
type: integrations
last_updated: 2026-01-27
source: lawos-platform
---

# LawOS Integration Map

Service dependencies, data ownership, and API contracts for the LawOS platform.

## 1. Service Dependency Matrix

### Dependency Table

| Service | Hard Dependencies | Soft Dependencies | Reads From | Writes To |
|---------|-------------------|-------------------|------------|-----------|
| **Auth** | PostgreSQL | Redis (sessions) | User, Organization | User, Session |
| **Tenant Resolution** | PostgreSQL, Auth | - | OrganizationDomain | - |
| **RBAC** | PostgreSQL, Auth | Redis (cache) | Team, Role, UserRole | Permission cache |
| **Matters** | Contacts, Auth, RBAC | Events | Contact, Customer | Matter, MatterContact |
| **Contacts** | Auth, RBAC, Tenants | Events | - | Contact |
| **Customers** | Auth, Tenants | Events | Contact | Customer, CustomerContact |
| **Channels** | Matters, Contacts | Messages | Matter, Contact | Channel |
| **Messages** | Channels | - | Channel | Message |
| **Tasks** | Matters | Assignments | Matter | Task |
| **Notes** | Matters | - | Matter | Note |
| **Documents** | Matters, S3 | - | Matter | Document |
| **Assignments** | Tasks, RBAC | - | Task, User | Assignment |
| **Events** | Auth, Tenants | - | All entities | Event |
| **Notifications** | Events | Email, SMS | Event | Notification |

---

## 2. Data Ownership Map

### Database Table Ownership by Service

| Table | Owner | Creates | Reads | Updates | Deletes |
|-------|-------|---------|-------|---------|---------|
| **Public Schema** |
| `User` | Auth | Auth, SSO | All services | Auth, Profile | Never |
| `Organization` | Platform | Admin | Tenant resolver | Admin | Never |
| **Tenant Schema - Core** |
| `Matter` | Matters | Intake, API | All services | Case Mgmt, API | Soft only |
| `Contact` | Contacts | Intake, CRM | Matters, Channels | CRM, API | Soft only |
| `Customer` | Billing | Billing, API | Matters, Invoicing | Billing, API | Soft only |
| **Tenant Schema - Communication** |
| `Channel` | Channels | Messaging, Intake | Messaging, UI | Messaging | Soft only |
| `Message` | Messages | All channels | Channels, Search | Messaging | Soft only |
| **Tenant Schema - Work** |
| `Task` | Tasks | All services | Task lists, Reports | Task service | Soft only |
| `Note` | Notes | All services | Case view, Search | Notes service | Soft only |
| `Document` | Documents | Upload, API | All services | Docs service | Soft only |

### Ownership Rules

1. **Single Writer**: Each table has ONE primary service that writes to it
2. **Soft Deletes**: All tenant data uses `is_deleted` flag - no hard deletes
3. **Immutable Audit**: Events table is append-only, never updated or deleted
4. **Cross-Service Reads**: Any service can read data it needs (after auth check)

---

## 3. API Contracts Summary

### Standard REST Pattern

All services follow this pattern:

```
GET    /api/v1/{service}/                    # List with filters
POST   /api/v1/{service}/                    # Create
GET    /api/v1/{service}/{id}/               # Retrieve
PUT    /api/v1/{service}/{id}/               # Update (data only)
DELETE /api/v1/{service}/{id}/               # Soft delete
PUT    /api/v1/{service}/{id}/archive        # Archive
PUT    /api/v1/{service}/{id}/restore        # Restore
```

### Key Endpoints by Service

| Service | Key Endpoints | Purpose |
|---------|---------------|---------|
| **Matters** | `GET /matters/?stage=lead` | Filter leads |
| | `PUT /matters/{id}/stage` | Stage transitions |
| | `GET /matters/{id}/timeline` | Activity history |
| **Contacts** | `GET /contacts/?search_channels=` | Multi-channel search |
| | `GET /contacts/duplicates` | Deduplication |
| **Channels** | `GET /channels/{id}/messages` | Conversation thread |
| | `POST /channels/{id}/messages` | Send message |
| **Messages** | `GET /messages/?context_type=` | Polymorphic query |
| | `GET /messages/search?q=` | Full-text search |
| **Tasks** | `PUT /tasks/{id}/assign` | Task assignment |
| | `PUT /tasks/{id}/complete` | Mark complete |
| **Events** | `GET /{service}/{id}/events` | Entity audit trail |

---

## 4. Event Flow

### Event Generation

| Source | Trigger Type | Events Generated |
|--------|--------------|------------------|
| Database Changes | SQL Triggers | `entity.created`, `entity.updated`, `entity.deleted` |
| Business Logic | Service Layer | `matter.stage_changed`, `task.completed`, `assignment.created` |
| External | Webhooks | `integration.synced`, `payment.received` |
| User Actions | API Calls | `document.viewed`, `message.sent` |

### Key Event Types

| Event | Triggers | Downstream Effects |
|-------|----------|-------------------|
| `matter.created` | Matter creation | Contact link, assignment, channel creation |
| `matter.stage_changed` | Stage update | Notifications, workflow triggers |
| `contact.created` | Contact creation | Deduplication check |
| `task.completed` | Task completion | Parent matter update, notifications |
| `message.sent` | Message creation | Notification, delivery tracking |
| `permission.changed` | RBAC update | Cache invalidation |

---

## 5. Deployment Dependencies

### Deployment Order

| Phase | Services | Dependencies |
|-------|----------|--------------|
| **0** | PostgreSQL, Redis, S3 | None (Infrastructure) |
| **1** | Auth, Tenant Resolution | Database |
| **2** | RBAC Service | Auth, Database, Cache |
| **3** | Events Service | Auth, Tenants |
| **4** | Contacts, Customers | Auth, RBAC, Events |
| **5** | Matters Service | Contacts, Customers, Events |
| **6** | Channels, Messages | Matters, Contacts |
| **7** | Tasks, Notes, Documents | Matters, Events |
| **8** | Assignments | Tasks, RBAC |
| **9** | Notifications | Events, All services |

---

## 6. External Integration Points

### Inbound Integrations

| Integration | Type | Purpose | Entry Point |
|-------------|------|---------|-------------|
| **Meta/Facebook** | Webhook | Lead capture | `/webhooks/meta/` |
| **Website Forms** | REST API | Lead submission | `/api/v1/leads/` |
| **Telnyx/Apeiron** | Webhook | SMS/Call events | `/webhooks/telephony/` |
| **LiveKit** | WebSocket | Call signaling | `/ws/livekit/` |
| **Azure AD** | OAuth 2.0 | SSO authentication | `/auth/sso/` |

### Outbound Integrations

| Integration | Type | Purpose | Trigger |
|-------------|------|---------|---------|
| **MyCase** | REST API | Case data sync | Scheduled job |
| **Telnyx/Apeiron** | REST API | Send SMS | Message service |
| **LiveKit** | REST API | Create rooms | Call service |
| **AWS S3** | SDK | Document storage | Document upload |

---

## 7. Tech Stack Summary

| Layer | Technology |
|-------|------------|
| **Backend** | Django + PostgreSQL |
| **Frontend** | React |
| **Cache** | Redis |
| **Storage** | AWS S3 |
| **Voice** | LiveKit WebRTC + SIP |
| **SMS** | Telnyx/Apeiron |
| **Auth** | Azure AD (SSO) |
