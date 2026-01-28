# Product Knowledge Base

This directory contains accumulated product knowledge that grows with each PRD.

## Contents

| File | Purpose |
|------|---------|
| `business-rules.md` | Core business logic and constraints |
| `user-roles.md` | User types, roles, and permission patterns |
| `user-journeys.md` | Key user flows and paths |
| `integrations.md` | External systems and touchpoints |

## How It Works

1. When creating a new PRD, the system scans this context silently
2. Context informs smarter questions and better PRDs
3. After PRD creation, new learnings are extracted back here
4. Over time, interviews get shorter as the knowledge base grows

## Adding New Context

Use `/compound-context [prd-path]` after creating a PRD to extract learnings.
