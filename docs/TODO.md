# IntentJS MVP TODO

Each TODO is one git commit. This is the 10k ft plan; details get planned commit-by-commit.

- [*] Establish shared TypeScript/package conventions for the workspace.
- [ ] Implement `@intent-js/core` behavior status types and `behavior.*` declarations.
- [ ] Implement `@intent-js/core` `claim(fn, metadata)` API.
- [ ] Implement `@intent-js/test` classified test helper API.
- [ ] Define config schema and implement `intent init`.
- [ ] Implement config loading and opted-in boundary discovery.
- [ ] Implement AST scanning for behavior declarations.
- [ ] Implement AST scanning for public claims.
- [ ] Enforce missing-claim and empty-classification failures.
- [ ] Implement classified test scanning and behavior-to-test linking.
- [ ] Generate deterministic `.intent.json` manifests.
- [ ] Enforce lifecycle rules for `compat` and `characterized` behaviors.
- [ ] Add intended-behavior coverage warnings.
- [ ] Implement semantic `intent diff` output.
- [ ] Implement `intent check --base` as the CI gate.
- [ ] Add the first end-to-end demo fixture from `docs/MVP.md`.
- [ ] Harden CLI UX, exit codes, and help output.
- [ ] Add focused tests for core, test helpers, scanner, manifest, diff, and check.
