# Browser dictation validation

## Latest follow-up: safe worker diagnostics (2026-10-30)

The feature branch now includes commit `15e52b7f296bc7b4b266cf1a144c1a6a187f2bdd`, based on `f0933b2e96860b5966f3e09eb4bd94cb61f96426`. This addresses the CodeRabbit suggestion to emit only the unexpected exception class on stderr while retaining the generic HTTP 503 response. Exception messages, private model paths and transcripts are excluded.

- All 6 isolated Python HTTP worker tests passed on this follow-up. The added diagnostic assertion failed before the fix and passed afterward. The existing concurrency, hostile-request, decoding and response-redaction checks remain in the same run.
- `git diff --check` passed.
- Only the Python worker, its HTTP test and the verification recipe changed. The full Node/browser/build suite was not rerun for this Python-only follow-up; results below belong to the earlier heads they identify.
- No production deployment was performed.

Review: [safe diagnostic suggestion](https://github.com/milind-soni/OpenMausBot/pull/3875#pullrequestreview-5711439681).

## Earlier validation record


Current feature head: `f0933b2e96860b5966f3e09eb4bd94cb61f96426`
Comparison base: `d124292a2aafcbed61f7ac58a0b975231df92da2`
Verified Git tree: `acfb7752795250f4281e6d646d3dcfb2e7bb15b1`
Environment: Linux, Node 24.22.1, pnpm 10.33.0; current upstream package version 0.1.134.
All mutating checks use disposable fake-engine workspaces and synthetic audio.

## Review findings addressed

- [Worker request boundary](https://github.com/milind-soni/OpenMausBot/pull/3875#discussion_r4419280394): the example worker requires its exact IPv4 loopback Host, rejects Origin and browser-only fetch metadata, and accepts only audio Content-Types before acquiring the inference slot or decoding. Tests cover empty/null Origin, rebinding and duplicate Host, simple browser uploads, and refusal while an authorized inference is already running. Backend Node fetch remains compatible with its automatic Sec-Fetch-Mode: cors header. Local processes remain inside the operator's documented trust boundary.
- [Escape outside the textarea](https://github.com/milind-soni/OpenMausBot/pull/3875#discussion_r4419280438): a document capture listener is installed only while browser dictation is pending. It respects IME composition/keyCode 229, cancels from the microphone button or body, preserves quoted replies, discards late results/grants, and is removed on completion/unmount. Completed capture releases its stream reference.

The added regressions first failed against the previous implementation: six composer cases failed and the worker checks demonstrated unauthorized requests reaching inference. They pass after the corrections.

## Checks on this revision

| Check | Result |
| --- | --- |
| Targeted API/auth/composer/native-dictation/IME/size/control tests | 109 passed across 7 files |
| Additional composer layouts, menus, queues and background API checks | 44 passed across 5 files |
| Real Chromium browser fixture | 1 passed; actual MediaRecorder WebM; Escape from the microphone button during recording and from the body during transcription; cancelled results discarded; exactly one explicit Send |
| Optional Python worker | 6 tests passed; real audio decoding and hostile request rejection before inference |
| TypeScript checks | Passed, including final check after the last source edit |
| Lint | Passed |
| Locale catalogs | Passed; 16 languages, 6,929 English strings |
| Production UI build | Passed |
| Packaged server smoke | Passed without node_modules; proxy paths, MCP, CLI, encrypted backup, container/desktop entry and enterprise lookup |
| Electron syntax | 322 modules passed |
| Final packaged API to actual offline worker | Passed in a disposable home with generated Portuguese speech; compiled UI served; no chat message created |

The real-model check proves integration, not recognition accuracy. Its synthetic phrase contained recognition errors, so no quality benchmark is claimed.

Conflicts in the composer imports and Brazilian Portuguese catalog were resolved while preserving current upstream changes. GitHub reported the updated PR mergeable after publication. No generated build output is included in the source PR.

## Manual check and production scope

The user previously confirmed physical microphone capture, text insertion into the draft and no automatic sending in their current browser, using the feature backported to a self-hosted v0.1.132 installation. Browser and OS versions were not recorded. That manual check predates this correction commit. Other platforms, Safari/mobile behavior and recognition quality still need manual checks.

This revision updates the contribution branch. It does not deploy the upstream v0.1.134 merge or restart the production application.

## Whole-suite and hosted CI limits

The entire repository suite has not been declared green for this revision. The earlier broad Vitest and Electron environment audit remains available in the [previous validation report](https://github.com/EchelOneZero/OpenMausBot/blob/7da665bdc223be6c7031dfd2ceced6ec80b61351/review-artifacts/browser-dictation/validation.md). Those results concern the previous feature head and comparison base, not this new merge.

That earlier run stopped after 3,670 passes, four skips and the existing workspace-backup timeout. The same timeout was reproduced on the exact unmodified comparison base. Two Electron failures also reproduced upstream in the root/temporary-filesystem environment. No unrelated assertion or timeout was relaxed. The complete broad suite and broker suite were not rerun for this correction; current checks are listed above.

GitHub-hosted CI remains subject to maintainer authorization for this external contribution. No hosted pass or maintainer approval is claimed. The integration cannot edit the upstream PR description or reply to its review threads (HTTP 403); this report is already linked from the PR body and records the fixes without requiring that permission.

Upstream PR: [#3875](https://github.com/milind-soni/OpenMausBot/pull/3875), open and ready for review.
