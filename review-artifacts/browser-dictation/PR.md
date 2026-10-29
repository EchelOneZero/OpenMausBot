[Open the prefilled upstream pull request](https://github.com/milind-soni/OpenMausBot/compare/main...EchelOneZero:OpenMausBot:feat/browser-server-dictation?quick_pull=1&title=feat%3A%20optional%20browser%20dictation%20through%20a%20local%20transcription%20worker&body=Authenticated%20browser%20clients%20of%20a%20self-hosted%20workspace%20currently%20have%20no%20composer%20dictation.%20This%20adds%20an%20opt-in%20local%20transcription%20worker%3A%20record%20in%20the%20browser%2C%20append%20the%20result%20to%20the%20current%20draft%2C%20review%20it%2C%20then%20explicitly%20send.%0A%0AThe%20browser%20path%20uses%20secure%20MediaRecorder%20capture%20and%20the%20existing%20session%2Forigin%2Fclient-scope%20gates.%20Uploads%20are%20capped%20at%2010%20MiB%2C%20decoded%20audio%20at%20120%20seconds%2C%20and%20inference%20at%20one%20request%20at%20a%20time.%20The%20operator%20controls%20a%20loopback-only%20backend%20URL%3B%20redirects%20are%20refused.%20Cancellation%20releases%20capture%20and%20rejects%20late%20results%2C%20conversation%20switches%20discard%20pending%20work%2C%20and%20manual%20draft%20edits%20are%20preserved.%20The%20desktop%20capability%20contract%20still%20selects%20the%20native%20recognizer%20when%20available.%0A%0AIncludes%20an%20optional%20CPU%2Fint8%20faster-whisper%20worker%2C%20setup%20and%20verification%20recipes%2C%20and%20English%2FBrazilian%20Portuguese%20UI%20strings.%20It%20stays%20disabled%20until%20the%20operator%20configures%20the%20local%20worker.%0A%0AThis%20complements%20the%20desktop%20dictation%20work%20in%20%231813%20and%20%232375.%0A%0A%23%23%23%20Validation%0A%0A-%20103%20targeted%20tests%20passed%3A%20API%20limits%2Fconcurrency%2Fcancellation%2C%20authentication%20and%20cookie%20origin%20checks%2C%20browser%20draft%20behavior%2C%20native%20dictation%20regressions%20and%20IME.%0A-%20Real%20Chromium%20fixture%20passed%20after%20rebasing%3A%20synthetic%20WebAudio%20recorded%20as%20actual%20WebM%2C%20authenticated%20API%20transport%2C%20cancelled%20capture%20without%20upload%2C%20draft%20review%2C%20exactly%20one%20explicit%20send.%20The%20transcription%20backend%20is%20a%20deterministic%20stand-in.%0A-%203%20optional%20Python%20worker%20tests%20passed%2C%20including%20actual%20audio%20decoding%2Fduration%20bounds%20with%20a%20synthetic%20model.%0A-%20TypeScript%20checks%2C%20lint%2C%20locale%20check%2C%20320%20Electron%20module%20syntax%20checks%2C%20production%20UI%20build%20and%20102%20broker%20tests%20passed.%0A-%20Detailed%20local%20results%20and%20full-suite%20status%3A%20%5Bvalidation%20report%5D(https%3A%2F%2Fgithub.com%2FEchelOneZero%2FOpenMausBot%2Fblob%2Fdocs%2Fbrowser-dictation-evidence-20261029%2Freview-artifacts%2Fbrowser-dictation%2Fvalidation.md).%0A-%20Electron%20Node%20suite%3A%201%2C406%20passed%2C%208%20skipped%2C%202%20environment%20failures.%20Both%20failures%20reproduce%20on%20unmodified%20upstream%3A%20a%20chmod-denial%20assertion%20while%20running%20as%20root%2C%20and%20a%20temporary-directory%20sharing%20test%20on%20a%20separate%20temporary%20filesystem.%0A%0APhysical%20microphone%2FOS%20permissions%2C%20Safari%2Fmobile%20behavior%20and%20recognition%20quality%20still%20need%20manual%20verification.%0A%0A%23%23%23%20UI%20evidence%0A%0AAll%20screenshots%20use%20an%20isolated%20fake-engine%20workspace%20and%20synthetic%20content.%0A%0A%7C%20Browser%20before%20%7C%20Text%20ready%20for%20review%20%7C%0A%7C%20---%20%7C%20---%20%7C%0A%7C%20!%5BBrowser%20without%20dictation%5D(https%3A%2F%2Fraw.githubusercontent.com%2FEchelOneZero%2FOpenMausBot%2F9a076988ae5ebfa8cc2b225d72b8f5f584816231%2Freview-artifacts%2Fbrowser-dictation%2Fbefore.png)%20%7C%20!%5BTranscript%20in%20the%20draft%20before%20Send%5D(https%3A%2F%2Fraw.githubusercontent.com%2FEchelOneZero%2FOpenMausBot%2F9a076988ae5ebfa8cc2b225d72b8f5f584816231%2Freview-artifacts%2Fbrowser-dictation%2Freview.png)%20%7C%0A%0A%3Cdetails%3E%0A%3Csummary%3ERecording%20state%3C%2Fsummary%3E%0A%0A!%5BRecording%20with%20explicit%20cancellation%5D(https%3A%2F%2Fraw.githubusercontent.com%2FEchelOneZero%2FOpenMausBot%2F9a076988ae5ebfa8cc2b225d72b8f5f584816231%2Freview-artifacts%2Fbrowser-dictation%2Frecording.png)%0A%0A%3C%2Fdetails%3E%0A%0AComparison%20base%3A%20%60f71928c15d2043cd8562877d350e189b859767ba%60.%20Head%3A%20%60EchelOneZero%3Afeat%2Fbrowser-server-dictation%60%20(%60ae28ca4816f279f6dd2b20604f2649f725ff99e1%60).%20Checks%20above%20ran%20after%20the%20final%20rebase.%0A)

Choose **Create draft pull request** in GitHub. The title, description and screenshot links are already filled in.

---

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
