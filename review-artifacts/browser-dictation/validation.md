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
| Packaged server smoke | Passed without node_modules; proxy paths, MCP, CLI, encrypted backup, container/desktop entry and enterprise lookup |
| Electron Node suite | 1,406 passed, 8 skipped, 2 environment failures reproduced on upstream |
| Broad Vitest run (`--bail=1`) | 3,670 passed, 4 skipped, 1 timeout; 32 files passed; stopped early, entire 2,021-file suite not completed |

The broad run stopped at `server/workspace-backup.test.ts`, "rejects authenticated credential metadata, saved auth paths, and connection-bearing config", after its existing 20,000 ms timeout. The same case timed out on the exact upstream comparison base `f71928c15d2043cd8562877d350e189b859767ba`, including a standalone run without the feature test running alongside it. The feature branch also reproduced it in isolation. The backup implementation and this test are unchanged by the PR. No timeout or assertion was relaxed.

The two Electron failures are `cloud-sync-scan.node-test.mjs` (chmod cannot deny root) and `use-my-mac.node-test.mjs` (temporary-directory sharing against a separate temporary filesystem). Both reproduce in the upstream checkout, and the feature does not alter Electron sources.

The optional CPU worker was also exercised with generated English speech and a pre-staged offline model. It returned a complete sentence with one word substitution ("review" → "renew"). This single phrase is not an accuracy benchmark. Text remains in the draft for human review.

Manual microphone follow-up: the user confirmed microphone capture, transcription into the draft and no automatic sending in their current browser after applying the same feature patch to a self-hosted v0.1.132 installation. Browser and OS versions were not recorded. This confirms that one real client works; other platforms, Safari/mobile capture and recognition quality still require manual checks.

The v0.1.132 deployment candidate separately passed the 103 targeted tests, the Chromium fixture, three worker tests, type checking, lint, locale validation, production builds and packaged-server smoke. A disposable copy of the final package also transcribed generated Portuguese speech through the offline model without creating a chat message. Production was checked using read-only health/status and asset requests; no synthetic chat messages were sent there.

Upstream PR: [#3875](https://github.com/milind-soni/OpenMausBot/pull/3875), open and ready for review (`draft: false`), head `ae28ca4816f279f6dd2b20604f2649f725ff99e1`, reported mergeable by GitHub. The user created it through GitHub after the integration returned HTTP 403 for PR creation.

Hosted checks currently pending maintainer action: GitHub CI and Docker workflows report `action_required`, with no jobs started. Vercel's failed preview status links to team authorization. No hosted test pass is claimed.

Local verification is concluded for this contribution. The feature-specific checks passed; the complete repository suite has not been declared green. Hosted CI and remaining platform checks are pending; the user's current-browser microphone check has passed.

The author marked the PR ready for review. CodeRabbit began processing the 22 changed files. GitHub CI and Docker runs still require maintainer action. The integration could not update the upstream PR description (HTTP 403), so the manual validation is recorded in this linked report.
