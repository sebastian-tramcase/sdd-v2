# Product Knowledge Base

This folder contains accumulated product knowledge that makes PRD creation faster and smarter over time.

## Files

| File | Domain | Description |
|------|--------|-------------|
| `business-rules.md` | Platform | Lead distribution, qualification criteria, status transitions, timeouts, permissions |
| `user-roles.md` | Users | Role definitions, capabilities, permission levels |
| `user-journeys.md` | Flows | Complete user flows from lead to case, supervisor/admin journeys |
| `integrations.md` | Technical | Service dependencies, API contracts, external integrations |

## How This Works

When you create a new PRD, Claude silently scans this context to:
- Skip questions it already knows the answer to
- Ask smarter, more specific questions
- Pre-fill sections with accurate information
- Catch inconsistencies with existing product knowledge

## Adding Knowledge

After creating a PRD, use `/compound-context [prd-path]` to extract learnings:
- New business rules
- New user roles or permissions
- New integration touchpoints
- Updated user journeys

## Searching Context

Use YAML frontmatter to find relevant knowledge:

```yaml
---
domain: platform       # Filter by domain
type: business-rules   # Filter by type
---
```

Domains: `platform`, `users`, `flows`, `technical`
Types: `business-rules`, `user-roles`, `user-journeys`, `integrations`
