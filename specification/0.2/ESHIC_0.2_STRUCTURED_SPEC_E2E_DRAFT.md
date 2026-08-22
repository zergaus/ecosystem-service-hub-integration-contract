# Ecosystem Service Hub Integration Contract (ESHIC) 0.2
## Structured Specification E2E Draft

**Document ID:** `ESHIC-0.2-STRUCTURED-SPEC-E2E-DRAFT`  
**Status:** `E2E REVIEW DRAFT / UNLOCKED / NOT CURRENT AUTHORITY`  
**Current authority:** ESHIC 0.1 Experimental Architecture Baseline  
**Design basis:** PM/RT/TM/SB real adopter requirements  
**Principle:** operational-fit first

---

# 0. Status and intent

ESHIC 0.1 establishes the architecture: independent services declare semantic meaning; a Service Hub owns presentation.

This 0.2 draft tests the next step: a **small structured model** that can actually integrate PM, RT, TM, SB, and similar services without service-name-specific Hub rendering.

This draft does not advance `ESHIC_CURRENT.md`. 0.1 remains current until final ecosystem review and reverse adoption are complete.

---

# 1. Goals

ESHIC 0.2 candidate should let a generic Hub answer:

```text
What service is this?
Can I bind to it?
What is its current common runtime posture?
Is operator attention needed?
What important values/metrics should I show?
What bounded collections are useful?
What ordinary settings can be edited?
What human-facing actions exist?
What is the lifecycle/result of an action I invoked?
How fresh/reliable is this snapshot?
Where should the operator open the service's own detailed UI?
```

without understanding PM/TM/RT/SB internal data models.

---

# 2. Non-goals

ESHIC 0.2 does not attempt to:

- replace service-owned detailed management UIs;
- model every domain graph/tree;
- define machine capability Effects;
- grant authority;
- become an RPC framework for all service operations;
- embed arbitrary service HTML/JavaScript/components;
- become a generic workflow engine;
- manage process lifecycle on behalf of RT/services;
- define credential vault/secret transport;
- require one implementation language, transport, or design system.

---

# 3. Normative model status

The structures in this draft are semantic contract candidates. A future ratified 0.2 package may serialize them as JSON Schema or another machine-readable schema after adopter validation.

Do not prematurely lock a wire schema merely because examples use JSON/YAML-like notation.

The semantic model is more important than one serialization.

---

# 4. Top-level model

Conceptually:

```text
ESHIC Service
├─ Declaration
│  ├─ service identity
│  ├─ contract compatibility
│  ├─ binding metadata
│  ├─ field/metric descriptors
│  ├─ collection descriptors
│  ├─ setting descriptors
│  ├─ action descriptors
│  └─ view descriptors
│
└─ Snapshot
   ├─ runtime
   ├─ health
   ├─ attention[]
   ├─ field/metric values
   ├─ collection summaries
   ├─ setting values/availability
   ├─ action availability
   └─ observation metadata
```

Large collections and action result streams may be fetched separately.

---

# 5. Service identity

A Declaration MUST identify the logical service stably.

Candidate fields:

```text
service.id
service.name
service.version
service.category optional
service.instance_id optional
service.description optional
```

`service.id` is stable logical integration identity.

`service.instance_id` MAY distinguish multiple local instances.

Display name is not a primary key.

---

# 6. Contract declaration

Candidate ESHIC metadata:

```text
eshic.version
eshic.enabled
eshic.binding
eshic.declaration_revision
eshic.compatibility
```

A Hub must distinguish:

```text
compatible
unsupported version
ESHIC disabled
malformed declaration
service installed but endpoint unavailable
```

Discovery/binding does not grant operation authority.

---

# 7. Runtime state

Candidate common runtime posture:

```text
STARTING
READY
DEGRADED
ATTENTION_REQUIRED
UNAVAILABLE
STOPPED
RECOVERY_REQUIRED
```

Semantics:

- `STARTING`: service/adapter not ready for ordinary operations.
- `READY`: common integration surface is operational.
- `DEGRADED`: operational but one or more meaningful capabilities/data paths are degraded.
- `ATTENTION_REQUIRED`: service is operational enough to report that a human decision/action is needed.
- `UNAVAILABLE`: service/endpoint cannot currently provide ordinary operation.
- `STOPPED`: intentionally not running where that distinction is known.
- `RECOVERY_REQUIRED`: service has an unresolved durable/unsafe state requiring recovery rather than ordinary retry.

Services retain richer domain state separately.

---

# 8. Health

Candidate health values:

```text
healthy
degraded
unhealthy
unknown
```

Health is not the same as runtime state.

Examples:

```text
runtime = READY
health = degraded
```

may mean the service is usable but one dependency is degraded.

```text
runtime = UNAVAILABLE
health = unknown
```

may mean the Hub cannot query sufficient domain health.

A service MAY include reason code/summary/details refs.

---

# 9. Attention model

Attention is first-class and independent of generic health.

Candidate item:

```text
AttentionItem
- id
- severity
- reason_code
- summary
- detail optional
- created_at
- updated_at
- action_refs[] optional
```

Severity:

```text
info
warning
error
critical
```

Examples:

```text
PM.AUTH_REQUIRED
RT.STORAGE_RETENTION_FAILED
TM.APPROVAL_WAITING
SB.SLACK_AUTH_REQUIRED
```

A Hub may aggregate attention across services without knowing domain-specific reason semantics.

---

# 10. Observation metadata

Every Snapshot SHOULD carry:

```text
observed_at
quality
source_revision/watermark optional
stale_after optional
```

Candidate quality:

```text
complete
partial
degraded
unknown
```

Rules:

- unknown is not zero;
- partial is not complete;
- stale data should remain distinguishable from fresh current state;
- a Hub should not silently convert missing/unknown values to reassuring defaults.

---

# 11. Field and metric descriptors

A service may declare scalar fields/metrics.

Candidate descriptor:

```text
FieldDescriptor
- id
- label
- value_type
- unit optional
- description optional
- importance optional
- read_only = true for observation fields
```

Candidate value types:

```text
text
integer
number
boolean
timestamp
duration
bytes
percent
count
enum
```

Candidate importance:

```text
normal
secondary
important
critical
```

Importance is semantic prominence, not a CSS/color instruction.

---

# 12. Field values

Snapshot values may contain:

```text
id
value
observed_at optional override
quality optional override
reason_code optional
```

A value may be absent/unknown explicitly.

The Hub chooses rendering according to type/unit/importance and its design system.

---

# 13. Collections

A Collection represents a bounded or pageable set of flat records.

Descriptor candidate:

```text
CollectionDescriptor
- id
- label
- item_identity_field
- columns[]
- paging = none | cursor | page
- default_limit optional
- importance optional
```

Collection result candidate:

```text
collection_id
items[]
completeness
next_cursor optional
observed_at
quality
```

Completeness:

```text
complete
partial
unknown
```

This is especially important for PM inventory where provider UI enumeration may not be exhaustive.

---

# 14. Collection rendering

ESHIC defines the semantic collection, not the exact visual widget.

A Hub MAY choose:

```text
table
list
cards
compact rows
```

according to screen size/design system.

A service should not require a custom renderer if supported ESHIC semantics are sufficient.

---

# 15. No recursive tree primitive in 0.2 baseline

The current reference ecosystem has multiple internal hierarchies, but no repeated shared Hub requirement justifies a generic tree contract yet.

Therefore the 0.2 baseline does not define a recursive tree primitive.

Services may:

- expose a flat summary collection in Hub;
- provide an `open management shell` action for detailed hierarchy;
- later propose a tree primitive after concrete repeated integration evidence.

This prevents premature standard complexity.

---

# 16. Setting descriptor

Ordinary typed settings candidate:

```text
SettingDescriptor
- id
- label
- value_type
- description optional
- writable
- required optional
- validation optional
- choices optional
- apply_semantics
- restart_requirement
```

Candidate apply semantics:

```text
immediate
explicit_apply
next_start
```

Restart requirement:

```text
none
service_restart
provider_restart
user_session_restart
system_restart
```

The service remains authoritative for whether a setting is currently writable.

---

# 17. Setting validation

Candidate constraints:

```text
min/max
min_length/max_length
regex/pattern where safe
allowed values
numeric step optional
```

Hub may perform client-side convenience validation, but service-side validation remains authoritative.

A rejected update returns structured field/reason information rather than requiring the Hub to infer errors from text.

---

# 18. Secret settings deferred

ESHIC 0.2 baseline SHOULD NOT standardize generic secret credential transport.

Reasons:

- current adopters do not need a shared implementation;
- secure storage/host/UI boundaries differ materially;
- normal Hub settings rendering risks accidental secret exposure/caching;
- SB already has a service-owned secure setup path.

A future secret-setting extension requires concrete adopter evidence and explicit threat-model design.

---

# 19. Human Action descriptor

Candidate action:

```text
HumanAction
- action_id
- label
- summary optional
- impact
- confirmation
- parameters[] optional
- capability_ref optional
- result_kind optional
```

Availability is dynamic and is reported in Snapshot/action state.

---

# 20. Action impact

Candidate semantic impact:

```text
read_only
routine
state_changing
destructive
security_sensitive
```

This guides operator presentation/confirmation. It is not a machine authority decision.

A provider/service may choose a stricter impact than the Hub would infer.

The Hub must not lower a service-declared impact.

---

# 21. Confirmation semantics

Candidate confirmation requirements:

```text
none
confirm
explicit_text
external_human_gate
```

Meaning:

- `none`: ordinary action may start immediately when caller is otherwise authorized.
- `confirm`: simple operator confirmation UX expected.
- `explicit_text`: service provides challenge/expected confirmation semantics.
- `external_human_gate`: Hub must route to/service-provided trusted human mechanism rather than treating an ordinary click as sufficient.

ESHIC does not itself prove authority or trusted human identity.

---

# 22. Action parameters

Parameters use a bounded typed model similar to ordinary settings:

```text
id
label
type
required
validation/choices optional
sensitive=false in baseline
```

Do not add arbitrary script/expression parameters.

Security-sensitive secret data remains outside generic baseline parameter handling unless explicitly versioned later.

---

# 23. Action availability

Dynamic action state candidate:

```text
action_id
available
reason_code optional
reason_summary optional
```

Examples:

```text
PM reconnect unavailable because provider is already READY
TM reconcile unavailable because no recovery item exists
SB reconnect unavailable while Slack auth is missing and secure setup is required
```

Unavailable does not mean unauthorized; services may expose reason distinctions as appropriate without leaking sensitive policy.

---

# 24. Action invocation lifecycle

Invocation returns a stable action-operation identity.

Candidate states:

```text
ACCEPTED
RUNNING
WAITING_HUMAN
SUCCEEDED
FAILED
RESULT_UNKNOWN
RECOVERY_REQUIRED
CANCELLED
```

The Hub may poll/subscribe/fetch result according to binding, but must treat service state as authoritative.

---

# 25. RESULT_UNKNOWN safety

`RESULT_UNKNOWN` means an effect may have occurred but terminal proof is missing.

A generic Hub MUST NOT automatically invoke the same action again merely because it saw a timeout.

Instead:

```text
show uncertain/recovery state
→ use service-provided reconcile/status action if available
→ await authoritative terminal result
```

Idempotency/reconciliation is service-owned.

This rule aligns PM, TM, and SB uncertain-effect semantics without importing their internal journals into ESHIC.

---

# 26. Semantic views

A Declaration may describe common Hub composition using a small view tree of **grouping only**, not arbitrary recursive data controls.

Candidate view element kinds:

```text
section
field_group
metric_group
collection
action_group
settings_group
notice_group
```

A view element references declared field/metric/collection/action/setting IDs.

Example:

```yaml
section: Overview
children:
  - metric_group: runtime-metrics
  - action_group: common-actions
```

The hierarchy here describes presentation grouping, not a recursive domain-data tree primitive.

---

# 27. Presentation boundary

Service declarations MUST NOT prescribe:

```text
CSS
font
exact colors
corner radius
shadows
animation timing
React/Fluent component class
pixel positioning
arbitrary HTML
JavaScript
```

The service may express semantic importance/severity/impact; Hub maps those semantics into its design system.

---

# 28. Service-owned detailed UI escape hatch

A service MAY provide a human action or link semantic equivalent to:

```text
OpenManagementShell
OpenDiagnostics
OpenSecureSetup
```

This is preferred over bloating ESHIC with every product-specific detailed management concept.

The Hub provides unified summary/control; the service may retain specialized deep UX.

---

# 29. Logical operations

A transport binding should provide semantic operations equivalent to:

```text
GetDeclaration
GetSnapshot
ReadCollection(collection_id, cursor/limit)
ReadSettings
ApplySettings(changes, operation_id)
InvokeAction(action_id, parameters, operation_id)
GetActionResult(operation_id)
```

A service may support push/subscription updates, but baseline correctness should not depend on one transport.

---

# 30. Transport independence

ESHIC semantic operations may be bound through:

```text
named pipe/local RPC
HTTP loopback
in-process interface
MCP tool/resource adapter
other versioned local transport
```

Transport metadata belongs to binding/discovery.

The Hub must not require every service to adopt the Hub implementation language.

---

# 31. Snapshot vs collection fetch

Snapshot should remain compact enough for routine Hub summary reads.

Large/detailed records should use `ReadCollection` or service-owned detailed UI.

This avoids turning Hub status refresh into a full PM inventory scan, TM job history scan, or RT process-history export.

Services SHOULD provide materialized/cheap current snapshots where their domain architecture supports it.

---

# 32. Freshness and source truth

A Hub rendering a Snapshot MUST preserve freshness/quality semantics.

If a snapshot is stale/partial:

- display should indicate degraded confidence;
- actions whose safety depends on fresh state may be unavailable according to service declaration;
- Hub should not reconstruct service truth by independently querying unrelated provider internals.

Service adapter remains the source of ESHIC domain projection.

---

# 33. Optional CME capability reference

`capability_ref` MAY identify the machine capability corresponding to a Human Action.

Example conceptual action:

```yaml
action_id: reconnect-provider
impact: state_changing
confirmation: none
capability_ref: pm/chatgpt.provider.reconnect
```

Exact identifier syntax comes from the referenced capability system.

Rules:

- baseline ESHIC conformance does not require CME;
- a Hub cannot interpret capability existence as permission;
- ESHIC impact/confirmation remains human UX semantics;
- CME Effects/availability remain machine capability semantics;
- authorization remains outside both.

---

# 34. PM reference adoption

PM ESHIC adapter should expose approximately:

```text
runtime/provider/auth state
Project/conversation/logical-session counts
unresolved count
last provider interaction
attention
open PM shell
reconnect/recover
capture/open diagnostics
```

It should not expose the entire Project→Conversation hierarchy through the common Hub contract in baseline 0.2.

---

# 35. RT reference adoption

RT adapter should expose:

```text
collector state
current target/resource summary
flat target collection
history/baseline summary
suspected anomaly attention
diagnostic capture
storage/retention attention
```

No kill/restart/priority actions appear because RT does not own them.

---

# 36. TM reference adoption

TM adapter should expose:

```text
runtime/Codex-host state
active/waiting/recovery/approval counts
capacity summary
last host interaction
attention
open TM status shell
reconcile/open diagnostics
```

Detailed Authority/organization/work graph remains TM-owned UI.

---

# 37. SB reference adoption

SB adapter should expose:

```text
Slack connection/runtime
PM/TM route summaries
last ingress/egress
pending/recovery counts
attention
reconnect Slack
open secure setup/diagnostics
```

Slack secret tokens remain outside generic ESHIC settings.

---

# 38. Hub rendering conformance candidate

A conforming Hub SHOULD:

1. discover/bind without service-name special casing for supported semantics;
2. preserve runtime/health/attention distinctions;
3. preserve unknown/partial/stale quality;
4. render supported scalar fields/metrics;
5. render flat collections generically;
6. render ordinary settings and validation errors;
7. honor action impact/confirmation semantics;
8. track async action operation identity;
9. never blind-retry RESULT_UNKNOWN;
10. offer service-owned management/diagnostic actions;
11. reject/mark unsupported ESHIC versions clearly;
12. not treat `capability_ref` as authorization.

---

# 39. Service conformance candidate

A conforming service adapter SHOULD:

1. provide stable service identity/version;
2. expose a valid Declaration;
3. provide a compact Snapshot;
4. keep service domain logic authoritative;
5. use stable IDs for fields/collections/settings/actions;
6. report quality/freshness honestly;
7. validate settings server-side;
8. provide stable operation identity for actions;
9. report uncertain outcomes explicitly;
10. avoid visual implementation instructions;
11. avoid exposing secrets in ordinary snapshots/declarations;
12. continue domain operation when Hub disappears unless its own product architecture explicitly requires Hub.

---

# 40. Security boundary

ESHIC transport/binding MUST be authenticated/authorized according to deployment architecture, but ESHIC does not define the organization authority model.

Declarations/snapshots should be safe for their intended local operator context and MUST NOT include:

```text
raw passwords/tokens
browser cookies/access tokens
private keys
recovery codes
unnecessary sensitive command lines
```

Sensitive diagnostic material belongs behind explicit service-specific policy.

---

# 41. Hub outage independence

A conforming ecosystem SHOULD treat Service Hub as an operator integration surface, not a mandatory runtime coordinator unless a service explicitly declares that architecture.

Reference expectation:

```text
Hub process/UI unavailable
→ PM continues
→ RT Embedded/Observer continues
→ TM continues
→ SB continues
```

Hub-specific actions/settings are unavailable until it returns.

---

# 42. Adapter failure

If the service is running but its ESHIC adapter is broken/unreachable, the Hub should show integration unavailable/unknown rather than claim the service domain itself is necessarily unhealthy.

A service may expose adapter health separately where useful.

This preserves:

```text
service domain health
!= ESHIC integration health
```

---

# 43. Versioning

Material semantic changes require a versioned ESHIC revision.

A future stable 0.2/1.0 process should distinguish:

```text
compatible optional addition
new required semantic
changed enum meaning
changed action safety meaning
changed serialization/binding only
```

Published prior versions are not silently rewritten.

---

# 44. Validation before schema freeze

Before machine JSON Schema is treated as normative, validate this semantic model against the four real adopters:

```text
PM
RT
TM
SB
```

For each adapter verify:

- no service-specific Hub rendering branch is required for baseline summary;
- detailed product semantics remain service-owned;
- no required field exists solely for hypothetical external adopters;
- no real operator need is lost;
- async/uncertain action semantics are sufficient;
- adapter implementation remains small.

Only then freeze a serialization schema.

---

# 45. Reverse-adoption failure rule

If applying this 0.2 candidate to PM/RT/TM/SB requires:

```text
duplicating domain truth
adding unnecessary service layers
exposing sensitive internals
inventing fake hierarchy/metrics
moving product UI into Hub
weakening service safety semantics
```

then ESHIC 0.2 reopens.

The service is not declared “wrong” merely because a draft standard was published first.

---

# 46. Candidate E2E acceptance matrix

```text
ESHIC2-01 stable service identity
ESHIC2-02 version compatibility
ESHIC2-03 runtime state
ESHIC2-04 health distinction
ESHIC2-05 attention items
ESHIC2-06 freshness/quality
ESHIC2-07 typed fields
ESHIC2-08 typed metrics/units
ESHIC2-09 flat collections
ESHIC2-10 collection completeness
ESHIC2-11 ordinary settings
ESHIC2-12 validation errors
ESHIC2-13 restart/apply semantics
ESHIC2-14 action descriptor
ESHIC2-15 impact semantics
ESHIC2-16 confirmation semantics
ESHIC2-17 typed parameters
ESHIC2-18 action availability
ESHIC2-19 async lifecycle
ESHIC2-20 RESULT_UNKNOWN no blind retry
ESHIC2-21 semantic view grouping
ESHIC2-22 no CSS/custom component leakage
ESHIC2-23 service-owned detailed UI
ESHIC2-24 transport independence
ESHIC2-25 compact snapshot
ESHIC2-26 optional capability_ref
ESHIC2-27 capability != authority
ESHIC2-28 secret settings deferred
ESHIC2-29 no generic recursive tree requirement
ESHIC2-30 PM adoption
ESHIC2-31 RT adoption
ESHIC2-32 TM adoption
ESHIC2-33 SB adoption
ESHIC2-34 Hub outage independence
ESHIC2-35 adapter/domain health distinction
```

---

# 47. Current authority / promotion gate

This document remains a draft.

Promotion requires:

1. PM/RT/TM/SB detailed Blueprint review;
2. requirement-matrix review;
3. reverse adoption to all four services;
4. CME↔ESHIC boundary review;
5. Service Hub reference-rendering feasibility review;
6. no material missing common operator need;
7. no speculative primitives retained without adopter need;
8. serialization/schema review only after semantic fit;
9. explicit decision to advance `ESHIC_CURRENT.md`.

Until then:

```text
ESHIC 0.1 = CURRENT AUTHORITY
ESHIC 0.2 = E2E REVIEW DRAFT / UNLOCKED
```

---

# 48. Definition of success

ESHIC 0.2 succeeds if our real Hub can integrate PM, RT, TM, and SB like this:

```text
Install/register service.
Hub discovers it.
Hub can show useful status without custom service renderer code.
Hub can show the right few metrics/collections/settings/actions.
Operator attention is obvious.
Long-running/uncertain actions do not get duplicated accidentally.
Deep product management opens the service's own shell instead of bloating the common contract.
CME can optionally identify the machine capability behind an action without taking over UI or authority.
If Hub dies, the services keep doing their actual jobs.
```

That is the operational target; external generality comes afterward.
