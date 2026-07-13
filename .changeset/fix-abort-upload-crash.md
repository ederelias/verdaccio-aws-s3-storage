---
"verdaccio-aws-s3-storage": patch
---

Prevent an aborted tarball upload from crashing the registry process.

`writeTarball` builds `uploadPromise = upload.done().catch(... throw)`. The
re-thrown rejection is only awaited inside `onEnd()`, which runs on the
stream's `end` event. When a client aborts mid-upload, `onEnd()` never runs,
so the rejected promise surfaced as a process-level `unhandledRejection` and
took down the whole Verdaccio process. Attach a floating `.catch()` so the
rejection is always consumed — the error is still surfaced to the stream via
`emit('error')`.
