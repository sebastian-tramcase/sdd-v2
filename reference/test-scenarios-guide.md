# Test Scenarios Guide

How to write meaningful E2E test scenarios.

## Philosophy

Test scenarios validate **user outcomes**, not implementation details.

Focus on:
- What the user does
- What they should experience
- Whether they achieved their goal

## Format

| Scenario | User Action | Expected Outcome |
|----------|-------------|------------------|
| Happy path | [Normal usage] | [Success state] |
| Edge case | [Unusual but valid] | [Handled gracefully] |
| Error case | [Something fails] | [Clear recovery path] |

## Examples

### Good Test Scenarios

| Scenario | User Action | Expected Outcome |
|----------|-------------|------------------|
| First document upload | User uploads a PDF | Document appears in list, success message shown |
| Empty state | User opens documents with none uploaded | Helpful message with upload button |
| Large file | User uploads 50MB file | Progress bar, then success or clear size limit error |
| Network error | Upload fails mid-way | Error message, file not lost, retry option |

### Bad Test Scenarios (Too Technical)

- "Given database connection, when query executes, then results return"
- "API returns 200 status code"
- "Redis cache is invalidated"

These belong in engineering test specs, not product PRDs.

## Coverage Checklist

- [ ] Happy path (normal successful usage)
- [ ] Empty states (no data, first-time user)
- [ ] Error states (what can fail, how user recovers)
- [ ] Permission boundaries (what unauthorized users see)
- [ ] Edge cases (limits, unusual inputs)
