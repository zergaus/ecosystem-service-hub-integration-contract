# ESHIC Ecosystem Requirement Matrix v0.1

**Status:** E2E REVIEW INPUT / UNLOCKED  
**Source adopters:** PM, RT, TM, SB  
**Purpose:** derive the smallest useful ESHIC successor from actual Service Hub/operator needs.

---

# 1. Executive conclusion

ESHIC 0.1 correctly establishes the architecture boundary. The next real need is **structured semantics**, not a large widget catalog.

The four current adopters repeatedly need:

```text
service identity/version
runtime + health summary
attention/operator intervention
freshness/quality
scalar fields and metrics
flat collections/tables
ordinary typed settings
human-facing actions
async action lifecycle
semantic view grouping
optional machine-capability reference
```

They do **not** currently justify a general recursive Project/tree UI primitive.

PM's Project→Conversation hierarchy belongs in the PM management shell. TM's organization/work graph belongs in TM. RT process trees belong in RT diagnostics. SB has no hierarchical management need.

Therefore ESHIC 0.2 should stay small.

---

# 2. PM requirements

Service Hub should understand PM summary/operator state:

```text
PM service version/runtime
provider state
auth state
Project count
conversation count
managed logical-session count
unresolved records
last provider interaction
attention items
open PM shell
reconnect/recover provider
capture/open diagnostics
```

PM detailed shell remains PM-owned:

```text
Projects
  → conversations
logical session generations
rollover transactions
operation journal
provider diagnostics
```

ESHIC should not require a generic tree solely for this internal hierarchy.

---

# 3. RT requirements

Hub-facing RT needs:

```text
collector state
target list/current target identity
current CPU/memory/handles/threads metrics
bounded history/baseline summary
suspected anomaly attention
diagnostic capture action
retention/storage attention
TELEMETRY_UNAVAILABLE
```

A flat collection/table can show target summaries. Detailed process-tree diagnostics may stay RT-owned.

ESHIC must not add process-control actions because RT intentionally does not own them.

---

# 4. TM requirements

Hub-facing TM needs:

```text
runtime/Codex-host state
model/catalog availability summary
active jobs
waiting/blocking jobs
approval attention
recovery-required count
capacity summary
last successful host interaction
open TM status/management surface
reconcile/open diagnostics
```

ESHIC should not absorb:

```text
Authority Ledger
role organization graph
full work graph
Resource Claims
release-gate internals
Codex App Server protocol
```

Those remain TM domain UI/state.

---

# 5. SB requirements

Hub-facing SB needs:

```text
Slack connection state
bridge runtime state
PM route status
TM route status
last ingress/egress activity
pending/recovery count
attention
reconnect Slack
open secure setup/diagnostics
```

Secret Slack token entry should remain SB-owned secure setup, not a generic ESHIC secret-setting primitive in 0.2.

---

# 6. Common state semantics

The four adopters benefit from a compact common runtime posture, separate from domain-specific states.

Candidate runtime states:

```text
STARTING
READY
DEGRADED
ATTENTION_REQUIRED
UNAVAILABLE
STOPPED
RECOVERY_REQUIRED
```

Candidate health values:

```text
healthy
degraded
unhealthy
unknown
```

Runtime and health should remain distinguishable. A service can be `READY` with degraded domain health, or `UNAVAILABLE` with unknown health because the adapter cannot query it.

---

# 7. Attention requirements

All adopters need explicit operator attention independent of generic health.

Attention item needs:

```text
stable id
severity
summary
detail optional
reason code
created/updated timestamp
action refs optional
```

Candidate severity:

```text
info
warning
error
critical
```

Examples:

- PM `AUTH_REQUIRED`;
- TM approval waiting;
- RT storage retention failure;
- SB Slack authentication/connection issue.

Attention does not grant authority to resolve the issue.

---

# 8. Freshness and quality

The Hub must distinguish a real zero/empty value from stale/partial/unknown data.

Observation metadata should support:

```text
observed_at
source_revision/watermark optional
quality
stale_after optional
```

Candidate quality:

```text
complete
partial
degraded
unknown
```

This requirement is especially important for PM partial inventory, RT partial metric collection, TM projected status, and SB dependency state.

---

# 9. Scalar fields and metrics

Common typed values include:

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
status-like enum
```

Metric/field descriptors may include:

```text
id
label
type
value
unit optional
quality/freshness
importance optional
```

ESHIC should describe semantic type/unit/importance, not visual color/font/CSS.

---

# 10. Collections

Actual adopters need flat collections such as:

```text
PM unresolved conversations
RT targets
TM active jobs
SB dependency routes
```

Requirements:

- stable collection id;
- column/field semantics;
- item identity;
- completeness/quality;
- optional paging/cursor metadata;
- bounded/default view hints.

A collection may be rendered as table/list/cards according to Hub policy. ESHIC should not prescribe a specific UI component library.

---

# 11. No generic recursive tree requirement yet

Current internal trees:

```text
PM Project→Conversation
RT process tree
TM organization/work hierarchy
```

have different domain semantics and are not all required in the shared Hub summary.

Therefore:

> ESHIC 0.2 should not add a recursive tree primitive until at least two concrete Hub integrations need the same semantic hierarchical interaction pattern that cannot be expressed acceptably by service-owned detailed UI + flat Hub summaries.

This is an operational-fit-first decision, not a permanent ban.

---

# 12. Settings

Common settings are ordinary operator configuration:

```text
boolean
integer/number with bounds
text
select/enum
duration
```

Settings need:

```text
setting id
current value
type
validation constraints
read-only/writable
restart required / apply semantics
```

Secret credential input is deferred from generic 0.2 unless a concrete adopter proves a safe cross-service need. SB Slack secrets remain in SB secure setup.

---

# 13. Human-facing actions

All adopters need typed actions.

Descriptor requirements:

```text
action_id
label/summary
availability
disabled reason optional
impact
confirmation requirement
parameters optional
optional capability_ref
```

Candidate impact:

```text
read_only
routine
state_changing
destructive
security_sensitive
```

Candidate confirmation:

```text
none
confirm
explicit_text
external_human_gate
```

ESHIC describes human interaction semantics. It does not decide authorization.

---

# 14. Async action lifecycle

Real actions can outlive one RPC round trip.

Candidate lifecycle:

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

Critical rule:

> `RESULT_UNKNOWN` is not equivalent to failure and MUST NOT be blindly retried by a generic Hub.

The service owns reconciliation/idempotency semantics.

---

# 15. Semantic views

Common grouping requirements can be expressed without custom UI code through a small view schema:

```text
section
field_group
metric_group
collection
action_group
settings_group
notice_group
```

A view references semantic data/action/setting ids.

It should not contain CSS, arbitrary HTML, JavaScript, design-system class names, or service-defined executable expressions.

---

# 16. Machine capability reference

An ESHIC action MAY carry:

```text
capability_ref
```

pointing to CME or another machine capability identifier.

Meaning:

```text
this human-facing action corresponds to this machine-declared capability
```

It does not mean:

```text
CME is required for all ESHIC services
the capability is authorized
the action is approved
ESHIC imports CME Effect semantics into UI rendering
```

---

# 17. Service/Hub independence

If Service Hub is unavailable:

```text
PM continues
RT Embedded/Observer continues
TM continues
SB continues
```

ESHIC is an integration boundary, not a runtime dependency that turns Hub outage into ecosystem outage.

The Hub may become unable to operate a service, but the service does not normally stop merely because its ESHIC consumer disappeared.

---

# 18. Explicitly rejected 0.2 scope

Do not add without concrete need:

```text
arbitrary HTML/widgets
CSS/theme controls
service-defined React/Fluent components
recursive tree primitive
workflow designer
script/expression evaluator
secret vault/credential entry framework
generic process kill/restart semantics
machine capability Effect vocabulary
authority/delegation ledger
resource telemetry schema
cloud/plugin marketplace
```

---

# 19. Requirement verdict

Recommended ESHIC 0.2 structured core:

```text
Declaration
Snapshot
├─ runtime
├─ health
├─ attention[]
├─ fields/metrics[]
├─ collection descriptors/data
├─ settings
├─ actions
└─ semantic views

Operations
├─ GetDeclaration
├─ GetSnapshot
├─ ReadCollection
├─ ReadSettings
├─ ApplySettings
├─ InvokeAction
└─ GetActionResult
```

Transport remains independent.

This is the smallest shared shape currently justified by PM/RT/TM/SB.
