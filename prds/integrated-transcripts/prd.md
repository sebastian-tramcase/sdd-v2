---
title: Integrated Transcripts
status: Not Started
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: Core Infrastructure
---

# Integrated Transcripts

## Executive Summary

Centralized transcription for calls and meetings. Depends on Google Workspace migration for full functionality.

---

## Problem Statement

Transcripts are scattered:
- Call transcripts not centralized
- Meeting transcripts missing
- No searchable transcript archive

---

## Proposed Solution

Build Integrated Transcripts with:
1. Centralized call transcription
2. Meeting bot integration (post Google Workspace)
3. Searchable transcript archive

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Agent | centralized call transcripts | I can review what was said |
| P1 | Agent | meeting bot for transcripts | meetings are automatically transcribed |
| P2 | Case Manager | searchable transcripts | I can find specific conversations |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Call Transcripts | Centralized call transcription | Medium | Not Started |
| Meeting Bot | Post Google Workspace meeting bot integration | Medium | Not Started |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Call transcripts centralized | Yes |
| Meeting bot functional | Yes |

---

## Technical Approach

- **Stack:** Transcription service, Django
- **Integrates with:** Softphone (calls), Google Workspace (meetings)

---

## Dependencies

| Dependency | Status |
|------------|--------|
| Google Workspace Migration | Required |

---

## Timeline

Q1 2026 (March)
