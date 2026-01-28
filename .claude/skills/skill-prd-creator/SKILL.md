# PRD Creator Skill

Create Product Requirements Documents focused on product understanding.

## When to Use

- User runs `/new-prd [name]`
- User says "create a PRD" or "new product spec"
- Starting a new product initiative
- Need to document requirements before development

## Document Hierarchy

```
PRD (Product Requirements)
  └── Can be broken into smaller PRDs if needed
```

All PRDs use the same template. Size varies by scope.

## Process

### Step 0: Context Scan (Silent)

Before asking questions, silently check existing knowledge:

1. **Check `context/`** for relevant product knowledge:
   - `business-rules.md` - existing rules that may apply
   - `user-journeys.md` - related user flows
   - `user-roles.md` - existing roles and permissions

2. **Check `prds/`** for related PRDs

**Do NOT ask user to confirm what you found.** Just use the knowledge to:
- Skip questions you already know the answer to
- Ask smarter, more specific questions
- Pre-fill sections of the PRD

If PRD with similar name exists, ask: "Update existing PRD or create new?"

### Step 1: Discovery Interview

Have a natural conversation. Use context to skip questions you already know.

**Core questions (ask what you don't know):**
1. "What problem are we solving and who feels this pain?"
2. "How do users handle this today? What's broken?"
3. "What should users be able to do?"
4. "Which user roles can access this? What can each role do?"
5. "What's the happy path? What could go wrong?"

**Optional (ask if relevant):**
- "How will we measure success?"
- "What are we explicitly NOT building?"
- "Does this need new UI designs?"

### Step 2: Generate PRD

Use `templates/prd-template.md`. Fill in:

**Core (always required):**
- Executive Summary
- Problem Statement
- Proposed Solution
- User Stories

**Validation (always required):**
- User Permissions
- Test Scenarios

**Optional (include if discussed):**
- Success Metrics
- Out of Scope
- Design Needed
- Dependencies
- Appendices

### Step 3: Save PRD

Create directory and save:
```
prds/[prd-name]/prd.md
```

Naming: lowercase kebab-case (e.g., `client-portal`, `lead-scoring`)

### Step 4: Offer Next Steps

After PRD is saved:

1. "Would you like an engineer review to catch gaps?" → runs `/review-prd`
2. "Should I extract learnings to the knowledge base?" → runs `/compound-context`
3. "Would you like to break this into smaller PRDs?"
4. "Ready to create Linear issue?"

## Quality Checklist

Before completing:
- [ ] Problem statement is specific (not vague)
- [ ] User stories are discrete and prioritized
- [ ] Permissions table has all roles
- [ ] Test scenarios cover happy path + errors
- [ ] No technical implementation details
- [ ] All Core sections filled

## Anti-Patterns to Avoid

- Technical details (how to build it)
- Vague problems ("improve user experience")
- Missing permissions section
- Asking user to confirm context findings
- Empty optional sections (remove if not needed)
- Writing before understanding the problem
