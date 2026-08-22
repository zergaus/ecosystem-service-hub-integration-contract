# ESHIC Current Authority Pointer

**Standard:** Ecosystem Service Hub Integration Contract  
**Role:** Stable canonical entrypoint for the current authoritative ESHIC architecture baseline  
**Status:** ACTIVE POINTER

## Current authority

- Document: `specification/0.1/ESHIC_0.1_BLUEPRINT.md`
- Version: `0.1 Experimental`
- Status: `Experimental Architecture Baseline`

## Consumer rule

A consumer should:

1. read this file;
2. resolve the current versioned authority document;
3. read that document before making ESHIC-bound design or implementation decisions;
4. record the resolved ESHIC version in implementation or verification evidence;
5. surface a compatibility gate instead of silently accepting a materially incompatible future ESHIC revision.

## Maintenance rule

When the authoritative ESHIC version changes, this pointer must be updated in the same publication work unit.

A new ESHIC authority is not publication-complete until:

1. the new versioned document is committed;
2. its status is explicit;
3. this pointer identifies it;
4. the version/status fields agree;
5. compatibility or migration notes are recorded where needed;
6. remote publication is independently verified.

## Authority history

| Effective date | Authority | Status | Notes |
|---|---|---|---|
| 2026-08-22 | `specification/0.1/ESHIC_0.1_BLUEPRINT.md` | Experimental Architecture Baseline | First independent ESHIC repository baseline. |
