# PRD Creator Skill

Create Product Requirements Documents focused on product understanding.

## When to Use

- User runs `/new-prd [name]`
- User says "create a PRD" or "new product spec"
- Starting a new product initiative
- Migrating existing specs to PRD format

## Document Hierarchy

```
PRD (Product Requirements)
  └── Can be broken into smaller PRDs if needed
```

All PRDs use the same template. Size varies by scope.

## Process

### Step 0: Check for Input Source

**First, check if `input/[name]/` exists.**

If input folder exists:
1. Read ALL content recursively:
   - `epic.md` - high-level context
   - `features/*/feature.md` - feature specs
   - `features/*/user-stories/*.md` - detailed stories with acceptance criteria
2. Skip the interview (Step 1) - you have the source material
3. Go directly to Step 2 (Generate PRD)

If no input folder:
1. Continue to Step 0b (Context Scan) and Step 1 (Interview)

### Step 0b: Context Scan (Silent) - Only if no input

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

### Step 1: Discovery Interview - Only if no input

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

Use `templates/prd-template.md`. Fill in from input or interview:

**From Input Files - Extract:**
- Problem Statement → from epic.md "Problem Statement" or "Business Context"
- Features → from each feature.md (name, objective, user stories table)
- User Stories → from user-stories/*.md (acceptance criteria, test cases)
- Dependencies → from epic.md and feature.md "Dependencies" sections
- Out of Scope → from epic.md and feature.md "Out of Scope" sections

**CRITICAL - Filter Out:**
- Class names, method names, service names
- File paths (EXCEPT in Technical Constraints section)
- Framework references (Django signals, React hooks, etc.)
- Architecture diagrams with implementation details

**Transform to PRD Format:**
- Technical Constraints section: Keep codebase pattern references for GSD
- Everything else: WHAT/WHY only, no HOW

**Core (always required):**
- Executive Summary
- Problem Statement
- Features (with user story tables)
- User Permissions
- Test Scenarios

**Optional (include if in source):**
- Success Metrics
- Out of Scope
- Dependencies
- Technical Constraints (pattern references for GSD)

### Step 3: Save PRD

Create directory and save:
```
prds/[prd-name]/prd.md
```

Naming: lowercase kebab-case (e.g., `client-portal`, `lead-scoring`)

### Step 4: Offer Next Steps

After PRD is saved:

1. "Would you like an engineer review to catch gaps?" → runs `/review-prd`
2. "Should I process another input folder?" → shows available in `input/`
3. "Ready to create Linear issue?"

## Quality Checklist

Before completing:
- [ ] Problem statement is specific (not vague)
- [ ] All features from input are captured
- [ ] User stories have acceptance criteria
- [ ] No technical implementation details (except Technical Constraints)
- [ ] Dependencies captured
- [ ] Out of scope captured

## Anti-Patterns to Avoid

- Technical details in flows/diagrams (how to build it)
- Vague problems ("improve user experience")
- Missing user stories that were in the input
- Class/method names outside Technical Constraints
- Asking interview questions when input folder exists
- Skipping user stories - they contain the real requirements
