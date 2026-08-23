# ESHIC 0.2 — RT v0.4 Requirement Feedback v0.1

**Status:** `E2E REVIEW INPUT / UNLOCKED / NON-NORMATIVE`  
**Current authority:** ESHIC 0.1 Experimental Architecture Baseline  
**Target:** ESHIC 0.2 structured-spec candidate  
**Source adopter:** Resource Thermometer (RT) v0.4  
**Source RT commit:** `68a4e34f02a3ba340dea208c6a9dc21fd7bfd5ed`  
**Source RT documents:**
- `docs/architecture/RESOURCE_THERMOMETER_BLUEPRINT_v0.4_E2E_DRAFT.md`
- `docs/architecture/RT_AUDIT_AUTHORITY_SECURITY_v0.4.md`

---

# 0. Purpose

RT v0.4 redefined RT from a simple resource collector into an independent resource-audit tool with a preferred host-scoped Host Agent, target-scoped attribution/evidence, per-metric quality, and explicit audit-security boundaries.

This feedback records the human/Service Hub integration requirements that should be considered during a later ESHIC 0.2 consolidation or successor revision.

It does **not** change ESHIC 0.1 authority and does not independently ratify ESHIC 0.2.

The central conclusion is:

> ESHIC 0.2's existing structured model remains a strong fit. RT v0.4 mainly requires updated RT terminology, a clearer health/anomaly separation, a domain-quality/provenance extension point, and explicit preservation of authorization boundaries outside ESHIC.

---

# 1. Preserve the current ESHIC boundary

RT v0.4 confirms these ESHIC principles:

```text
Service domain meaning remains service-owned
Hub presentation remains Hub-owned
Human-facing action != machine capability != authorization
Hub integration != service runtime dependency
Flat shared summary != service-owned detailed diagnostic model
```

ESHIC MUST NOT absorb RT's audit authority model, telemetry schema, process-control semantics, or detailed process topology.

---

# 2. Requirement ESHIC-RT-R1 — update RT runtime terminology to Host Agent/audit service

Earlier ESHIC 0.2 requirement input refers to RT primarily as a `collector` and says Embedded/Observer RT continues when the Hub is unavailable.

RT v0.4 changes the normal ExecuteOffice topology:

```text
RT Host Agent
→ host-scoped acquisition
→ target-scoped attribution/evidence
```

Embedded acquisition is now a standalone/fallback path rather than the normal ecosystem hot path.

Future ESHIC wording SHOULD therefore prefer concepts such as:

```text
audit service/runtime state
Host Agent state
acquisition state/quality
```

rather than assuming a Hub-owned or target-local collector.

Hub outage rule should become:

```text
visible Service Hub unavailable
→ RT Host Agent/audit collection continues independently where configured
```

---

# 3. Requirement ESHIC-RT-R2 — RT health is not audited-target anomaly state

ESHIC already separates runtime, health, and attention. RT provides a concrete requirement to preserve that separation.

Example A:

```text
RT Host Agent = READY / healthy
PM private-commit growth suspected
```

Expected Hub semantics:

```text
RT runtime/health remains healthy
attention contains target-specific resource anomaly evidence
```

Example B:

```text
PM resource behavior normal
RT ETW unavailable or history store degraded
```

Expected Hub semantics:

```text
RT runtime/health may be DEGRADED
no implication that PM itself is unhealthy
```

Required invariant:

```text
RT service health
!= audited target health
!= RT resource-anomaly evidence
```

RT MUST NOT become the source of generic business-domain health for PM/TM/SB merely because it observes their processes.

---

# 4. Requirement ESHIC-RT-R3 — common quality plus service-specific evidence quality

ESHIC 0.2 currently has useful common observation quality such as:

```text
complete
partial
degraded
unknown
```

RT v0.4 has richer domain-specific evidence quality/provenance, for example:

```text
EXACT
STRONG
INFERRED
PARTIAL
UNSUPPORTED
ACCESS_DENIED
UNAVAILABLE
STALE
```

and evidence source such as:

```text
provider-native attribution
existing Job Object
ETW lifecycle
snapshot inference
Win32 process-memory query
```

ESHIC SHOULD NOT absorb RT's full vocabulary as common Hub quality.

Instead, the structured model should support the pattern:

```text
common quality
+ optional service/domain quality code
+ optional safe provenance/source reference
```

Conceptually:

```text
quality = degraded
reason_code = RT.ATTRIBUTION_INFERRED
source/provenance = optional safe RT-owned value
```

This lets a generic Hub understand the common posture while preserving RT-specific audit meaning.

---

# 5. Requirement ESHIC-RT-R4 — target summaries need attribution quality

RT's target list remains an excellent fit for ESHIC flat collections.

Useful summary columns/fields include:

```text
target identity
current CPU
private commit / memory summary
handles/threads where useful
observation freshness
attribution quality
attention count/summary
telemetry-gap indicator
```

Attribution quality is important because an externally observed process group may be `INFERRED` while a provider-native group is `EXACT` or `STRONG`.

The Hub should not present both as equally certain without a semantic distinction.

---

# 6. Requirement ESHIC-RT-R5 — telemetry gaps are first-class operator-visible evidence

RT v0.4 explicitly models conditions such as:

```text
TELEMETRY_UNAVAILABLE
TELEMETRY_GAP
SUSPEND_RESUME_GAP
HISTORY_DISCONTINUITY
ATTRIBUTION_QUALITY_DEGRADED
ACQUISITION_AUTHORITY_CONFLICT
```

ESHIC does not need these exact RT codes in its common vocabulary.

It does need to allow RT to surface them through:

```text
attention
field/metric reason_code
quality/freshness metadata
notice/diagnostic detail
```

A gap must remain distinguishable from a real zero resource value.

---

# 7. Requirement ESHIC-RT-R6 — authorization precedes ESHIC projection

RT may hold broader audit-read authority than ordinary services. It therefore separates:

```text
AUDIT_AUTHORITY
AUDIT_REQUEST_AUTHORITY
EVIDENCE_ACCESS_AUTHORITY
MANAGEMENT_AUTHORITY
```

ESHIC MUST NOT become the authority model for these planes.

The correct integration flow is:

```text
Hub/operator/caller identity
→ RT/adapter authority check
→ authorized semantic projection
→ ESHIC Snapshot / Collection / Action state
→ Hub rendering
```

Meaning:

> ESHIC carries an already-authorized semantic projection; it does not infer evidence-access authority from service discovery or Hub presence.

A Service Hub consumer does not automatically gain host-wide RT evidence merely because RT can see it.

---

# 8. Requirement ESHIC-RT-R7 — diagnostic capture action impact

`CaptureDiagnosticWindow` is non-mutating with respect to the audited target, but it **does** change RT's own audit behavior:

```text
sampling rate may increase
RT diagnostic-session state is created/updated
bounded storage/I/O may increase
```

Therefore a future RT ESHIC action should not necessarily be classified as `read_only` merely because the audited target is untouched.

Candidate human-impact classification is more naturally:

```text
routine
```

or, if ESHIC semantics define it that way:

```text
state_changing
```

with clear service text that the state change is inside RT, not the target service.

The action may reference a CME capability such as:

```text
resource.diagnostic.capture
```

but ESHIC remains responsible only for human-facing action semantics, not capability authorization.

---

# 9. Requirement ESHIC-RT-R8 — detailed topology remains RT-owned

The earlier ESHIC decision to avoid a generic recursive tree primitive remains correct.

RT detailed diagnostics may contain:

```text
process topology
process-instance history
attribution sources
metric provenance
short-lived child evidence
raw/derived metric details
```

Those semantics belong to RT's detailed diagnostic surface.

The shared Hub needs only a flat target collection plus summary metrics/attention and an action to open RT diagnostics when needed.

No recursive ESHIC tree primitive is justified by RT v0.4 alone.

---

# 10. Requirement ESHIC-RT-R9 — metric descriptors should preserve semantic units and safe reason metadata

RT benefits directly from ESHIC 0.2 field/metric descriptors for:

```text
bytes
percent
count
timestamp
duration
status-like enum
```

Future structured ESHIC should preserve a way for a metric value to carry, where useful:

```text
observed_at
common quality
reason_code
```

and an optional service-specific safe quality/provenance code per ESHIC-RT-R3.

ESHIC should not embed RT raw-counter or derivation schemas.

---

# 11. Requirement ESHIC-RT-R10 — attention and runtime posture for privileged audit limitations

RT may be healthy but unable to perform one privileged observation because platform permission is absent.

Examples:

```text
ETW unavailable
specific process query ACCESS_DENIED
GPU metric unsupported
```

The RT adapter should be able to decide whether that results in:

```text
runtime = READY or DEGRADED
health = healthy/degraded
attention = optional
metric quality = partial/unknown/degraded
```

based on actual product impact.

ESHIC should not hard-code RT-specific inference logic in the Hub.

---

# 12. Explicit non-scope for ESHIC

RT v0.4 reinforces that ESHIC must not absorb:

```text
RT metric/raw-counter schema
process-instance identity model
process topology/attribution algorithms
ETW/Job Object implementation
SQLite/retention schema
baseline/anomaly algorithms
Host acquisition ownership/fencing
AUDIT_AUTHORITY
AUDIT_REQUEST_AUTHORITY
EVIDENCE_ACCESS_AUTHORITY
IPC authentication/ACL implementation
process kill/restart/throttle/config controls
```

ESHIC remains the service-to-Hub human semantic boundary.

---

# 13. Reverse-adoption checks after ESHIC 0.2 consolidation

When the next cumulative ESHIC 0.2 candidate is ready, reverse-apply it to RT and verify:

```text
ESHIC-RT-A1 RT Host Agent/runtime semantics fit the common runtime model
ESHIC-RT-A2 RT service health remains separate from audited-target anomaly attention
ESHIC-RT-A3 common quality can coexist with RT-specific quality/provenance
ESHIC-RT-A4 flat target collection can expose attribution quality and telemetry gaps
ESHIC-RT-A5 diagnostic capture action expresses RT-state-changing/target-non-mutating semantics
ESHIC-RT-A6 Hub outage does not imply RT Host Agent outage
ESHIC-RT-A7 authorized projection occurs before ESHIC presentation
ESHIC-RT-A8 detailed process topology remains RT-owned without requiring generic tree UI
ESHIC-RT-A9 no RT authority/security/runtime implementation leaks into ESHIC core
```

---

# 14. Feedback verdict

```text
ESHIC-RT-R1 Host Agent/audit terminology update          HARVEST
ESHIC-RT-R2 RT health vs target anomaly separation       HARVEST / CONFIRM EXISTING MODEL
ESHIC-RT-R3 common + domain quality/provenance pattern    HARVEST
ESHIC-RT-R4 target attribution quality in summary         HARVEST
ESHIC-RT-R5 telemetry-gap presentation                    HARVEST
ESHIC-RT-R6 authorization before semantic projection      CONFIRM BOUNDARY
ESHIC-RT-R7 diagnostic action impact nuance               HARVEST
ESHIC-RT-R8 no generic tree requirement                   CONFIRM EXISTING DIRECTION
ESHIC-RT-R9 metric safe reason/quality metadata            HARVEST
ESHIC-RT-R10 privileged-observation degradation semantics HARVEST
```

This document is future revision input only. It does not alter the current ESHIC 0.1 authority or independently ratify the ESHIC 0.2 draft.
