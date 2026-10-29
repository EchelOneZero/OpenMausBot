# Browser dictation validation

Feature head: `ae28ca4816f279f6dd2b20604f2649f725ff99e1`
Comparison base: `f71928c15d2043cd8562877d350e189b859767ba`
Environment: Linux, Node 24.22.1, pnpm 10.33.0.
All server/browser mutations use disposable fake-engine workspaces.

| Check | Result |
| --- | --- |
| Targeted API/auth/composer/native-dictation/IME tests | 103 passed across 7 files |
| Real Chromium browser fixture after rebase | 1 passed; actual MediaRecorder WebM through the workspace API; synthetic input and deterministic transcription stand-in |
| Optional Python worker tests | 3 passed; actual decoding, duration bounds, inference serialization and sanitized errors |
| TypeScript checks | Passed |
| Production UI build | `pnpm exec vite build` passed |
| Lint | Passed |
| Locale catalogs | Passed; 16 languages |
| Electron syntax | 320 modules passed |
| Broker tests | 102 passed |
| Electron Node suite | 1,406 passed, 8 skipped, 2 environment failures reproduced on upstream |
| Full Vitest suite | Running; final outcome pending |

The two Electron failures are `cloud-sync-scan.node-test.mjs` (chmod cannot deny root) and `use-my-mac.node-test.mjs` (temporary-directory sharing against a separate temporary filesystem). Both reproduce in the upstream checkout, and the feature does not alter Electron sources.

The optional CPU worker was also exercised with generated English speech and a pre-staged offline model. It returned a complete sentence with one word substitution ("review" → "renew"). This single phrase is not an accuracy benchmark. Text remains in the draft for human review.

Physical microphone and OS permission prompts, Safari/mobile capture and real-world recognition quality require manual checks.

Publication through the connected GitHub integration returned HTTP 403, "Resource not accessible by integration", for creating a PR in the upstream repository. The feature branch and evidence are available in the fork.
