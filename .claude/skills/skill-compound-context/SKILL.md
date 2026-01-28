# Compound Context Skill

Extract product learnings from PRDs and add to project context.

## When to Use

- `/compound-context [prd-path]`
- After creating a PRD: "Should I extract learnings to the knowledge base?"

## What It Extracts

Scans the PRD for reusable product knowledge:

1. **New user roles or permissions** → Add to `context/user-roles.md`
2. **Business rules** → Add to `context/business-rules.md`
3. **User journey patterns** → Add to `context/user-journeys.md`
4. **Integration touchpoints** → Add to `context/integrations.md`
5. **New terminology or concepts** → Add to relevant context file

## Workflow

### Step 1: Analyze PRD

Read the PRD and identify extractable product knowledge.

### Step 2: Show Proposed Additions

"I found these learnings to add to context:"
- [Learning 1] → `context/business-rules.md`
- [Learning 2] → `context/user-roles.md`

### Step 3: Confirm and Update

On approval, append learnings to relevant context files.

Use YAML frontmatter for searchability:

```yaml
---
source_prd: [prd-name]
date_added: YYYY-MM-DD
---
```

### Step 4: Summary

Report what was added:
- "Added 2 business rules to context/business-rules.md"
- "Added 1 new role to context/user-roles.md"

## Example

From a "Client Portal" PRD, extract:
- New role "Portal User" with read-only access → `user-roles.md`
- Rule "Clients can only see their own cases" → `business-rules.md`
- Integration with document storage → `integrations.md`
