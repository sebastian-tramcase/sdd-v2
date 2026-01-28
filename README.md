# SDD v2 - Product Requirements Assistant

A tool that transforms product ideas into comprehensive Product Requirements Documents (PRDs).

## Philosophy

- Focus on **What** and **Why** - never How (implementation)
- Cover all scenarios - happy paths, variations, edge cases, errors
- Build a Product Knowledge Base that grows smarter over time
- Interviews get shorter as the system learns your product

## Commands

| Command | Purpose |
|---------|---------|
| `/new-prd [name]` | Create PRD at any level |
| `/review-prd [path]` | Engineer review to catch gaps |
| `/compound-context [path]` | Extract learnings to context |
| `/explore-specs [query]` | Search existing PRDs and context |

## Workflow

```
/new-prd → Create PRD → /review-prd → /compound-context → Linear
```

## What We DON'T Include

- Code references or file paths
- Technical implementation details
- Database schemas or API designs
- Framework-specific patterns
- Architecture diagrams

These belong in engineering implementation docs, not product PRDs.

## Getting Started

Run `/new-prd [name]` to create your first PRD.
