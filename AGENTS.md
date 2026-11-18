# Tunibo — Repository Agent Instructions

## Scope and status
- This is an offline-first local music player. MVP platforms: Windows, Android, iOS. macOS is deferred.
- The project is in design/PoC stage. Do not assume an app, database schema, Gradle structure or working tests already exist.
- Read relevant files in `docs/` before modifying implementation or data contracts.
- Distinguish **approved product invariants** from **proposed technical designs**; do not silently treat proposals as final approvals.
- The original specification files in `docs/` retain the earlier working name "Music Player". Do not silently rewrite them or claim that they already reflect all later decisions.

## Product invariants
- Audio playback and local playlists must not depend on a network or sign-in.
- Never delete, rename, rewrite or upload user audio files without a separate explicit, approved user action.
- Synced-track catalog membership is separate from playlist membership (SYNC-01).
- Disabling sync for a track used in synced playlists requires the approved dependent-playlist workflow and atomic server operation (SYNC-02).
- Same filenames do not prove same recordings (TRACK-03). Different filenames or encodings may link only when reliably verified or confirmed (TRACK-04).
- A synced catalog icon indicates metadata membership; it does not imply the audio bytes exist on another device.
- Preserve account isolation, offline changes, idempotency and non-destructive failure recovery.

## Engineering workflow
- Keep reusable logic separate from platform-specific audio and filesystem implementations.
- Prototype uncertain architecture first; avoid premature complex module splits.
- Before changes, identify the relevant specification and acceptance criteria.
- When requirements conflict or are unclear, report the conflict and suggest options rather than inventing a product decision.
- Add focused automated tests for changed behavior, especially sync conflict/retry/rollback and file identity.
- Run available relevant checks; report exact commands, passes, failures and untested areas.
- Update documentation when an approved architecture or behavior changes.
- Avoid speculative dependencies; justify new native libraries and versions.

## Safety and Git
- Never commit credentials, access tokens, signing keys, local `.env` files, or user music.
- Never run destructive database migrations, delete production data, change access policies in production, push, publish or deploy without explicit authorization.
- Prefer small, reviewable commits; do not overwrite unrelated user changes.
