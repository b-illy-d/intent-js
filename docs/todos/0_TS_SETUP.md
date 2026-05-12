# TODO 0: Establish shared TypeScript/package conventions

## Commit

```txt
Establish workspace TypeScript package conventions
```

## Goal

Create one boring, repeatable convention for building, typechecking, exporting, and publishing each workspace package.

## Scope

Expected files for this commit:

```txt
package.json
tsconfig.base.json
.gitignore
packages/core/package.json
packages/core/tsconfig.json
packages/test/package.json
packages/test/tsconfig.json
packages/cli/package.json
packages/cli/tsconfig.json
```

Do not implement runtime APIs in this commit.

## Decisions

- Source lives in `src/`.
- Build output goes to `dist/`.
- Packages export built JS and generated declarations from `dist/`, not raw TS from `src/`.
- Packages publish only `dist/`.
- All packages extend one shared root TypeScript config.
- Root scripts delegate to packages with pnpm recursive commands.
- Existing placeholder test scripts should keep failing for now.

## Root package conventions

Add/keep scripts:

```json
{
  "scripts": {
    "build": "pnpm -r build",
    "typecheck": "pnpm -r typecheck",
    "clean": "pnpm -r clean",
    "test": "pnpm -r test"
  }
}
```

## Shared TypeScript config

Create `tsconfig.base.json` with strict ESM-oriented defaults suitable for Node and package builds.

Expected themes:

- strict typechecking
- declaration output enabled
- source maps enabled
- modern ES target
- Node-compatible ESM resolution
- consistent casing checks
- skip library checking

## Per-package TypeScript config

Each package gets a `tsconfig.json` that extends the root config and sets:

```json
{
  "compilerOptions": {
    "rootDir": "src",
    "outDir": "dist",
    "tsBuildInfoFile": "dist/.tsbuildinfo"
  },
  "include": ["src/**/*.ts"]
}
```

## Package export conventions

Library packages should export built files:

```json
{
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.js"
    }
  },
  "files": ["dist"]
}
```

CLI package should also expose the binary:

```json
{
  "bin": {
    "intent": "./dist/index.js"
  }
}
```

## Per-package scripts

Each package should have:

```json
{
  "scripts": {
    "build": "tsc -p tsconfig.json",
    "typecheck": "tsc --noEmit -p tsconfig.json",
    "clean": "rm -rf dist",
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

## Verification

Run checks only relevant to modified packages:

```bash
pnpm -r typecheck
pnpm -r build
```

Do not run tests in this commit because placeholder package test scripts intentionally fail.

## Acceptance criteria

- `pnpm -r typecheck` passes.
- `pnpm -r build` passes.
- Each package emits JS and declarations to `dist/`.
- Package exports point at `dist/`.
- Test scripts remain intentionally failing placeholders.
