# Origin and Extraction

**Status:** Historical / informative

ESHIC originated inside the ExecuteOffice project as an internal architecture contract named:

> ExecuteOffice Service Hub Integration Contract

The original 0.1 baseline was designed to make independently implemented ExecuteOffice services integrable into a future common Service Hub.

That work established the core ideas that remain central to the independent standard:

- independent service modules;
- Hub-owned presentation;
- semantic rather than visual declarations;
- a small common runtime/health core;
- service-specific domain data;
- typed settings;
- semantic operator actions;
- declarative UI schema;
- transport independence;
- separation between capability and authorization.

## Why ESHIC became independent

As additional independent services began depending on the same integration boundary, keeping ESHIC inside one product repository would have made the standard look product-specific.

The contract was therefore extracted and generalized as:

> **Ecosystem Service Hub Integration Contract**

The independent repository is now intended to be the authority for ESHIC itself.

ExecuteOffice becomes an adopter/reference implementation rather than the owner of ESHIC semantics.

## What changed during extraction

The independent baseline intentionally removes product-specific normative assumptions such as:

```text
.NET host
WebView2
React
TypeScript
Fluent 2
Mail Intelligence-specific state
Slack Bridge-specific state
```

Those may remain useful reference implementation material, but they are not requirements of ESHIC.

The extraction also clarifies the boundary between:

```text
ESHIC human/Hub semantics
and
generic machine capability standards such as CME
```

## Historical authority

The former ExecuteOffice-hosted ESHIC documents remain historical evidence of origin.

Future ESHIC version authority should live in this independent repository.
