# Rebase validation

PR #4544 remains draft. Head: `617cb91c58aeb2f3eeedcf2b4a82c620681174b8`. Parent: `96ea6698d722eec23167c175a2a6c2b440035bbb`.

The original feature and submission fix were rebased, then squashed to one commit. The initial squash on c172076bf preserved the original binary feature diff exactly. Upstream then added workspace unread support and plugin icon fixes. Rebase onto 96ea6698d retained these additions, with git range-diff reporting the feature commit unchanged (=), including the four overlapping files. This verifies preservation of the feature; it does not constitute fresh native platform QA.

On ubuntu-pcs, Node 26.8.1: the final rebase passes `npm run build:server`, `npm run typecheck`, feature-file `npm run lint -- <paths>` and `npm run format:check:files -- <paths>`. The earlier squash also passed the commit hook and `npm run format`. The targeted boundary test was rerun after rebase:

```text

 RUN  v4.1.7 <checkout>/packages/app


 Test Files  1 passed (1)
      Tests  9 passed (9)
   Start at  21:14:53
   Duration  400ms (transform 259ms, setup 26ms, import 308ms, tests 5ms, environment 0ms)

```

The source branch has no media or QA artifact commits. Previous native Linux evidence and this demo are from `d2aa74e9c`; Windows retesting on the new head is pending. GitHub CI, Docker and Nix await maintainer approval.

Demo: 30.00 seconds, 1280x980, H.264, 25 fps, no audio. Actual 1280x900 Electron footage is preserved beneath an 80-pixel caption band. Source intervals: 80-90s; 131-131.5s slowed 20x; 179-189s. Captions identify the recorded revision and editing. Guarded source run had no fixture provider turns. The recording uses a Linux development build with process-local `--no-sandbox`.

The unscoped lint attempt scanned untracked generated QA bundles in tmp and failed. The explicit feature-file lint command passed with zero errors/warnings; the generated-artifact output is retained in the local audit bundle.
