# Repository Instructions

This repository contains the public ESHIC standard and supporting documentation.

## Authority

- `ESHIC_CURRENT.md` is the stable authority pointer.
- Versioned ESHIC architecture/specification documents live under `specification/`.
- Informative interoperability, history, and reference material lives under `docs/`.

## Standard evolution

- Do not silently rewrite the meaning of a published version.
- Material semantic changes require a new versioned authority document.
- Update `ESHIC_CURRENT.md` in the same publication work unit when authority changes.
- Keep normative and informative text clearly distinguished.
- Do not make one reference implementation's language, transport, UI framework, or design system normative without explicit cross-implementation justification.

## Operational-fit-first rule

ESHIC is developed first to solve concrete integration needs in the systems that actually use it.

- Real adopter requirements come before speculative universality.
- Do not weaken, complicate, or distort a useful adopter design merely to make the standard appear broader.
- Generalize only after a requirement has been observed in a real integration or when generalization clearly reduces complexity for current adopters.
- Public usefulness is a welcome result of a good working standard, not a reason to force adopters to conform to abstractions they do not need.
- The primary reference ecosystem may exercise new semantics before those semantics are promoted into stable normative ESHIC requirements.
- A proposed abstraction that makes current adopters harder to implement requires explicit justification.

See `docs/rationale/OPERATIONAL_FIT_FIRST.md`.

## Scope discipline

ESHIC owns service-to-Hub human integration semantics.

Do not absorb:

- generic machine capability standards;
- authorization/delegation systems;
- credentials;
- product-specific business logic;
- one Hub implementation stack.

Interop references may be added without merging standards.

## Publication verification

Before reporting a publication complete:

- intended files committed successfully;
- worktree clean;
- local HEAD equals upstream;
- remote branch contains intended commit;
- remote files/diff verified;
- applicable CI/checks verified.
