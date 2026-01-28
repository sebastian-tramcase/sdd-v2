---
title: Client Portal
status: In Progress
created: 2026-01-27
updated: 2026-01-27
owner: Platform
initiative: 90 Days Quick Wins
---

# Client Portal

## Executive Summary

Client Portal provides a secure interface for clients to upload documents and view case status. This is a 90-day quick win initiative enabling client self-service.

---

## Problem Statement

Clients have no self-service options:
- No way to upload documents directly
- No visibility into case status
- No secure, authenticated access

---

## Proposed Solution

Build Client Portal v1 with:
1. Secure document upload
2. Case status view
3. Secure link with 2FA/SMS authentication

---

## User Stories

| Priority | As a... | I want... | So that... |
|----------|---------|-----------|------------|
| P1 | Client | to upload documents securely | I don't need to email sensitive files |
| P1 | Client | to view my case status | I know where things stand |
| P1 | Client | secure access with 2FA | my information is protected |

---

## Features

| Feature | Description | Priority | Status |
|---------|-------------|----------|--------|
| Client Portal v1 | Document upload, status view, secure link with 2FA/SMS | Critical | In Progress |

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Document upload functional | Yes |
| Status view available | Yes |
| 2FA/SMS authentication | Working |

---

## Technical Approach

- **Stack:** Django, secure token generation, SMS verification
- **Integrates with:** Document Center, Omnichannel (for SMS)

---

## Dependencies

| Dependency | Status |
|------------|--------|
| None | - |

---

## Timeline

Q1 2026 (January - February)
