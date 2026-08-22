# Operational Fit First

**Status:** Informative design rationale  
**Applies to:** ESHIC standard evolution

## Principle

ESHIC is public, but public generality is not its primary design objective.

The governing rule is:

> **Build a contract that works well for real adopters first. Generalize from evidence.**

ESHIC was created because independently implemented services needed a coherent, low-friction way to integrate into a shared Service Hub.

That practical need remains the standard's primary design pressure.

## What this means

When deciding whether to add, remove, or generalize a concept, prefer this order:

```text
real adopter requirement
→ simplest correct semantic model
→ validation in real integrations
→ generalization when it reduces repeated complexity
→ public documentation and conformance
```

Do not reverse the order into:

```text
abstract universal model
→ force adopters to fit it
```

## Public does not mean externally optimized

Publishing ESHIC publicly provides useful benefits:

- transparent design;
- external reuse;
- easier interoperability;
- clearer version history;
- independent specification ownership.

Those benefits do not require ESHIC to optimize for hypothetical adopters before its real users.

A feature should not be added solely because:

- another ecosystem might need it someday;
- a more abstract model appears academically cleaner;
- a public standard is expected to cover every possible service shape;
- a generic framework can be imagined.

## Reference adopters

The primary adopter ecosystem is allowed to drive early ESHIC evolution.

Concrete services may expose requirements that later become general ESHIC semantics.

Examples may include:

```text
scheduled / worker services
communication / bridge services
hierarchical management services
resource telemetry / observer services
```

The standard should learn from those integrations.

It should not require those integrations to become worse in order to preserve an unnecessary abstraction.

## Generalization gate

A generalization is justified when at least one of the following is true:

1. multiple real adopters need the same semantic pattern;
2. one adopter exposes a clearly reusable semantic concept without adding material complexity;
3. the generalization removes service-specific Hub logic;
4. the generalization improves compatibility or version safety;
5. the generalization reduces total implementation complexity across the current ecosystem.

A generalization is suspect when it:

- adds concepts no real adopter uses;
- introduces mandatory indirection with no operational benefit;
- forces additional runtime components merely to satisfy the standard;
- duplicates another contract such as CME;
- makes Hub integration harder for the reference ecosystem.

## Relationship to conformance

Conformance exists to protect interoperability, not to punish useful implementations.

Before ESHIC 1.0, conformance rules should be derived from proven integration behavior.

The preferred maturation path is:

```text
architecture principle
→ adopter implementation
→ observed requirement
→ structured semantic model
→ cross-adopter validation
→ normative rule
→ machine-testable conformance
```

## Result

ESHIC should be useful enough that its primary adopters would choose it even if the repository were private.

If it is also useful to others, that is evidence that the design generalized well.
