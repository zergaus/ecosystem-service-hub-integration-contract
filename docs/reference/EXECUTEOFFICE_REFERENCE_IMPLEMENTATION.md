# ExecuteOffice Reference Implementation

**Status:** Informative / non-normative

ExecuteOffice is the project in which ESHIC was originally developed.

Its current Service Hub UI implementation baseline is:

```text
.NET Host
+ WebView2
+ React
+ TypeScript
+ Fluent 2
  -> Fluent UI React v9
```

This stack demonstrates one way a Hub can consume ESHIC declarations.

It is **not** part of the ESHIC standard.

A conforming ESHIC Hub may use another:

```text
native host
web framework
design system
operating system
transport
```

provided it preserves the required ESHIC semantics and compatibility rules.

The reference architecture is useful because it demonstrates the intended separation:

```text
service
  → ESHIC semantic declaration
  → generic Hub renderer
  → Hub-owned design system
```

rather than:

```text
service
  → service-specific frontend code
  → bespoke Hub screen
```
