# Rebase validation

PR #4544 remains draft. Head: `59915756733080e1932d8367e8ef2c5610512aa0`. Parent: `c172076bffa84e4ccf3beffa0993bac6579801fd`.

The original feature and submission fix were rebased, then squashed to one commit. The binary feature diff against the new upstream is byte-identical to the previous diff against `da8c1b5c94e752b01d451645e5fa52aba2c1b2f0`. Upstream changed no feature paths. This verifies preservation of the feature; it does not constitute fresh native platform QA.

On ubuntu-pcs, Node 26.8.1: `npm run build:server`, `npm run format`, and the commit hook's full typecheck, targeted lint and formatting passed. The targeted boundary test was rerun after rebase:

```text

 RUN  v4.1.7 <checkout>/packages/app


 Test Files  1 passed (1)
      Tests  9 passed (9)
   Start at  21:06:15
   Duration  416ms (transform 268ms, setup 23ms, import 323ms, tests 6ms, environment 0ms)

```

The source branch has no media or QA artifact commits. Previous native Linux evidence and this demo are from `d2aa74e9c`; Windows retesting on the new head is pending. GitHub CI, Docker and Nix await maintainer approval.

Demo: 30.00 seconds, 1280x980, H.264, 25 fps, no audio. Actual 1280x900 Electron footage is preserved beneath an 80-pixel caption band. Source intervals: 80-90s; 131-131.5s slowed 20x; 179-189s. Captions identify the recorded revision and editing. Guarded source run had no fixture provider turns. The recording uses a Linux development build with process-local `--no-sandbox`.
