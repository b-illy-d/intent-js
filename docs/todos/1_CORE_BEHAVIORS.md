# TODO 1: Implement `@intent-js/core` behavior declarations

## Commit

```txt
Implement core behavior declarations
```

## Goal

Add the core metadata model for behavior contracts and expose the MVP `behavior.*` declaration API.

## Scope

Expected files for this commit:

```txt
packages/core/src/index.ts
```

If the implementation gets too large for one file, ask before splitting into additional source files.

Do not implement `claim(...)`, test helpers, scanner logic, manifests, or CLI behavior in this commit.

## API to support

```ts
import { behavior } from "@intent-js/core";

export const RefundCannotExceedCaptured = behavior.intended({
  id: "RefundCannotExceedCaptured",
  summary: "Partial refunds cannot exceed captured payment.",
  owner: "payments",
  severity: "critical"
});

export const CrossCurrencyRefund = behavior.unsupported({
  id: "CrossCurrencyRefund",
  summary: "Refund currency must match captured payment currency.",
  owner: "payments",
  httpStatus: 422
});

export const LegacyStringAmounts = behavior.compat({
  id: "LegacyStringAmounts",
  summary: "v1 clients may send refund amount as a string.",
  owner: "payments",
  until: "2026-09-01",
  metric: "refund.legacy_string_amount.used"
});
```

## Behavior statuses

Implement status support for:

- `intended`
- `unsupported`
- `unspecified`
- `compat`
- `characterized`

## Type model

Define exported types for:

- `BehaviorStatus`
- status-specific metadata input types
- status-specific behavior output types
- a union `Behavior`

Recommended shape:

```ts
type BehaviorStatus =
  | "intended"
  | "unsupported"
  | "unspecified"
  | "compat"
  | "characterized";
```

All behaviors should have at least:

- `id`
- `status`
- `summary`
- `owner`

## Lifecycle metadata

`compat` and `characterized` should include lifecycle fields in the type model.

MVP expected fields:

- `until` or review/expiry equivalent
- `metric` for `compat`

This commit should model the required metadata types, but full lifecycle enforcement belongs to the later CLI/check commit.

## Runtime behavior

The declaration helpers should be tiny and deterministic:

- accept metadata
- return metadata plus the literal `status`
- preserve useful literal types where practical
- avoid runtime validation for now unless it is essentially free

Example expected runtime shape:

```ts
behavior.intended({ id: "A", summary: "...", owner: "team" })
// => { status: "intended", id: "A", summary: "...", owner: "team" }
```

## Export conventions

Everything needed by downstream packages should be exported from `packages/core/src/index.ts`.

## Verification

Run checks only for the modified package:

```bash
pnpm --filter @intent-js/core typecheck
pnpm --filter @intent-js/core build
```

Do not run tests because placeholder test scripts intentionally fail.

## Acceptance criteria

- `@intent-js/core` exports `behavior`.
- `behavior.intended`, `.unsupported`, `.unspecified`, `.compat`, and `.characterized` exist.
- Each helper returns an object with the correct literal `status`.
- Behavior and metadata types are exported.
- Core package typechecks and builds.
