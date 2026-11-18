# Ritorn

Ritorn is an offline-first, cross-platform music player for locally stored audio files, with optional synchronization of playlists and metadata between a user's devices.

> Status: pre-implementation / proof-of-concept planning. The underlying specifications date from November 13-14, 2026 and use the earlier working name "Music Player". The versioned documents retain their historical titles and technical detail, with targeted alignment recorded in [DECISIONS.md](docs/DECISIONS.md). Product invariants are approved; technical choices still require review and PoC.

## Target platforms

- Windows, Android and iOS: MVP targets.
- macOS: planned for a later version.

## Proposed technology (subject to PoC)

- Kotlin and Compose Multiplatform.
- Room KMP / SQLite for the local library and offline state.
- Platform-specific audio engines (evaluate libmpv for Windows, Media3 for Android, AVFoundation for iOS).
- Supabase Auth + PostgreSQL for accounts and synchronized metadata.

## Core product principles

- Playback and local playlists work offline and without an account.
- Audio files stay on the user's device; do not automatically upload or modify them.
- A synced track is a logical entity, not an audio file.
- Filenames alone are never enough to match recordings.
- All synchronized playlist operations must honor the approved SYNC-01/02 and TRACK-03/04 rules.

## Specifications

- [Decision register and approval status](docs/DECISIONS.md)
- [Product requirements](docs/Music_Player_PRD_v1.0.md)
- [Architecture](docs/ARCHITECTURE_v1.0.md)
- [Data model](docs/DATA_MODEL_v1.0.md)
- [Synchronization protocol](docs/SYNC_SPEC_v1.0.md)

These documents contain a mixture of approved product invariants and proposed technical approaches. Read their status notes and open questions.

## Next steps

1. Resolve the open product policies in the decision register and validate technical candidates through PoC.
2. Generate a current Kotlin Multiplatform project with the official tooling rather than hand-writing Gradle scaffolding.
3. Build a Windows audio-engine proof of concept, benchmark it, then test Android and iOS early; incorporate local listening-history collection at the playback stage.
4. Introduce database, sync and end-to-end test prototypes before main development.

## Repository workflow

Use VS Code + Codex, read [AGENTS.md](AGENTS.md), work in short branches and review changes before merging. Do not commit audio libraries, local secrets or machine-specific build outputs.
