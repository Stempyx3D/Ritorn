# Ritorn — Decision register

Aligned on 2026-11-18. This register separates approved product behavior from implementation proposals; it does not certify a working stack or approve the entire proposed MVP.

## Status and authority

| Status | Meaning |
| --- | --- |
| Approved | Explicitly confirmed product direction or invariant. Implementation details are not implicitly approved. |
| Proposed | Candidate design or scope awaiting review and approval. |
| Needs PoC | Candidate requiring prototype evidence before a selection can be approved. Passing a PoC alone does not grant approval. |
| Deferred | Outside the currently proposed MVP; reconsider only through a separate scope decision. |
| Superseded | Historical decision replaced by an identified newer decision. |

Sources: **U** = product-owner task “Ritorn — Documentation Alignment & Decision Register”, supplied 2026-11-18; **P** = [PRD](Music_Player_PRD_v1.0.md); **A** = [Architecture](ARCHITECTURE_v1.0.md); **D** = [Data Model](DATA_MODEL_v1.0.md); **S** = [Sync Spec](SYNC_SPEC_v1.0.md). Section numbers below identify the supporting detail. U explicitly confirms the product decisions below and requests the statistics constraints in P / STAT-01; it does not approve additional technical choices.

Read this register alongside the specifications. P0/P1 express proposed priority, not decision approval. Legacy “Рекомендовано”, “Пропозиція”, and “ПОПЕРЕДНЬО” mean Proposed; “PoC” means Needs PoC. “Погоджено” / “ЗАТВЕРДЖЕНО” apply only to explicitly approved behavior. Report conflicts; do not silently infer approval, remove requirements, or invent missing product policy.

## Approved product decisions

| ID | Decision | Status | Rationale and consequences | Source | Reconsideration |
| --- | --- | --- | --- | --- | --- |
| NAME-01 | Current name is Ritorn; previous name was Tunibo. “Music Player” is the legacy specification title. | Approved | Use Ritorn in current-facing text; retain versioned filenames to preserve references. | U | Explicit product-owner rename. |
| LOCAL-01 | Offline-first local music playback and local playlists work without sign-in. | Approved | Network or Auth failures must not prevent local use. | U; P §§1–2, AUTH-01 | Explicit product decision; no implicit backend dependency. |
| PLATFORM-01 | MVP targets Windows, Android and iOS; macOS is later. | Approved | Validate the three target platforms early; no macOS MVP commitment. | U; P §4.1 | Separate platform/scope approval. |
| MEDIA-01 | Audio files remain local by default; no automatic upload or destructive handling. | Approved | Import, matching and metadata sync do not rename, rewrite or delete originals. | U; P §2, LIB-01 | Separate explicit user action and approved feature for audio changes. |
| SYNC-SCOPE-01 | Metadata and playlists synchronize only between the user's own devices. | Approved | Account-private catalog; no cross-user collaboration implied. | U; P §1 | Separate product/privacy approval. |
| SYNC-01 | Removing a track from its last synchronized playlist does not remove it from the synchronized track catalog. | Approved | Catalog membership is independent of playlist membership. | U; P SYNC-01; S §2 | Explicit revision preserving user intent and recovery. |
| SYNC-02 | Disabling track sync requires confirmation; with synchronized playlist references, show dependencies and offer review, atomic remove-all-and-disable, or cancel. | Approved | Recheck current server dependencies; changed dependencies require renewed confirmation. No partial removal; local audio remains. Without dependencies, use ordinary confirmation. | U; P SYNC-02; S §9 | Explicit product review; never weaken atomicity implicitly. |
| TRACK-03 | Identical filenames are insufficient to identify identical recordings. | Approved | No automatic matching by filename alone. | U; P TRACK-03 | New verified identity design must preserve this invariant. |
| TRACK-04 | Different names or encodings may map to one logical recording only after reliable verification or explicit user confirmation. | Approved | Metadata suggests candidates; different encodings need evidence beyond byte equality. | U; P TRACK-04 | Review verification evidence and matching UX. |
| SYNC-IND-01 | The sync indicator means metadata catalog membership, not audio-file availability. | Approved | Show local availability separately. | U; P SYNC-05 | Explicit UX/product review. |
| STAT-01 | Incorporate early collection of local listening statistics into playback architecture. | Approved | Offline, local, nonblocking history with stable recording/session identities and recovery; missing files do not automatically erase history. Detailed metrics remain open; no initial analytics dashboard required. | U; P STAT-01 | Product approval for semantics, retention, privacy and export before final implementation contract. |
| ISOLATION-01 | Preserve account isolation and prevent automatic mixing of private data. | Approved | Scope changes cannot expose another account's data; storage mechanism remains a candidate. | U; P AUTH-03; A §10 | Security/product review; implementation cannot relax isolation. |

## Technical candidates

| ID | Candidate | Status | Rationale / consequences | Source | Validation / reconsideration |
| --- | --- | --- | --- | --- | --- |
| ARCH-01 | Kotlin Multiplatform / Compose Multiplatform | Needs PoC | Share logic/UI with native boundaries; not production-validated. | A §§2, 11 | Builds on all targets, Windows RAM/startup, platform integration. |
| ARCH-02 | Room KMP / SQLite | Needs PoC | Local transactional persistence; driver, migrations and search need evidence. | A §2; D §§3–4 | Large-library performance, migrations and crash recovery on all targets. |
| ARCH-03 | Platform audio adapters: Windows libmpv vs LibVLC, Android Media3, iOS AVFoundation | Needs PoC | Isolate playback; backend choice is not final. | A §§2, 5 | Compare Windows packaging/licenses/resources; test formats, seek, interruptions and background audio on real mobile devices. |
| ARCH-04 | Supabase Auth / PostgreSQL and a custom sync protocol | Needs PoC | Candidate account/metadata backend; playback stays independent. | A §§2, 10; S §§3, 14 | Auth lifecycle, authorization, integration and cost validation. |
| ARCH-05 | Transactional per-account journal and idempotent commands | Needs PoC | Candidate delivery mechanism; preserve offline changes and SYNC-02 atomicity. | A §8; D §5.2; S §§6–7 | Concurrent commits, lost ACK, retry, rollback, cursor and retention tests. |
| ARCH-06 | Artwork optimization, local cache and private cloud storage | Needs PoC | Compact optional cloud copies; no audio uploads or playback dependency. | P ART-01/02; A §9 | Size/quality, quotas, lifecycle, privacy and resource measurements. |
| ORDER-01 | Fractional playlist ordering | Needs PoC | Candidate stable ordering with server anchors and bounded rebalance. | D DM-DEC-02; S SS-DEC-03 | Cross-database sorting, collisions, concurrent moves and large rebalance tests. |
| SNAPSHOT-01 | Immutable snapshot plus confirmed replica / pending-intent reconciliation | Needs PoC | Recover stale cursors without losing outbox; staging has storage/lock costs. | S §§11–12, SS-DEC-02/04 | Concurrent writes, interrupted chunks, lost ACK and rebase tests. |
| ISOLATION-TECH-01 | One SQLite with scope_id versus separate databases | Needs PoC | Isolation is approved; physical layout is not. | D DM-DEC-01; A §7.1 | Account-switch, stale response, guest access and security review. |
| STAT-TECH-01 | Playback Statistics Collector with local sessions; optional events only if needed | Proposed | Explicit PlayerController event boundary, bounded asynchronous persistence and recoverable local history. | U; A §5.1; D §4.12 | Approve metric/privacy policy; prototype crash, queue-pressure and clock behavior before final schema/frequency. |

## Deferred and superseded

| ID | Decision | Status | Rationale / consequences | Source | Reconsideration |
| --- | --- | --- | --- | --- | --- |
| DEFER-01 | Automatic audio-file transfer | Deferred | Current sync transfers metadata, not audio. Future LAN/remote transfer is separate. | U; P §4.3 | Explicit scope and user-consent design. |
| DEFER-02 | Cross-user collaborative playlists | Deferred | Current catalog is account-private. | U; P §4.3 | Explicit sharing/authorization product decision. |
| DEFER-03 | Cross-device playback position, queue and listening statistics sync | Deferred | Local history collection is independent of cloud synchronization. No stats in catalog, journal or outbox. | U; P §4.3; S §1.3 | Separate approval and protocol/privacy design. |
| DEFER-04 | Advanced DSP/EQ, crossfade, ReplayGain, bit-perfect | Deferred | Keep current playback scope focused; no backend promise for these features. | U; P §4.3 | Scope approval and audio PoC. |
| DEFER-05 | Direct modification of audio tags | Deferred | Current metadata edits do not rewrite original files. | U; P §4.3 | Explicit feature approval and per-action user authorization. |
| DEFER-06 | macOS release | Deferred | Windows/Android/iOS are the MVP targets. | U; PLATFORM-01 | Platform scope approval. |
| NAME-00 | Tunibo as current product name | Superseded | Replaced by NAME-01; retain only as historical context. | U | New explicit naming decision. |

## Open decisions and alignment notes

- **Statistics product policy:** definition of play, skip, completed listen and listening duration; event granularity; retention/deletion, consent/settings, export format, acceptable crash-loss window, and guest/account history visibility or transfer. Do not invent thresholds, storage intervals or retention periods. STAT-01 approves early collection, not these details.
- **Statistics implementation:** session boundaries, event log necessity, clock handling, durable checkpoints, queue overflow/backpressure and recovery representation remain Proposed. History must survive missing files; design deletion/merge references without cascading history loss.
- **Existing scope questions remain open:** guest → account playlist defaults, favorite sync, trash/backup retention, artwork limits, minimum OS versions, additional formats/gapless, release budget, localization and distribution. Existing P0/P1 labels are preserved; full release scope still needs approval.
- **Model/protocol alignment pending:** S §4 refines D's generic versions/outbox states into separate revisions, confirmed/pending projections and snapshot staging. D §5.3's snapshot RPC is conceptual; S §11 proposes create/read snapshot calls. A uses the conceptual name `account_change_counter`, while D/S use `account_sync_state`. These are documented levels of proposal, not competing approved schemas; reconcile concrete names/contracts before migrations.
- **Approval labels corrected:** S §2 and D §1 combined approved invariants with proposed enforcement/restore details. Their notes now distinguish those levels without removing algorithms or requirements. A's duplicate register points here for status.
- **Historical sources:** existing technical links and dated service claims are retained as research leads, not newly verified platform guarantees. Recheck current official documentation and pricing during PoC/release planning.

No additional product decisions are approved by this alignment. Changes to the open policies require product-owner approval; technical candidates require review and, where marked, PoC evidence.
