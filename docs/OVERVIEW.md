# IntentJS

Intent MVP lets TypeScript repositories classify behavior at opted-in public boundaries using normal TypeScript metadata.
Developers declare whether behavior is intended, unsupported, unspecified, legacy-compatible, or merely characterized.
A scanner generates a deterministic .intent.json manifest.
CI produces behavior diffs for PRs and blocks missing claims, invalid lifecycle metadata, expired legacy behavior, and accidental promotion of characterized behavior into intended coverage.
The system requires no new syntax and works incrementally in existing TypeScript repos.

## Core value prop

Intent answers five questions at reliance boundaries:

Question	Why it matters
Is this behavior deliberately promised?	Consumers can rely on it.
Is this behavior deliberately rejected?	Consumers get a typed rejection, not mystery failure.
Is this behavior intentionally unspecified?	Consumers cannot treat incidental behavior as contract.
Is this behavior legacy compatibility?	The team knows it is an obligation with an owner, expiry, and telemetry.
Is this test protecting a contract or freezing current behavior?	Refactors stop being blocked by accidental test canonization.

**Intent is a behavioral diff layer for TypeScript APIs. Types show shape. Tests show examples. Intent shows what the team actually promises.**

