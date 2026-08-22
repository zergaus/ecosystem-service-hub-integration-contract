# Ecosystem Service Hub Integration Contract (ESHIC)

**Document:** Architecture Blueprint  
**Version:** 0.1 Experimental  
**Status:** Experimental Architecture Baseline  
**Scope:** Implementation-agnostic service-to-Hub integration contract

---

## 1. Executive Summary

The **Ecosystem Service Hub Integration Contract (ESHIC)** defines an integration boundary between independent software services and a shared **Service Hub**.

ESHIC exists so that a service can be:

```text
discovered
identified
understood
observed
configured
operated by a human
rendered coherently
```

without requiring bespoke Hub code for every service.

The target integration flow is:

```text
Install or register service
  -> Discover ESHIC declaration
  -> Validate ESHIC compatibility
  -> Bind service endpoint
  -> Read runtime/data semantics
  -> Read settings and human-facing actions
  -> Read declarative UI schema
  -> Render through the Hub-owned design system
```

The developer experience should remain simple:

> A compatible service declares **meaning and behavior**.  
> The Hub owns **presentation and interaction composition**.

ESHIC is not a Service Hub implementation specification.

It is the contract that allows independently implemented services and independently implemented Service Hubs to meet at a stable semantic boundary.

---

## 2. Problem Statement

Real service ecosystems are heterogeneous.

A single Hub may need to integrate services implemented in:

```text
.NET
Python
Node.js
Rust
Go
Java
other runtimes
```

Those services may have different:

```text
process models
release cadences
local control surfaces
storage models
failure boundaries
transport mechanisms
```

Without an explicit integration contract, each new service tends to become a bespoke Hub integration project.

That produces:

- duplicated UI code;
- service-specific rendering branches;
- inconsistent health semantics;
- inconsistent settings behavior;
- inconsistent operator actions;
- unnecessary coupling between service internals and the Hub UI.

ESHIC exists to prevent that outcome.

---

## 3. Scope

ESHIC 0.1 defines the architectural requirements for:

1. service discovery and identity;
2. Hub-level runtime and health semantics;
3. attention/operator-intervention state;
4. service-specific operational data;
5. typed settings exposure;
6. human-facing semantic actions;
7. declarative control-surface description;
8. separation between service logic and Hub presentation;
9. transport independence;
10. version and compatibility negotiation;
11. validation and conformance direction.

ESHIC 0.1 does not define:

- a mandatory programming language;
- a mandatory UI framework;
- a mandatory design system;
- a mandatory local transport;
- a machine authorization system;
- a general machine-capability standard;
- a cloud control plane;
- a plugin marketplace;
- a universal service orchestrator.

---

## 4. Terminology

### 4.1 Service

An independently implemented operational component that exposes an ESHIC integration boundary.

A Service may be:

- resident;
- on-demand;
- user-session hosted;
- service-session hosted;
- native;
- web-backed;
- multi-process.

### 4.2 Service Hub

An operator-facing integration host that discovers and renders ESHIC-compatible services.

A Hub may provide:

- navigation;
- common status surfaces;
- service control tabs;
- settings;
- diagnostics;
- operator actions;
- logs or history views.

### 4.3 ESHIC Adapter

The service-side integration layer that maps the service domain into ESHIC semantics.

### 4.4 Human-facing action

An operation intentionally represented to an operator through a Service Hub.

It is not the same thing as generic machine capability discovery.

### 4.5 Domain state

Service-specific operational state whose meaning belongs to the service.

### 4.6 Hub-level state

A compact common summary used by the Hub to communicate service operability and attention needs.

---

## 5. Architectural Invariants

### 5.1 Services remain independent

ESHIC does not merge conforming services into one monolithic application.

```text
Service A ─┐
Service B ─┼─ ESHIC ─> Service Hub
Service C ─┤
Service D ─┘
```

Each service retains its own:

- implementation;
- internal domain model;
- lifecycle;
- release cadence;
- failure boundary.

### 5.2 The Hub owns presentation

A service describes **semantic intent**.

The Hub owns **visual presentation**.

A service may declare:

- status;
- metric;
- timestamp;
- text;
- warning;
- notice;
- boolean setting;
- selectable setting;
- read-only value;
- table/list;
- human-facing action.

A service should not define Hub-specific:

- CSS;
- colors;
- fonts;
- spacing;
- border radius;
- shadows;
- animations;
- component library classes.

### 5.3 No service-name rendering branches

A conforming Hub should not require code such as:

```text
if service == "service-a":
    render_service_a()
elif service == "service-b":
    render_service_b()
```

Adding a service that only uses already-supported ESHIC semantics should normally require no service-specific rendering code.

### 5.4 Implementation technology is not part of ESHIC

A service is not required to adopt the Hub's implementation language or UI stack.

A Hub is not required to adopt a service's implementation language or UI stack.

### 5.5 Semantic stability outranks visual convenience

A service must not flatten or distort domain meaning merely to fit a simplistic Hub model.

ESHIC must support multiple:

```text
states
timestamps
metrics
settings
actions
sections
collections
```

within one service.

### 5.6 Capability, authorization, and presentation remain distinct

ESHIC may expose a human-facing action.

That does not mean:

```text
the machine has a generic capability declaration
the caller is authorized
the action has been approved
```

Those are separate concerns.

---

## 6. Contract Model

ESHIC is organized into the following logical layers:

```text
ESHIC
├─ Discovery Contract
├─ Runtime & Health Contract
├─ Attention Contract
├─ Service Data Contract
├─ Settings Contract
├─ Human Action Contract
├─ UI Schema Contract
├─ Compatibility Contract
└─ Integration Boundary Rules
```

A future normative specification may serialize some layers together.

The architectural separation remains important even if the wire representation is compact.

---

## 7. Discovery Contract

The Discovery Contract allows a Hub to determine:

- whether a component is ESHIC-compatible;
- how it is identified;
- which ESHIC version it implements;
- how the Hub may bind to its adapter.

Conceptually the discovery declaration should cover:

```text
stable service identity
display name
service version
ESHIC version / compatibility
Hub integration opt-in
service category
optional icon identity
optional display ordering hint
binding metadata
```

Illustrative, non-normative shape:

```yaml
service:
  id: example.scheduler
  name: Scheduler Service
  version: "1.4.0"

eshic:
  version: "0.1"
  enabled: true
  category: automation
```

A Hub must be able to distinguish:

- compatible service;
- unsupported ESHIC version;
- ESHIC-disabled service;
- malformed declaration;
- installed service whose runtime is unavailable.

Discovery does not imply invocation authority.

---

## 8. Mandatory Hub Core

ESHIC defines a deliberately small common core.

The Hub needs enough common meaning to answer:

```text
What is this service?
Is it running/operable?
Is it healthy?
Does it need attention?
Is the ESHIC contract compatible?
```

Conceptual areas:

```text
identity
runtime summary
health summary
attention summary
contract compatibility
```

Illustrative fields:

```text
identity.service_id
identity.name
identity.version

runtime.status
runtime.health

attention.required
attention.summary

contract.eshic_version
contract.compatibility
```

The common core must not become a generic replacement for each service's domain model.

---

## 9. Service Data Contract

Operational data outside the common core belongs to the service-specific data model.

Example:

```text
scheduler
├─ state
├─ next_run_at
└─ schedule_metadata

execution
├─ current_state
├─ current_started_at
├─ last_result
└─ last_completed_at

queue
├─ pending_count
├─ active_count
└─ last_drained_at
```

Current and historical states may coexist:

```text
Current state: RUNNING
Previous result: SUCCESS
Previous completion: 2026-08-22 21:30
```

ESHIC must not force those into one ambiguous field.

### 9.1 Arbitrary cardinality

A conforming service may expose:

```text
status × N
timestamp × N
metric × N
setting × N
action × N
section × N
collection × N
```

subject to schema validity and Hub resource limits.

---

## 10. Hub-Level Health vs Domain State

ESHIC separates Hub summary health from service-domain state.

### 10.1 Hub-level state

The Hub needs a compact summary such as:

```text
HEALTHY
RUNNING
DEGRADED
STOPPED
NEEDS_ATTENTION
UNAVAILABLE
```

The final normative enumeration is deferred.

### 10.2 Domain state

A service may simultaneously expose:

```text
Scheduler      RUNNING
Worker         IDLE
Queue          WAITING
Last cycle     SUCCESS
```

### 10.3 Aggregation ownership

The service-side ESHIC adapter owns translation from domain state to Hub-level summary.

```text
domain subsystem error
        ↓
service adapter evaluates impact
        ↓
runtime.health = DEGRADED
```

The Hub should not contain service-specific inference logic.

---

## 11. Attention Contract

Services must be able to represent operator-relevant attention without requiring the Hub to understand service internals.

Conceptual attention data:

```text
attention.required
attention.severity
attention.code
attention.summary
attention.safe_detail
attention.since
attention.related_action_ids
```

The Hub should be able to distinguish:

- healthy;
- intentionally stopped;
- temporarily busy;
- degraded but usable;
- operator action required;
- unreachable/unavailable.

Attention is not authorization.

---

## 12. Human Action Contract

ESHIC represents **human-facing semantic actions**.

Examples:

```text
run_now
restart
reconnect
save_settings
run_diagnostics
refresh
open_management_shell
```

An action declaration should be able to express:

- stable action identifier;
- human-readable label;
- description/help text;
- current enabled/disabled state;
- disabled reason;
- confirmation requirement;
- destructive/impact semantics;
- optional parameters;
- asynchronous execution state;
- progress when meaningful;
- success/failure result;
- safe operator-facing failure detail;
- optional reference to a machine capability identifier.

### 12.1 Action availability is not machine capability discovery

ESHIC answers:

> Should the Hub present this operator action and how should the interaction behave?

A generic machine capability standard answers a different question.

ESHIC therefore does not define generic machine Effect vocabularies, generic capability manifests, or delegated machine authority.

---

## 13. Settings Contract

Settings are exposed as typed semantic data rather than service-owned Hub UI.

Representative field semantics:

```text
boolean
string
number
secret reference
enum/select
duration
timestamp/time
timezone
path
read-only value
```

A setting declaration should be able to carry:

- stable field ID;
- label;
- description;
- type;
- current value;
- editable/read-only state;
- validation rules;
- allowed values;
- sensitivity classification;
- save behavior;
- restart/reload requirement where applicable.

Sensitive raw values should not be serialized into ordinary Hub-visible schemas or logs when a reference or secure channel can be used instead.

---

## 14. UI Schema Contract

The UI Schema Contract enables a Hub to generate service control surfaces without bespoke service-specific UI code.

A service does not provide a finished Hub screen.

It provides a semantic description of:

- sections;
- groups;
- fields;
- data bindings;
- actions;
- settings;
- ordering;
- importance;
- visibility conditions;
- collections.

Illustrative primitive families:

```text
layout:
  section
  group

display:
  status
  metric
  timestamp
  text
  key_value
  warning
  notice

settings:
  toggle
  select
  input

operations:
  action

data:
  log
  table
  list
```

The final primitive set belongs in a future normative schema.

### 14.1 Illustrative schema

```yaml
ui:
  sections:
    - id: runtime
      title: Runtime
      fields:
        - type: status
          bind: runtime.status
          label: Service status

        - type: timestamp
          bind: execution.last_completed_at
          label: Last completed

    - id: queue
      title: Queue
      fields:
        - type: metric
          bind: queue.pending_count
          label: Pending

        - type: action
          action: run_now
          label: Run now
```

### 14.2 Rendering model

```text
Service-specific data
        +
Declarative UI schema
        ↓
ESHIC semantic renderer
        ↓
Hub semantic components
        ↓
Hub-owned design system
        ↓
Generated control surface
```

A service should not select concrete visual components except where a future ESHIC version explicitly defines a constrained semantic mapping.

---

## 15. Semantic Presentation Rules

Prefer declarations such as:

```text
This is a warning.
This value is a metric.
This value is a timestamp.
This action is destructive.
This setting is boolean.
This value is read-only.
```

Avoid declarations such as:

```text
Make this red.
Use a 16 px radius.
Use this CSS class.
Animate this for 250 ms.
Use this font.
```

ESHIC standardizes semantic intent, not aesthetic implementation.

---

## 16. Integration Boundary

Preferred conceptual structure:

```text
Service Core
    │
    ├─ Existing service-owned UI, if any
    │
    └─ ESHIC Adapter
            │
            └─> Service Hub
```

The ESHIC adapter is expected to expose:

- identity/version;
- runtime summary;
- health summary;
- attention state;
- service-specific data;
- settings;
- human-facing actions;
- UI schema;
- compatibility metadata.

The service's existing UI may coexist.

ESHIC does not require that the Hub become the service's only UI.

---

## 17. Transport Independence

ESHIC 0.1 does not select a final transport.

Potential local or remote bindings may include:

- named pipes;
- authenticated local IPC;
- local HTTP;
- JSON-RPC;
- WebSocket;
- another transport that preserves the ESHIC semantics and security boundary.

The semantic contract must not depend on transport-specific details.

Binding profiles may be standardized separately.

---

## 18. Security and Authority Boundary

A Service Hub is an operator interface, not an automatic grant of authority.

ESHIC preserves:

```text
Human-facing action
≠
Machine capability
≠
Authorization
≠
Operator approval
```

A service may advertise an action while the current operator is not authorized to invoke it.

A future normative specification should define how action presentation can communicate:

- unavailable state;
- authorization required;
- confirmation required;
- human gate required;
- destructive/security-sensitive impact.

Actual authorization/delegation semantics belong outside ESHIC.

Credentials and secrets remain outside ordinary UI schemas, logs, and public repository content.

---

## 19. Interoperability with Machine Capability Standards

ESHIC may interoperate with an external capability standard such as CME.

The relationship is optional and one-way at the UI semantic boundary.

Illustrative concept:

```yaml
actions:
  - id: reconnect_provider
    label: Reconnect
    capability_ref: "chatgpt.provider.reconnect"
```

`capability_ref` means:

> This human-facing action corresponds to an externally declared machine capability.

It does not make CME mandatory for ESHIC conformance.

ESHIC must not duplicate:

- machine Effect taxonomies;
- generic capability manifests;
- invocation-resolution semantics;
- delegated machine authority.

See `docs/interoperability/CME_INTEROPERABILITY.md`.

---

## 20. Error and Failure Semantics

The service should expose safe operator-relevant failures without leaking implementation details unnecessarily.

Useful concepts include:

```text
stable condition/error code
operator-facing summary
safe detail
timestamp
related attention state
related action
retry/recovery hint
```

Unknown or malformed states should fail explicitly rather than rendering misleading controls.

---

## 21. Contract Versioning

Initial designation:

```text
ESHIC 0.1 Experimental
```

Principles:

1. 0.x versions may evolve substantially.
2. A service declares the ESHIC version it implements.
3. A Hub validates compatibility before binding/rendering.
4. Unknown incompatible versions fail closed.
5. Backward compatibility rules must be explicit before ESHIC 1.0.
6. New semantics should arise from real integration requirements rather than speculative breadth.
7. A stable authority pointer should identify the current baseline.

---

## 22. Validation and Conformance Direction

ESHIC conformance should become machine-testable.

A validator should eventually verify:

- valid service identity;
- recognized ESHIC version;
- required core fields;
- valid data bindings;
- valid UI primitive types;
- no missing binding targets;
- valid action references;
- valid settings declarations;
- valid visibility conditions;
- schema constraints;
- reserved namespace rules;
- unsupported required features.

A malformed declaration should not partially render into an ambiguous state without clear diagnostics.

---

## 23. Reference Conformance Scenarios

ESHIC 0.1 should be tested against at least:

### Scenario A — Scheduled/worker service

Can represent:

- current state;
- next scheduled run;
- previous result;
- multiple timestamps;
- multiple metrics;
- editable settings;
- manual run;
- logs/history;
- attention states.

### Scenario B — Message/bridge service

Can represent:

- external connection state;
- internal bridge state;
- recovery state;
- multiple timestamps;
- reconnect action;
- diagnostics;
- attention/operator intervention.

### Scenario C — Resource telemetry service

Can represent:

- collector state;
- target list;
- metrics/history;
- attention/anomaly summary;
- read-only observation semantics;
- diagnostics.

### Scenario D — Unknown service

A new service can be integrated using existing ESHIC semantic primitives without adding a service-name rendering branch to the Hub.

---

## 24. Acceptance Criteria for the 0.1 Architecture Baseline

ESHIC 0.1 is acceptable as an architecture baseline when:

1. services remain independent;
2. Hub/service implementation technologies remain independent;
3. service identity and ESHIC version are explicit;
4. the common Hub core remains small;
5. service-specific data retains real domain meaning;
6. Hub-level health and domain state remain distinct;
7. operator attention is expressible;
8. settings are typed semantically;
9. human-facing actions are semantic and not UI-handler-specific;
10. supported control surfaces can be described declaratively;
11. a Hub can render supported schemas without service-specific rendering code;
12. services do not own Hub visual styling;
13. transport remains replaceable;
14. human-facing actions remain distinct from machine capability and authorization;
15. ESHIC can optionally reference external machine capability identifiers without absorbing their standards.

---

## 25. Explicit Non-Goals for 0.1

ESHIC 0.1 does not attempt to:

- implement a Service Hub application;
- define one design system;
- define visual polish or motion;
- build bespoke service screens;
- create a plugin marketplace;
- solve arbitrary internet-scale service discovery;
- build a universal dependency resolver;
- orchestrate software updates;
- standardize all service types;
- define a universal machine-capability system;
- define an authorization/delegation system;
- select every UI primitive in advance;
- select one transport prematurely.

---

## 26. Design Guardrail

The primary scope guard is:

> **ESHIC defines a stable semantic boundary between services and Service Hubs.  
> It does not build either side in advance.**

A feature belongs in ESHIC when it is necessary for generic service-to-Hub integration.

A valid software feature is not automatically an ESHIC concern.

---

## 27. Baseline Decision Record

| Area | Decision |
|---|---|
| Contract name | Ecosystem Service Hub Integration Contract |
| Short name | ESHIC |
| Maturity | 0.1 Experimental |
| Service architecture | Independent modules |
| Hub implementation stack | Not specified |
| Service implementation stack | Not specified |
| Common data model | Small mandatory core + service-specific data |
| UI model | Declarative semantic schema |
| Styling ownership | Service Hub |
| Service-owned UI | Optional / may coexist |
| Human actions | ESHIC semantic actions |
| Machine capability standard | External / optional interoperability |
| Authorization | External to ESHIC |
| Transport | Deferred / binding-independent |
| Validation | Intended to become machine-testable |
| Reference implementation | Non-normative and external to the core standard |

---

## 28. Target Outcome

A conforming Service Hub should be able to:

```text
Discover
→ Validate
→ Bind
→ Understand
→ Render
→ Operate
```

a conforming service without asking:

> How do I build a custom integration screen for this specific service?

If the required semantics already exist in ESHIC, the Hub should already know how to integrate them.

That is the purpose of ESHIC.
