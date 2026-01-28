# Input Folder

Drop original SDD epic folders here for processing into PRDs.

## Structure

```
input/
├── softphone/              # Full epic folder from original SDD
│   ├── epic.md
│   └── features/
│       ├── conferences/
│       │   ├── feature.md
│       │   └── user-stories/
│       │       ├── SOFT-001-*.md
│       │       └── ...
│       └── ...
├── omnichannel/
└── ...
```

## Workflow

1. Copy epic folder from original SDD to `input/`
2. Run `/new-prd [epic-name]`
3. SDD v2 reads all content (epic, features, user stories)
4. Generates clean PRD in `prds/[epic-name]/prd.md`

## What Gets Extracted

From your input, SDD v2 extracts:
- Business context and problem statements
- User stories and acceptance criteria
- Test cases
- Dependencies and relationships
- Out of scope items

## What Gets Filtered Out

SDD v2 removes implementation details (HOW):
- Specific class/method names
- Framework references (Django signals, etc.)
- File paths (except in Technical Constraints for GSD)
