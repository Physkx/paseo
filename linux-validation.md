# Native Linux validation

Revision `d2aa74e9c33a62ab0ff4e7e0736959afcc0704ae`, 2026-09-09. Native Linux x86_64 Electron 44.2.0 development build, separate userData, native desktop bridge. Fixture/build Node 26.8.1. Screenshots 1280x900. Process-local `--no-sandbox`; not a packaged or sandbox-enabled test.

Real discovery: Claude 16, Codex 7, OpenCode 13. The fixture installed a self-tested AgentManager.createAgent denial before constructing the daemon. Every normal and auxiliary creation attempt throws QA_INFERENCE_DISABLED. Zero agents were created during this rerun. Explicit selection guard hits prove the requested model reaches the boundary, not a completed provider turn.

| Native check | Result |
| --- | --- |
| Hide all, no saved profiles, click Create | Error rendered, draft preserved, no workspace, agent or creation attempt |
| Keyboard hide-all attempt | No workspace or agent |
| Explicit saved hidden Astra profile | Correct model reaches guard |
| Fresh remembered hidden default | Select model |
| Show Sol in settings, then submit | Visible Sol default, correct model reaches guard |
| Explicitly pick Sol, then hide it | Choice retained, correct model reaches guard |
| Fresh hide-all with saved hidden profile present | Blocked before workspace creation |
| Empty workspace with all models hidden | Empty workspace created, zero agents |

The earlier native run found the bug and accidentally completed a provider turn before its test guard was corrected. This fixed-revision rerun used the stronger guard throughout. Prior Windows results are on fa3782e7f, not this fix. macOS, iOS and Android were not tested.

Controller corrections during this run were a nonexistent client method, a reload returning to the project screen, and a locator selecting a retained hidden error. Subsequent recorded commands used fetchWorkspaces, the visible new-workspace entry and the visible error locator. No product changes were made during runtime testing.

## Reproduction

```bash
npm ci --ignore-scripts --no-audit --no-fund
npm run postinstall
npm run build:server
npm run build:app-deps
PASEO_WEB_PLATFORM=electron npm run build:main --workspace=@getpaseo/desktop
# In packages/app:
PASEO_WEB_PLATFORM=electron CI=1 ../../node_modules/.bin/expo export --platform web --output-dir ../../tmp/native-linux-qa/renderer
# In the checkout root:
npm run typecheck
npm run lint -- --ignore-pattern 'tmp/**'
npm run format
# In packages/app:
npm exec -- vitest run --project=unit --bail=1 src/screens/new-workspace-chat.test.ts
```

The native harness uses an isolated temporary daemon home and an ephemeral loopback port. It never starts or restarts the production daemon. All fixture processes, listeners and homes were cleaned; production and shared Git hook baselines matched afterward.

## Regression failure before the fix

```text

 RUN  v4.1.7 <checkout>/packages/app

Sourcemap for "<checkout>/packages/plugin/dist/paseo-context.js" points to missing source files
Sourcemap for "<checkout>/packages/plugin/dist/client-state.js" points to missing source files
Sourcemap for "<checkout>/packages/plugin/dist/shallow.js" points to missing source files
Sourcemap for "<checkout>/packages/plugin/dist/rpc-context.js" points to missing source files
 ❯ |unit| src/screens/new-workspace-chat.test.ts (1 test | 1 failed) 5ms
     × rejects hide-all before workspace creation or draft handoff 5ms

⎯⎯⎯⎯⎯⎯⎯ Failed Tests 1 ⎯⎯⎯⎯⎯⎯⎯

 FAIL  |unit| src/screens/new-workspace-chat.test.ts > new workspace chat submission > rejects hide-all before workspace creation or draft handoff
AssertionError: promise resolved "undefined" instead of rejecting

- Expected:
Error {
  "message": "rejected promise",
}

+ Received:
undefined

 ❯ src/screens/new-workspace-chat.test.ts:42:43
     40|   it("rejects hide-all before workspace creation or draft handoff", as…
     41|     const { input, effects, drafts } = submission(composer());
     42|     await expect(runCreateChatAgent(input)).rejects.toThrow(i18n.t("pr…
       |                                           ^
     43|     expect(effects).toEqual([]);
     44|     expect(drafts).toEqual([]);

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯[1/1]⎯


 Test Files  1 failed (1)
      Tests  1 failed (1)
   Start at  20:11:14
   Duration  420ms (transform 259ms, setup 26ms, import 317ms, tests 5ms, environment 0ms)

```

## Regression after the fix

```text

 RUN  v4.1.7 <checkout>/packages/app


 Test Files  1 passed (1)
      Tests  9 passed (9)
   Start at  20:15:12
   Duration  787ms (transform 511ms, setup 47ms, import 599ms, tests 9ms, environment 0ms)

```

## Final static checks

```text

> paseo@0.8.0-beta.1 typecheck
> npm run typecheck --workspaces --if-present


> @getpaseo/expo-two-way-audio@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/highlight@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/plugin@0.8.0-beta.1 typecheck
> tsgo --noEmit && tsgo -p tsconfig.examples.json --noEmit


> @getpaseo/protocol@0.8.0-beta.1 pretypecheck
> npm run generate:validators


> @getpaseo/protocol@0.8.0-beta.1 generate:validators
> node scripts/generate-validation-aot.mjs

generated src/generated/validation/ws-outbound.aot.ts from codegen/ws-outbound.compile.ts (WSOutboundMessageSchema)

> @getpaseo/protocol@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/client@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/server@0.8.0-beta.1 typecheck
> tsgo -p tsconfig.server.typecheck.json --noEmit


> @getpaseo/app@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/relay@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/website@0.8.0-beta.1 typecheck
> tsgo --noEmit


> @getpaseo/desktop@0.8.0-beta.1 typecheck
> tsgo --noEmit -p tsconfig.json


> @getpaseo/cli@0.8.0-beta.1 typecheck
> tsgo --noEmit


> paseo@0.8.0-beta.1 lint
> oxlint --ignore-pattern tmp/**

Found 0 warnings and 0 errors.
Finished in 1.0s on 4117 files with 177 rules using 20 threads.

> paseo@0.8.0-beta.1 format
> oxfmt .

Finished in 1003ms on 4398 files using 20 threads.
```

[Raw native controller actions and responses](linux-actions.jsonl).

## Raw fixture creation guard log

```jsonl
{"at":"2026-09-09T08:16:30.806Z","event":"inference-guard-blocked","selfTest":true,"provider":"codex","model":null}
{"at":"2026-09-09T08:16:30.865Z","event":"ready","serverId":"srv_qa_41f2ccb3de134a11b0756cc68eaefcbd","port":40883,"url":"http://127.0.0.1:40883","webBuild":"SUPPLIED","node":"v26.8.1"}
{"at":"2026-09-09T08:18:23.316Z","event":"inference-guard-blocked","selfTest":false,"provider":"claude","model":"claude-haiku-4-5"}
{"at":"2026-09-09T08:18:23.544Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-6-astra"}
{"at":"2026-09-09T08:18:44.666Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-6-astra"}
{"at":"2026-09-09T08:18:44.876Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-6-astra"}
{"at":"2026-09-09T08:19:00.231Z","event":"inference-guard-blocked","selfTest":false,"provider":"claude","model":"claude-haiku-4-5"}
{"at":"2026-09-09T08:19:00.317Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-5.6-sol"}
{"at":"2026-09-09T08:19:44.445Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-6-astra"}
{"at":"2026-09-09T08:19:44.445Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-5.6-sol"}
{"at":"2026-09-09T08:20:02.249Z","event":"inference-guard-blocked","selfTest":false,"provider":"claude","model":"claude-haiku-4-5"}
{"at":"2026-09-09T08:20:02.379Z","event":"inference-guard-blocked","selfTest":false,"provider":"codex","model":"gpt-5.6-sol"}
{"at":"2026-09-09T08:20:38.373Z","event":"stopped"}
```
