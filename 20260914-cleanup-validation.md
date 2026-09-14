# Model visibility validation

Source: `14d04ecac8ed60e70202f0ebbba272ede2cd3cc4`, based on upstream `d1b705a0cd91617a5707fae25d80cb0be3057950`.

Host: ubuntu-pcs. Node 26.8.1, npm 11.19.0.

The cleanup moves browser-test setup and assertions into the existing helper, removes unrelated Metro watcher tuning, and corrects the documentation for explicit hidden model choices. Production behaviour is unchanged.

The browser run uses an isolated daemon and fixture catalogue. Provider-session creation, including auxiliary naming, is guarded. It verifies UI behaviour and routing without inference.

Linux and Windows Electron/Chrome checks from the previous PR description were from earlier revisions. macOS, Android and iOS were not tested.

## Browser scenarios

```bash
E2E_RECORD_VIDEO=1 npm run test:e2e --workspace=@getpaseo/app -- e2e/browser/model-visibility.spec.ts --workers=1 --max-failures=1 --output=/tmp/paseo-pr-4544-cleanup/playwright-final
```

Exit status: 0. Raw output:

```text

> @getpaseo/app@0.8.0 test:e2e
> playwright test --project=browser e2e/browser/model-visibility.spec.ts --workers=1 --max-failures=1 --output=/tmp/paseo-pr-4544-cleanup/playwright-final

(node:2718922) [DEP0205] DeprecationWarning: `module.register()` is deprecated. Use `module.registerHooks()` instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
[metro] Starting project at /home/syslord/dev/paseo/packages/app
[metro] Using src/app as the root directory for Expo Router.
[metro] Experimental Expo Autolinking module resolver is enabled.
[metro] React Compiler enabled
[metro] Starting Metro Bundler
[metro] Waiting on http://localhost:45361
[metro] Logs for your project will appear below.
[metro] Web packages/app/index.ts ░░░░░░░░░░░░░░░░  0.0% (0/1)
[metro] Web Bundled 1500ms packages/app/index.ts (5134 modules)
[e2e] Metro warmed on port 45361

Running 3 tests using 1 worker

(node:2719897) Warning: The 'NO_COLOR' env is ignored due to the 'FORCE_COLOR' env being set.
(Use `node --trace-warnings ...` to show where the warning was created)
(node:2719897) [DEP0205] DeprecationWarning: `module.register()` is deprecated. Use `module.registerHooks()` instead.
(node:2719897) Warning: The 'NO_COLOR' env is ignored due to the 'FORCE_COLOR' env being set.
(Use `node --trace-warnings ...` to show where the warning was created)
[metro] Web Bundled 77ms packages/app/index.ts (1 module)
[metro]  LOG  [web] Logs will appear in the browser console
[metro] Web Bundled 37ms packages/app/src/attachments/web/indexeddb-attachment-store.ts (1 module)
[metro] Web Bundled 69ms packages/app/index.ts (1 module)
[metro] Web packages/app/src/attachments/web/indexeddb-attachment-store.ts ░░░░░░░░░░░░░░░░  0.0% (0/1)
[metro] Web Bundled 4ms packages/app/src/attachments/web/indexeddb-attachment-store.ts (1 module)
[metro]  LOG  [web] Logs will appear in the browser console
[metro] Web Bundled 80ms packages/app/index.ts (1 module)
[metro] Web Bundled 3ms packages/app/src/attachments/web/indexeddb-attachment-store.ts (1 module)
[metro]  LOG  [web] Logs will appear in the browser console
  ✓  1 [browser] › e2e/browser/model-visibility.spec.ts:22:5 › hiding a model removes it from selectors and restoring brings it back (12.0s)
[metro] Web Bundled 66ms packages/app/index.ts (1 module)
[metro] Web Bundled 3ms packages/app/src/attachments/web/indexeddb-attachment-store.ts (1 module)
[metro]  LOG  [web] Logs will appear in the browser console
  ✓  2 [browser] › e2e/browser/model-visibility.spec.ts:37:7 › hide-all rejects without side effects and clears the error after restore (4.1s)
[metro] Web Bundled 88ms packages/app/index.ts (1 module)
[metro] Web packages/app/src/attachments/web/indexeddb-attachment-store.ts ░░░░░░░░░░░░░░░░  0.0% (0/1)
[metro] Web Bundled 4ms packages/app/src/attachments/web/indexeddb-attachment-store.ts (1 module)
[metro]  LOG  [web] Logs will appear in the browser console
  ✓  3 [browser] › e2e/browser/model-visibility.spec.ts:37:7 › hide-all rejects without side effects and clears the error after profile (7.7s)
[e2e] Metro stopped

  3 passed (33.4s)
```

## Lint

```bash
npm run lint -- --ignore-pattern tmp/
```

Exit status: 0. Raw output:

```text

> paseo@0.8.0 lint
> oxlint --ignore-pattern tmp/

Found 0 warnings and 0 errors.
Finished in 652ms on 4204 files with 177 rules using 20 threads.
```

## Formatting

```bash
npm run format -- '!tmp/**'
```

Exit status: 0. Raw output:

```text

> paseo@0.8.0 format
> oxfmt . !tmp/**

Finished in 908ms on 4488 files using 20 threads.
```

## Commit hooks: formatting, lint and typecheck

```bash
git commit --amend --no-edit
```

Exit status: 0. Raw output:

```text
╭───────────────────────────────────╮
│ lefthook v2.1.6  hook: pre-commit │
╰───────────────────────────────────╯
┃  lint ❯


> paseo@0.8.0 lint
> oxlint packages/app/e2e/support/global-setup.ts

Found 0 warnings and 0 errors.
Finished in 15ms on 1 file with 177 rules using 20 threads.

┃  format ❯


> paseo@0.8.0 format:check:files
> oxfmt --check docs/custom-providers.md packages/app/e2e/support/global-setup.ts

Checking formatting...

All matched files use the correct format.
Finished in 163ms on 2 files using 20 threads.

┃  typecheck ❯


> paseo@0.8.0 typecheck
> npm run typecheck --workspaces --if-present


> @getpaseo/expo-two-way-audio@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/highlight@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/plugin@0.8.0 typecheck
> tsgo --noEmit && tsgo -p tsconfig.examples.json --noEmit


> @getpaseo/protocol@0.8.0 pretypecheck
> npm run generate:validators


> @getpaseo/protocol@0.8.0 generate:validators
> node scripts/generate-validation-aot.mjs

generated src/generated/validation/ws-outbound.aot.ts from codegen/ws-outbound.compile.ts (WSOutboundMessageSchema)

> @getpaseo/protocol@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/client@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/server@0.8.0 typecheck
> tsgo -p tsconfig.server.typecheck.json --noEmit


> @getpaseo/app@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/relay@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/website@0.8.0 typecheck
> tsgo --noEmit


> @getpaseo/desktop@0.8.0 typecheck
> tsgo --noEmit -p tsconfig.json


> @getpaseo/cli@0.8.0 typecheck
> tsgo --noEmit



  ────────────────────────────────────
summary: (done in 8.67 seconds)
✓ lint (0.11 seconds)
✓ format (0.26 seconds)
✓ typecheck (8.66 seconds)
[feat/model-visibility 14d04ecac] Add per-provider model visibility controls
 Date: Mon Sep 14 16:51:04 2026 +1200
 64 files changed, 4400 insertions(+), 490 deletions(-)
 create mode 100644 packages/app/e2e/browser/model-visibility.spec.ts
 create mode 100644 packages/app/e2e/browser/provider-model-row-layout.spec.ts
 create mode 100644 packages/app/e2e/support/helpers/model-row-layout.ts
 create mode 100644 packages/app/e2e/support/helpers/model-visibility-fixture.ts
 create mode 100644 packages/app/e2e/support/helpers/model-visibility.ts
 create mode 100644 packages/app/src/components/ui/switch-input.test.ts
 create mode 100644 packages/app/src/components/ui/switch-input.ts
 delete mode 100644 packages/app/src/components/ui/switch.test.tsx
 create mode 100644 packages/app/src/data/daemon-config.test.ts
 create mode 100644 packages/app/src/hooks/use-model-visibility.ts
 create mode 100644 packages/app/src/provider-selection/model-visibility.test.ts
 create mode 100644 packages/app/src/provider-selection/model-visibility.ts
 create mode 100644 packages/app/src/screens/new-workspace-chat.test.ts
 create mode 100644 packages/app/src/screens/new-workspace-chat.ts
 create mode 100644 packages/server/src/server/test-utils/catalog-daemon-process.ts
```

## Check scope

The first unfiltered lint command scanned pre-existing untracked JavaScript bundles under `tmp/native-linux-qa/renderer/` and failed. The successful root lint and formatting commands exclude `tmp/`; no tracked source was excluded and none of those existing scratch files was changed. The normal commit hooks also passed all their checks.

The full local test suite was not run, following the repository instructions. These local checks do not establish GitHub CI completion or testing on other platforms.

## History verification

The final commit tree exactly matches the staged tree saved before amending. Compared with `4bfe3430c00f994947d3be06f44f7a3d94349b35`, only the four files listed below differ. The global setup file now matches upstream exactly.

There is one commit above `d1b705a0cd91617a5707fae25d80cb0be3057950`, with no tool coauthor trailers. A fresh independent review and its one delta both returned OK, with no HIGH or CRITICAL findings.

## Source hashes

```text
d4781d28292f214faf4989b7ccb9bfc4c913cca42370a460356ce25e17c52bed  packages/app/e2e/browser/model-visibility.spec.ts
4e62815df34fe3d8d13c92a2dca53dff71490820001db9c7a15e8db20bb672b8  packages/app/e2e/support/helpers/model-visibility.ts
65ad22ac5524ded6c9b2b0dd1dfafe10c623c075a59391ab90bd3e4820c1f9c7  packages/app/e2e/support/global-setup.ts
2ebda215bf1d6b6d0f083318733480be01d8bfbf1c6a2a82ea0eb51fdd2cda0a  docs/custom-providers.md
```
