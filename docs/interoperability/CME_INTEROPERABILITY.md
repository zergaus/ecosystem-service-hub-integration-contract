# ESHIC ↔ CME Interoperability

**Status:** Informative / non-normative  
**Applies to:** ESHIC 0.1 Experimental architecture baseline

## 1. Purpose

ESHIC and CME address adjacent but different integration concerns.

They should be composable without either standard absorbing the other.

## 2. Boundary

```text
ESHIC
= service-to-Hub human integration semantics

CME
= machine-readable provider capability / Effect / availability semantics
```

ESHIC answers questions such as:

- What should a Service Hub show?
- What operational state should a human see?
- What setting fields exist?
- What operator action should be presented?
- How should service data be grouped semantically?

CME answers questions such as:

- What machine capability does a provider support?
- What Effects does that capability have?
- Is that capability currently available?
- What capability set is required for a machine invocation?

## 3. Optional linkage

ESHIC may allow a human-facing action to reference an external machine capability ID.

Concept:

```yaml
actions:
  - id: reconnect
    label: Reconnect
    capability_ref: "provider.reconnect"
```

The reference is informative/integrative.

It does not import CME semantics into ESHIC.

## 4. Authority remains separate

Neither ESHIC nor CME should silently become the authorization system.

```text
ESHIC action exists
CME capability exists
caller authority exists
operator approval exists
```

are distinct facts.

## 5. UI Effects are not ESHIC UI schema

A CME Effect such as:

```text
ui.observe
ui.control
```

describes what machine execution affects.

It does not describe Hub rendering.

ESHIC UI schema describes semantic human presentation.

## 6. Independence

Baseline ESHIC conformance does not require CME.

Baseline CME conformance does not require ESHIC.

A product ecosystem may adopt both.
