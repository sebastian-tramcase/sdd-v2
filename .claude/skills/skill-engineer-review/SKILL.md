# Engineer Review Skill

Review PRDs from an engineer's perspective to catch gaps and ambiguities.

## When to Use

- After creating a PRD: "Would you like an engineer review?"
- On-demand: `/review-prd [path]`
- Before sending PRD to engineering team

## What It Does

Reads the PRD as an engineer would and asks clarifying questions about:

1. **User Stories gaps** - Missing scenarios, unclear flows
2. **Permissions ambiguities** - What exactly can each role do?
3. **Edge cases** - What happens when X is empty/null/unexpected?
4. **State transitions** - How does user get from state A to B?
5. **Error scenarios** - What does user see when things fail?
6. **Success indicators** - How does user know action completed?

## Process

### Step 1: Analyze PRD

Read the PRD and identify:
- Vague statements that need clarification
- Missing edge cases in user stories
- Unclear permission boundaries
- Undefined error states
- Gaps between user stories

### Step 2: Ask Questions

Present questions grouped by section:

**User Stories Questions:**
- "Story #2: What happens if the user has no cases to display?"
- "Story #4: Can users view documents or also download them?"

**Permissions Questions:**
- "Read-only access: Does this include exporting data?"
- "What does a user without access see - error message or hidden feature?"

**Test Scenarios Questions:**
- "What's the expected behavior if [edge case]?"
- "The error case mentions 'user sees error' - what error message specifically?"

### Step 3: Capture Answers

As PM answers questions, capture responses to update PRD.

### Step 4: Update PRD

Add clarifications to relevant sections:
- Add missing edge cases to User Stories
- Clarify permission rules
- Expand test scenarios
- Add to Out of Scope if answer is "we're not handling that"

### Step 5: Offer Next Steps

"PRD updated with clarifications. Would you like to:"
1. Run `/compound-context` to extract learnings
2. Create Linear issue
3. Get another review pass

## Question Patterns

| Pattern | Example |
|---------|---------|
| Empty state | "What if user has no [items]?" |
| Boundary | "What's the max number of [items]?" |
| Permission edge | "Can [role] do [action] on others' data?" |
| Failure mode | "What happens if [action] fails?" |
| Confirmation | "How does user know [action] succeeded?" |
| Transition | "How does user go from [state A] to [state B]?" |

## Output

Updated PRD with clarifications added inline, plus summary of changes made.
