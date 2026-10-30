# Browser dictation PR #3875

[Open the existing upstream pull request](https://github.com/milind-soni/OpenMausBot/pull/3875).

The corrected code is already on the existing PR. The GitHub integration returned HTTP 403 for editing its upstream description or replying to review threads. The text below is the prepared current description, including the repository checklist; the linked validation report carries the current results independently.

---

## What changed

Authenticated browser clients can optionally record microphone audio through a local transcription worker. Transcripts are appended to the draft for review and require an explicit Send. Cancellation works from the microphone button or document body as well as the textarea, including during microphone permission and transcription, while preserving IME composition.

The browser path uses secure MediaRecorder capture and existing session/origin/client-scope gates. Uploads are limited to 10 MiB and decoded audio to 120 seconds. The operator configures an IPv4 loopback backend; redirects are refused. The example worker rejects browser-origin, rebinding and simple-form requests before occupying inference. Native desktop dictation keeps its capability-selected recognizer.

Includes an optional offline CPU/int8 faster-whisper worker, setup/verification documentation and English/Brazilian Portuguese UI strings. The browser feature stays disabled until the operator configures its worker.

## Why

Remote browser users of a self-hosted workspace currently lack composer dictation. This provides an optional local path and complements the desktop work in #1813 and #2375.

## How it was verified

- 109 targeted tests and 44 additional composer/API regression tests passed.
- Chromium fixture passed with actual MediaRecorder WebM, Escape outside the textarea, cancellation, draft review and exactly one explicit Send.
- Six Python worker tests passed, including browser-origin/Host/Content-Type rejection before inference.
- Type checking, lint, locale catalogs, 322 Electron module syntax checks, production UI build and packaged-server smoke passed.
- The compiled API transcribed synthetic Portuguese speech through an actual offline worker in a disposable home without sending a chat message.
- The user previously confirmed a physical microphone on the original v0.1.132 backport. Other platforms and recognition quality remain manual checks.
- [Detailed results and full-suite limitations](https://github.com/EchelOneZero/OpenMausBot/blob/docs/browser-dictation-evidence-20261029/review-artifacts/browser-dictation/validation.md). A full repository-suite pass and hosted CI pass are not claimed.

Comparison base: `d124292a2aafcbed61f7ac58a0b975231df92da2`. Head: `f0933b2e96860b5966f3e09eb4bd94cb61f96426`. All listed current automated checks ran against tree `acfb7752795250f4281e6d646d3dcfb2e7bb15b1`.

## Screenshots (UI changes)

All screenshots use an isolated fake-engine workspace and synthetic content.

| Browser before | Text ready for review |
| --- | --- |
| ![Browser without dictation](https://raw.githubusercontent.com/EchelOneZero/OpenMausBot/9a076988ae5ebfa8cc2b225d72b8f5f584816231/review-artifacts/browser-dictation/before.png) | ![Transcript in the draft before Send](https://raw.githubusercontent.com/EchelOneZero/OpenMausBot/9a076988ae5ebfa8cc2b225d72b8f5f584816231/review-artifacts/browser-dictation/review.png) |

<details>
<summary>Recording state</summary>

![Recording with explicit cancellation](https://raw.githubusercontent.com/EchelOneZero/OpenMausBot/9a076988ae5ebfa8cc2b225d72b8f5f584816231/review-artifacts/browser-dictation/recording.png)

</details>


## Checklist

- [ ] `pnpm typecheck` and `pnpm test` pass locally — type checking passed; a complete `pnpm test` pass is not claimed, as detailed in the linked report.
- [x] Server behavior changes come with regression tests.
- [x] No `dist-server/` or other generated build output edits in the PR.
- [x] Native desktop capability behavior retained; no `shell: true` or cmd.exe string-building.
- [x] No secrets or recordings added to logs, responses, events or argv.
