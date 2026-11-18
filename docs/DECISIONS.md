# Ritorn — Decision register

Aligned on 2026-11-18; extended with explicit product-owner decisions confirmed in the Ritorn planning conversation on 2026-11-18. This register separates approved product behavior from implementation proposals; it does not certify a working stack or approve the entire proposed MVP.

## Status and authority

| Status | Meaning |
| --- | --- |
| Approved | Explicitly confirmed product direction or invariant. Implementation details are not implicitly approved. |
| Proposed | Candidate design or scope awaiting review and approval. |
| Needs PoC | Candidate requiring prototype evidence before a selection can be approved. Passing a PoC alone does not grant approval. |
| Deferred | Outside the currently proposed MVP; reconsider only through a separate scope decision. |
| Superseded | Historical decision replaced by an identified newer decision. |

Sources: **U** = product-owner task “Ritorn — Documentation Alignment & Decision Register”, supplied 2026-11-18; **P** = [PRD](Music_Player_PRD_v1.0.md); **A** = [Architecture](ARCHITECTURE_v1.0.md); **D** = [Data Model](DATA_MODEL_v1.0.md); **S** = [Sync Spec](SYNC_SPEC_v1.0.md). Section numbers below identify the supporting detail. U confirms the initial product decisions and requests the statistics constraints in P / STAT-01. **U2** = subsequent explicit product-owner confirmations in the Ritorn planning conversation on 2026-11-18, covering product scope, local listening statistics and deferrals. Neither U nor U2 approves implementation candidates or the entire MVP.

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
| STAT-01 | Incorporate early collection of local listening statistics into playback architecture. | Approved | Offline, local, nonblocking history with stable recording/session identities and recovery; missing files do not automatically erase history. Some behavioral rules are approved in STAT-SKIP-01, STAT-TIME-01 and STAT-PRIV-01; numerical thresholds and session boundaries remain open. No initial analytics dashboard required. | U; P STAT-01 | Product approval for semantics, retention, privacy and export before final implementation contract. |
| ISOLATION-01 | Preserve account isolation and prevent automatic mixing of private data. | Approved | Scope changes cannot expose another account's data; storage mechanism remains a candidate. | U; P AUTH-03; A §10 | Security/product review; implementation cannot relax isolation. |
| SCOPE-EQ-01 | Basic equalizer is conditional P1 for MVP if platform PoCs confirm reliability without material release delay. | Approved | Assess EQ on Windows libmpv/LibVLC and Android/iOS. If substantial integration problems arise, move it after MVP. This does not approve advanced DSP. | U2; P §4.2; A §5 | Revisit after platform PoCs and release assessment. |
| SCOPE-TAGS-01 | Local edits to displayed metadata are P1 in MVP; writing tags into original files is after MVP. | Approved | Database-only changes must not rewrite media files. | U2; P LIB-05 | Separate file-writing requirements and authorization. |
| SHARING-01 | Read-only subscriptions to others' playlists are after MVP; collaborative editing is later. | Approved | Neither feature changes the account-private sync protocol in MVP. | U2; P §4.3 | Separate sharing, privacy and authorization design. |
| STAT-SKIP-01 | A manual Next before the end creates a pending skip; returning to actual playback of that recording during the same listening session cancels it. | Approved | At listening-session end, unresolved skips count unless near-complete rules exempt them. A pause alone is not session end. Repeated Next and other navigation need clarification. | U2; P STAT | Define session boundaries and edge cases before final metric contract. |
| STAT-TIME-01 | Listening duration is actual active playback time, not seek distance or wall-clock elapsed time; replayed segments add time again. | Approved | Pauses and buffering do not add time. Repeat One after natural completion starts a new track-playback session. | U2; P STAT | Technical clock, rate and checkpoint behavior need PoC. |
| STAT-PLAY-01 | A near-complete actual listen should not count as a skip and may qualify for Play Count. | Approved | 90% verified content coverage OR a 10-second remainder with sufficient real listening are PoC candidates, not final constants. Seeking near the end or looping a tiny segment is insufficient. Natural completion is separate. | U2; P STAT | Approve thresholds, short-track treatment and coverage calculation after PoC. |
| STAT-SILENCE-01 | Statistics may account for confidently detected long trailing silence. | Approved | Optional enhancement, not required for MVP; never trim or rewrite audio. Protect quiet outros and hidden audio; fall back to full file duration when uncertain. | U2; P STAT | Validate accuracy/performance before implementation. |
| STAT-PRIV-01 | Local listening history stays until explicitly cleared while local app data exists; recording new history can be disabled separately. | Approved | Turning off collection does not erase existing history. Audio files are unaffected; statistics are not cloud-synced. | U2; P STAT | Specify settings and deletion UX. |
| STAT-EXPORT-01 | Dedicated export of detailed listening statistics is after MVP. | Approved | Keep MVP playlist/metadata export and cloud-data obligations unchanged. | U2; P STAT; P BACKUP-02 | Revisit format and release scope later. |
| STAT-GUEST-01 | On sign-in, explicitly offer to transfer guest listening history or keep it separate. | Approved | No silent mixing across account scopes; mechanics remain Proposed. | U2; P STAT | Verify isolation before implementation. |
| AUDIO-FORMATS-01 | Target MP3, FLAC and AAC/M4A in MVP; WAV PCM inclusion depends on audio PoC. | Approved | Decoder, container/profile and OS compatibility remain to be measured; no promise of all AAC or WAV codecs. | U2; P AUDIO-01 | Set exact guaranteed format matrix after platform PoCs. |
| SHUFFLE-01 | Use uniform playlist-entry shuffle in MVP, not artist/genre-aware smart shuffle. | Approved | Use Fisher–Yates or an equivalent unbiased permutation with a suitable PRNG. Every playlist_item_id participates once per shuffle cycle, including intentionally duplicated recordings as separate entries. Preserve the active order and playback history for Previous/restart; do not independently draw a new random next track. | U2; P AUDIO-07 | Validate deterministic queue/history tests and playlist mutation behavior; future smart shuffle requires separate approval. |

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
| STAT-PLAY-PARAM-01 | Qualified-play and near-completion numeric thresholds and listening-session boundaries | Needs PoC | Compare 90% verified coverage and 10-second remaining candidate criteria; test seeks, repeats, short tracks and pending skips. | U2; P STAT | Explicit owner approval after test evidence. |
| STAT-RECOVERY-01 | Crash-loss tolerance, durable pending skips and checkpoint policy | Needs PoC | Recovery must not invent listening time/completion or double count; no loss window is approved yet. | U2; A §5.1; D §4.12 | Fault-injection and measurements. |
| AUDIO-EQ-POC-01 | Basic equalizer feasibility on Windows and mobile backends | Needs PoC | Check audio quality, DSP availability, stability and performance without finalizing an engine. | U2; A §5 | Compare working prototypes and licenses. |
| LICENSE-01 | Ritorn source code and distribution license not chosen | Proposed | Study license obligations of all packaged native engines, codecs and dependencies; a public repository does not automatically grant an open-source license. | U2 | Product-owner decision before public distribution. |
| WINDOWS-REQ-01 | Minimum Windows versions and reference hardware | Needs PoC | Set support and performance requirements after testing known Windows configurations. | U2; P §12 | Compatibility and baseline measurements. |

## Deferred and superseded

| ID | Decision | Status | Rationale / consequences | Source | Reconsideration |
| --- | --- | --- | --- | --- | --- |
| DEFER-01 | Automatic audio-file transfer | Deferred | Current sync transfers metadata, not audio. Future LAN/remote transfer is separate. | U; P §4.3 | Explicit scope and user-consent design. |
| DEFER-02 | Cross-user collaborative playlists | Deferred | Current catalog is account-private. | U; P §4.3 | Explicit sharing/authorization product decision. |
| DEFER-03 | Cross-device playback position, queue and listening statistics sync | Deferred | Local history collection is independent of cloud synchronization. No stats in catalog, journal or outbox. | U; P §4.3; S §1.3 | Separate approval and protocol/privacy design. |
| DEFER-04 | Advanced DSP, crossfade, ReplayGain and bit-perfect | Deferred | Basic EQ is now conditional MVP P1 under SCOPE-EQ-01; other effects remain after MVP. | U; U2; P §4.3 | Separate DSP scope approval. |
| DEFER-05 | Direct modification of audio tags | Deferred | Planned after MVP; local displayed-metadata edits remain MVP P1 and do not rewrite originals. | U; U2; P §4.3 | Separate file-writing review and authorization. |
| DEFER-06 | macOS release | Deferred | Windows/Android/iOS are the MVP targets. | U; PLATFORM-01 | Platform scope approval. |
| NAME-00 | Tunibo as current product name | Superseded | Replaced by NAME-01; retain only as historical context. | U | New explicit naming decision. |

## Open decisions and alignment notes

- **Statistics product policy:** approved rules for pending skips, active listening time, local-history controls and guest-history transfer are listed above. Still open: numeric qualified-play/near-complete thresholds, completion semantics, session end/timeout, multiple pending skips, other navigation, event granularity, settings UX, export format and acceptable crash-loss window. The 90% and 10-second thresholds are PoC candidates, not approved constants.
- **Statistics implementation:** session boundaries, event log necessity, clock handling, durable checkpoints, queue overflow/backpressure and recovery representation remain Proposed. History must survive missing files; design deletion/merge references without cascading history loss.
- **Existing scope questions remain open:** guest → account playlist defaults (distinct from the approved history-transfer offer), favorite sync, trash/backup retention, artwork limits, minimum OS versions, additional formats/gapless, source/distribution license, release budget and localization. Basic EQ is conditional P1; the remaining full MVP scope still needs explicit approval.
- **Model/protocol alignment pending:** S §4 refines D's generic versions/outbox states into separate revisions, confirmed/pending projections and snapshot staging. D §5.3's snapshot RPC is conceptual; S §11 proposes create/read snapshot calls. A uses the conceptual name `account_change_counter`, while D/S use `account_sync_state`. These are documented levels of proposal, not competing approved schemas; reconcile concrete names/contracts before migrations.
- **Approval labels corrected:** S §2 and D §1 combined approved invariants with proposed enforcement/restore details. Their notes now distinguish those levels without removing algorithms or requirements. A's duplicate register points here for status.
- **Historical sources:** existing technical links and dated service claims are retained as research leads, not newly verified platform guarantees. Recheck current official documentation and pricing during PoC/release planning.

The U2 rows above record further explicit product-owner decisions. Only the specified behaviors and scope priorities are approved; numerical thresholds, implementation approaches, licenses and the overall MVP remain open or require PoC.
