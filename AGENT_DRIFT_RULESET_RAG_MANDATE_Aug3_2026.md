# AGENT DRIFT RULESET + VW NEXUS RAG MANDATE
**Date:** Aug 3, 2026
**Authority:** Canonical. Applies to ALL agents (Cascade, GLM, KIMI, FUBBU workers, ZHARK, any future agent) on ALL projects in this workspace.
**Scope:** DQLOSTTRANSLATION, Modern_X64 (Void Walkers X64), VW Nexus, Liminal Lore, FUBBU, ZHARK, VHB Super BIOS.
**Supersedes:** All informal session conventions. Project roadmaps reference this document as the governing ruleset.

---

## 0. The Mandate of Sound Logic

Every claim an agent makes must satisfy one of three evidence classes:

1. **SOURCE** — direct citation of code: `file.ext:line` with the actual instruction/statement read this session.
2. **COMMAND** — output of a command run this session (build log, verifier, emulator telemetry, SQL count).
3. **DERIVATION** — a logical chain from SOURCE/COMMAND facts, with each step stated.

Anything else is **SPECULATION** and must be labeled `HYPOTHESIS:` in docs and chat. Hypotheses never gate builds, never justify patches, and never appear in "Done" lists.

**Contradiction Rule:** If two documents (or a document and source) disagree, work STOPS on that topic until the contradiction is resolved against source/command evidence and the losing document is annotated `SUPERSEDED`. Real example: Aug 2, 2026 — two docs published opposite `DESC_PUBLISH_OFF` values (0x72194 vs 0x63674) within 24 hours. Source check resolved it (0x63674). This class of drift is now forbidden by Rule R4 below.

---

## 1. The Ten Mandates (M1–M10)

### M1 — Frozen Inputs, Hash-Pinned
Every build input (EXE, HBD, tree, base disc, translation JSON) gets a SHA-256 recorded in `FROZEN_INPUTS.json` beside the builder. The builder asserts all hashes at start and **aborts** on mismatch.
*Why:* `translation/SLUSP012.06` was silently replaced with a wrong file (166,649 word diffs) and poisoned builds V101b–V104. Never again.

### M2 — Guard-Before-Patch, No Exceptions
Every binary patch site must verify the expected original bytes/instruction and **abort the build** on mismatch. No warn-and-continue, no blind writes. A patch that finds 0 matches on a boot-critical path is a BUILD FAILURE, not a log line.
*Why:* `patch_cdrom_stall_bypass()` corrupted the CD-ROM data path in every pre-Aug-2 build by overwriting a non-matching instruction.

### M3 — Single Offset Registry
One machine-readable offset table per project (`offsets.json`) with disassembly evidence per entry. Header constants are GENERATED from it, never hand-edited. Any offset change requires a re-verification entry in the same file.
*Why:* DQ4 decompressor offsets were "verified and corrected" three times in 10 days (Jul 22, Jul 31, Aug 2).

### M4 — No Build Exists Without a Boot Test
A build is only real when its gate results are appended to `BUILD_LEDGER.md` (one line per build: artifact, hash, gate results, timestamp). Untested builds are deleted or quarantined to `old/` — they do not accumulate in project root.
*Why:* 26 `.bin` artifacts currently sit in DQLOSTTRANSLATION root; two "best candidate" discs sat untested for weeks while new architecture was built on sand.

### M5 — One Canonical State Doc Per Project
Each project has exactly ONE living status document (`CURRENT_STATE.md` or the active surgical roadmap), updated in place. Dated session docs are write-once archives. When a session doc contradicts the canonical doc, the canonical doc wins after source verification.
*Why:* Same-day contradictory "CRITICAL FIX" docs (Aug 2) happened because every session minted a new authority.

### M6 — Dead Code Is Deleted, Not Kept
"Compiles but unwired" is forbidden. Any module not reachable from the live execution path is deleted or wired within the same sprint. Forbidden categories: parallel state machines, unused validation layers, alternate render paths nobody calls, "legacy compat" constants.
*Why:* X64 has GameStateManager + 5 GameStates_* files (one with a latent compile error), TransitionGuardMatrix (never called), and GameStateManager (never instantiated) — all created Jul 28, all dead.

### M7 — One Active Path
No new architecture while an existing path has an untested artifact or an open empirical question. Pivoting requires: (a) the current path's blocker documented with SOURCE/COMMAND evidence, (b) user sign-off.
*Why:* DQ4 ran 5 architectural approaches (Frankenstein, Reverse, Native, Re-encode, VHB BIOS) while the two cheapest tests sat unrun.

### M8 — Determinism (1:1)
Identical inputs must produce byte-identical outputs. No timestamps, host paths, or environment-dependent bytes in build outputs. Rebuild-and-compare is the regression test: `build → hash → rebuild → hash → diff == 0`.
*Why:* This is the shipping bar the user set. Non-deterministic builds make every bug report irreproducible.

### M9 — E2E Gate Ladder, Recorded
Each project defines a gate ladder (see its roadmap). Every session that changes code ends by running the highest reachable gate and recording the result in the ledger. "It compiles" is not a gate.
*Why:* X64's save system, audio, and settings have never been runtime-tested; DQ4's static verifier passed 19/19 on discs that don't boot.

### M10 — Surgical Changes Only
No big-bang refactors. One system per change, one change per build, gate after each. Multi-file sweeps across a monolith (e.g. GameLoop.cpp) require user approval and a revert point.
*Why:* The Jul 28 X64 session ("Senior JRPG Logic & State Machine Overhaul") produced only dead code and regressed playability — the "broke the mold" event.

---

## 2. Agent Session Protocol

### Session Start (mandatory, in order)
1. Read the project's canonical state doc (see §4).
2. Query the project RAG corpus for the task topic (see §3). Cite retrieved chunks in your plan.
3. Verify any load-bearing fact against SOURCE, not docs.
4. State the session's ONE objective and its gate.

### During Session
- Cite `file:line` for every claim about code.
- Docs and constants may be stale — the binary/source is the only truth.
- Before ANY binary patch: verify original bytes. Before ANY refactor: confirm the target is actually on the live path.
- Keep a running list of contradictions found; resolve or flag them before session end.
- No new files unless strictly necessary. No new architecture without user approval (M7, M10).

### Session End (mandatory, in order)
1. Run the highest reachable gate (M9). Record result in the project's `BUILD_LEDGER.md`.
2. Update the canonical state doc in place (not a new dated doc) — dated archives optional.
3. Index changed docs into the project RAG corpus (§3, step 4).
4. List open contradictions and hypotheses explicitly.

---

## 3. VW NEXUS RAG Mandate (binding on all agents)

**Every agent must query the RAG before writing code and must index its outputs at session end.** This is not optional — it is the institutional memory that prevents re-diagnosing solved problems.

### 3.1 Corpora (one DB per project knowledge base)

| Corpus | Root | DB |
|---|---|---|
| VW X64 / toolchain | `Modern_X64/study` | `Modern_X64/tools/vw_rag/vw_rag_index.db` |
| DQLOSTTRANSLATION | `DQLOSTTRANSLATION/study` | `DQLOSTTRANSLATION/study/vw_rag_index.db` |
| Cross-project governance | `study/` (workspace root) | `study/vw_rag_index.db` |

### 3.2 Mandatory environment

Always set `VW_RAG_NO_EMBED=1`. This forces BM25 keyword-only mode and skips the sentence-transformers/HuggingFace path that **hangs the CLI** (Aug 3 root cause of the "vw rag hung" incident). Run all commands from `Modern_X64/tools`.

### 3.3 Commands

```powershell
# Query (do this BEFORE coding, every session)
$env:VW_RAG_NO_EMBED="1"
python -m vw_rag query "your task terms" --max 8 `
  --db "c:\LuxAura\VoidWalkers_Project\DQLOSTTRANSLATION\study\vw_rag_index.db" `
  --root "c:\LuxAura\VoidWalkers_Project\DQLOSTTRANSLATION\study"

# Index (do this AFTER writing docs, every session)
python -m vw_rag index `
  --db "c:\LuxAura\VoidWalkers_Project\DQLOSTTRANSLATION\study\vw_rag_index.db" `
  --root "c:\LuxAura\VoidWalkers_Project\DQLOSTTRANSLATION\study"

# Status
python -m vw_rag status
```

Omit `--db`/`--root` for the default VW X64 corpus. Vendored trees (duckstation-src, PSn00bSDK, PSoXide, etc.) are excluded from indexing — never index third-party source into the knowledge base.

### 3.4 Query Discipline
- Query with 2–5 domain terms (BM25 ranks OR semantics; AND is tried first for precision).
- Zero results is a signal to re-term the query, not to skip RAG.
- Retrieved chunks must be cited as `relative_path` in session notes.

---

## 4. Canonical Documents (single sources of truth)

| Project | Canonical doc |
|---|---|
| This ruleset | `study/AGENT_DRIFT_RULESET_RAG_MANDATE_Aug3_2026.md` (root) |
| DQ4 Frankenstein | `DQLOSTTRANSLATION/study/SURGICAL_COMPLETION_DQ4_Aug3_2026.md` |
| Void Walkers X64 | `Modern_X64/study/SURGICAL_COMPLETION_VWX64_Aug3_2026.md` |

All other dated docs are archives. Archives may inform; only canon + source govern.

---

## 5. Enforcement

- An agent that violates M2/M4/M6 marks its own output `NON-CANONICAL`.
- The user may ask any agent "which mandate authorizes this change?" — a valid answer cites a mandate or gets reverted.
- FUBBU verifier structural checks should include: `cites_source_lines`, `no_unlabeled_hypothesis`, `ledger_updated`.

*Sat Nam. Sound logic, 1:1 determinism, one path, one truth.*
