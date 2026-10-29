Authenticated browser clients of a self-hosted workspace currently have no composer dictation. This adds an opt-in local transcription worker: record in the browser, append the result to the current draft, review it, then explicitly send.

The browser path uses secure MediaRecorder capture and the existing session/origin/client-scope gates. Uploads are capped at 10 MiB, decoded audio at 120 seconds, and inference at one request at a time. The operator controls a loopback-only backend URL; redirects are refused. Cancellation releases capture and rejects late results, conversation switches discard pending work, and manual draft edits are preserved. The desktop capability contract still selects the native recognizer when available.

Includes an optional CPU/int8 faster-whisper worker, setup and verification recipes, and English/Brazilian Portuguese UI strings. It stays disabled until the operator configures the local worker.

This complements the desktop dictation work in #1813 and #2375.

### Validation

- 103 targeted tests passed: API limits/concurrency/cancellation, authentication and cookie origin checks, browser draft behavior, native dictation regressions and IME.
- Real Chromium fixture passed after rebasing: synthetic WebAudio recorded as actual WebM, authenticated API transport, cancelled capture without upload, draft review, exactly one explicit send. The transcription backend is a deterministic stand-in.
- 3 optional Python worker tests passed, including actual audio decoding/duration bounds with a synthetic model.
- TypeScript checks, lint, locale check, 320 Electron module syntax checks, production UI build and 102 broker tests passed.
- Detailed local results and full-suite status: [validation report](https://github.com/EchelOneZero/OpenMausBot/blob/docs/browser-dictation-evidence-20261029/review-artifacts/browser-dictation/validation.md).
- Electron Node suite: 1,406 passed, 8 skipped, 2 environment failures. Both failures reproduce on unmodified upstream: a chmod-denial assertion while running as root, and a temporary-directory sharing test on a separate temporary filesystem.

Physical microphone/OS permissions, Safari/mobile behavior and recognition quality still need manual verification.

### UI evidence

All screenshots use an isolated fake-engine workspace and synthetic content.

| Browser before | Text ready for review |
| --- | --- |
| ![Browser without dictation](https://raw.githubusercontent.com/EchelOneZero/OpenMausBot/9a076988ae5ebfa8cc2b225d72b8f5f584816231/review-artifacts/browser-dictation/before.png) | ![Transcript in the draft before Send](https://raw.githubusercontent.com/EchelOneZero/OpenMausBot/9a076988ae5ebfa8cc2b225d72b8f5f584816231/review-artifacts/browser-dictation/review.png) |

<details>
<summary>Recording state</summary>

![Recording with explicit cancellation](https://raw.githubusercontent.com/EchelOneZero/OpenMausBot/9a076988ae5ebfa8cc2b225d72b8f5f584816231/review-artifacts/browser-dictation/recording.png)

</details>

Comparison base: `f71928c15d2043cd8562877d350e189b859767ba`. Head: `EchelOneZero:feat/browser-server-dictation` (`ae28ca4816f279f6dd2b20604f2649f725ff99e1`). Checks above ran after the final rebase.
