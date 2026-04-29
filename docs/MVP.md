# IntentJS MVP project

## MVP thesis

MVP should do one thing well:

For opted-in public boundaries, force behavior to be classified, collect those classifications into a deterministic manifest, and show meaningful CI diffs when behavior contracts change.

That means the MVP is mostly metadata + scanner + PR gate, not runtime enforcement.

### The first demo should show this

1. A public function is exported without a claim.
2. CI fails with: Public export issueRefund requires an intent claim.
3. The developer adds claim(...) with intended and unsupported.
4. .intent.json records the behavior.
5. A classified test links to the intended behavior.
6. A later PR removes or changes the intended behavior.
7. intent diff says: Intended behavior removed: RefundCannotExceedCaptured from issueRefund. Potential breaking change.

That is the product.

## MVP scope

Area	MVP version
Behavior declarations	behavior.intended, behavior.unsupported, behavior.unspecified, behavior.compat, behavior.characterized
Claims	One supported pattern: claim(fn, metadata)
Public boundaries	Opt-in config only, such as package exports, API route globs, SDK entrypoints
Tests	Classified test helpers that map tests to behavior IDs
Manifest	Deterministic .intent.json generated from declarations, claims, and tests
CI	intent check and intent diff main...HEAD
Lifecycle enforcement	Required for compat and characterized
Coverage	Warn when public intended behavior has no classified test
Characterized tests	Explicitly do not count as intended coverage

## MVP statuses

Status	MVP reason
intended	The central promise.
unsupported	Makes explicit rejection part of the contract.
unspecified	Prevents accidental reliance.
compat	Captures legacy obligations with lifecycle.
characterized	Lets teams preserve observed behavior during refactors without promoting it to intent.

## MVP package shape

Package	MVP responsibility
@intent/core	behavior, claim, status types, metadata types
@intent/test	Jest/Vitest helpers or wrappers
@intent/cli	Manifest generation, diff, CI checks
@intent/eslint-plugin	Optional fast feedback for missing claims

## MVP API

```typescript
import { behavior, claim } from "@intent/core";

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

export const issueRefund = claim(
  async function issueRefund(req: RefundRequest): Promise<RefundResult> {
    // implementation
  },
  {
    visibility: "public",
    intends: [RefundCannotExceedCaptured],
    rejects: [CrossCurrencyRefund],
    compat: [LegacyStringAmounts]
  }
);
```

## MVP Test API

```typescript
import { it } from "@intent/test";

it.intended(RefundCannotExceedCaptured, () => {
  // protects promised behavior
});

it.unsupported(CrossCurrencyRefund, () => {
  // verifies typed rejection
});

it.compat(LegacyStringAmounts, () => {
  // protects legacy obligation
});

it.characterized(LegacyDiscountRounding, () => {
  // captures current behavior without making it intended
});
```

## MVP Manifest

The manifest should be semantic and stable. Avoid making line numbers part of the canonical diff because they create noise.

```json
{
  "schemaVersion": 1,
  "package": "refunds",
  "behaviors": [
    {
      "id": "RefundCannotExceedCaptured",
      "status": "intended",
      "summary": "Partial refunds cannot exceed captured payment.",
      "owner": "payments",
      "exports": ["issueRefund"],
      "verifiedBy": [
        {
          "file": "refunds.test.ts",
          "testName": "partial refunds cannot exceed captured payment"
        }
      ]
    }
  ]
}
```

Source locations can exist in a debug field or generated report, but they should not drive behavior diffs.

## MVP CI gates

Check	MVP action
Public export in opted-in boundary has no claim	Fail
Public claim has no behavior classification	Fail
compat missing owner, summary, expiry/review date, or metric	Fail
characterized missing owner or expiry/review date	Fail
Expired compat or characterized	Fail unless waived
Public intended behavior has no classified test	Warn
characterized test used as intended coverage	Fail
Manifest changed without intent diff output	Fail or require PR annotation
Public intended behavior removed	Require explicit behavior diff review

## MVP cli

```bash
intent init
intent scan --write
intent diff main...HEAD
intent check --base main
```

Command	Purpose
intent init	Creates config and baseline.
intent scan --write	Generates .intent.json.
intent diff main...HEAD	Shows semantic behavior changes.
intent check --base main	Runs manifest generation, lifecycle checks, coverage checks, and diff checks.

## MVP config

```json{
  "package": "refunds",
  "boundaries": {
    "publicExports": ["src/index.ts"],
    "apiRoutes": ["src/routes/**/*.ts"]
  },
  "tests": {
    "classifiedSuites": ["src/**/*.test.ts"]
  }
}
```


