# ESHIC

**Ecosystem Service Hub Integration Contract**

ESHIC is an open, implementation-agnostic contract for integrating independent services into a shared Service Hub with declarative discovery, operational state, human-facing controls, settings, and UI semantics.

Its central idea is simple:

> A service declares **what it is, what it exposes, and what an operator can do**.  
> A Service Hub decides **how that meaning is presented**.

ESHIC is designed to let heterogeneous services participate in one coherent operator experience without forcing them to share:

- an implementation language;
- a process model;
- a UI framework;
- a design system;
- a transport;
- a release cadence.

## Current status

ESHIC is currently experimental.

The stable authority pointer is:

[`ESHIC_CURRENT.md`](ESHIC_CURRENT.md)

The current architecture baseline is:

[`specification/0.1/ESHIC_0.1_BLUEPRINT.md`](specification/0.1/ESHIC_0.1_BLUEPRINT.md)

## What ESHIC covers

ESHIC focuses on the **service-to-Hub human integration plane**:

```text
service discovery
service identity
runtime / health summary
attention state
service-specific operational data
settings semantics
human-facing action semantics
declarative UI schema
compatibility / version negotiation
```

ESHIC does **not** define machine authorization or generic machine capability semantics.

## Relationship to CME

ESHIC and CME are complementary, not competing standards.

```text
ESHIC
= What should a Service Hub understand and present to a human?

CME
= What machine capabilities does a provider support, what Effects do they have,
  and are those capabilities currently available?
```

ESHIC may optionally reference a machine capability identifier, such as a CME capability, from a human-facing action.

CME is not required for baseline ESHIC conformance.

See:

[`docs/interoperability/CME_INTEROPERABILITY.md`](docs/interoperability/CME_INTEROPERABILITY.md)

## Reference implementation

ESHIC does not prescribe a UI stack.

A conforming Service Hub may be implemented with any suitable native or web stack.

The project that originally motivated ESHIC uses a `.NET + WebView2 + React + TypeScript + Fluent 2` Service Hub architecture. That stack is a **reference implementation choice**, not part of the ESHIC standard.

See:

[`docs/reference/EXECUTEOFFICE_REFERENCE_IMPLEMENTATION.md`](docs/reference/EXECUTEOFFICE_REFERENCE_IMPLEMENTATION.md)

## Origin

ESHIC originated as an internal Service Hub integration contract in the ExecuteOffice project and was later generalized into this independent specification.

See:

[`docs/history/ORIGIN_AND_EXTRACTION.md`](docs/history/ORIGIN_AND_EXTRACTION.md)

## Design principle

ESHIC prefers semantic declarations such as:

```text
this is a warning
this is a metric
this is a timestamp
this is a destructive human-facing action
this setting is read-only
```

over presentation instructions such as:

```text
make this red
use this font
render this with this CSS class
use a 16px radius
```

The Service Hub owns presentation.

The service owns domain meaning.

## License

ESHIC is licensed under the **Apache License 2.0**. See [`LICENSE`](LICENSE).
