# Create New PRD

Create a Product Requirements Document at any level.

## Usage

/new-prd [name]

## Behavior

1. Context scan (silently check context/ and prds/)
2. Discovery interview (use context to ask smarter questions)
3. Generate PRD from template
4. Save to prds/[name]/prd.md
5. Offer next steps:
   - Engineer review (/review-prd)
   - Compound context (/compound-context)
   - Create Linear issue
