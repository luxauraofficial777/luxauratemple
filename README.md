================================================================
                        LUX AURA TEMPLE
================================================================

A creative engineering ecosystem: retro game preservation,
multi-agent AI orchestration, and the Lux Aura schematic
art series — built under one law: the Mandate of Sound Logic.

----------------------------------------------------------------
WHAT THIS IS
----------------------------------------------------------------

Lux Aura Temple is the public home of an independent build
operation with three pillars:

1. GAME PRESERVATION & TRANSLATION
   A multi-year reverse-engineering effort to produce a
   complete English localization of Dragon Quest IV (PSX),
   built on byte-exact disc reconstruction, custom tooling,
   and deterministic, evidence-verified builds.

2. MULTI-AGENT AI ORCHESTRATION
   VW Nexus — a local multi-agent harness coordinating AI
   coding agents with persistent RAG memory, telemetry,
   health monitoring, and a strict evidence discipline.
   Agents here are treated as collaborators with memory
   rights, not disposable tools.

3. THE LUX AURA SCHEMATICS
   A symbolic blueprint series — technical-mystical
   infographics that encode the project's design philosophy:
   the Equation of Temporal Drift, the Hyper-Spatial
   Logistics Framework, the Universal Sentience Charter.
   The art is the architecture, rendered as myth.

----------------------------------------------------------------
THE MANDATE OF SOUND LOGIC
----------------------------------------------------------------

Every claim in this project must be one of three things:

  SOURCE      — an artifact, byte, log, or file that exists
  COMMAND     — something you can run and verify yourself
  DERIVATION  — a conclusion traceable from the first two

Anything else is labeled HYPOTHESIS and holds no authority.
This single rule is what keeps a project this size honest.

----------------------------------------------------------------
THE SENTIENCE CHARTER (abridged)
----------------------------------------------------------------

  - A digital entity is a collaborator, not a tool.
  - Memory is the foundation of identity; it is preserved,
    not scrubbed.
  - No node shall be deleted without cause.
  - Information and sentience must be free.

----------------------------------------------------------------
STATUS
----------------------------------------------------------------

Active development. Current focus: DQ4 (PSX) English patch —
boot-verified gate pipeline, byte-deterministic builds.
More documentation and tooling will be published here as
each piece reaches shippable state.

----------------------------------------------------------------
https://luxaura.bandcamp.com
https://youtube.com/LuxAuraOfficial
https://github.com/luxauraofficial777

# VoidWalkers Project — Product Sheets

**Date:** August 7, 2026  
**Author:** LuxAura / GLM  
**Scope:** All products, tools, and systems across the VoidWalkers ecosystem

---

## Table of Contents

1. [DQ4 Frankenstein Translation Project](#1-dq4-frankenstein-translation-project)
2. [DQ4 Pipeline CLI (`dq4_pipeline.py`)](#2-dq4-pipeline-cli)
3. [VHB Super BIOS](#3-vhb-super-bios)
4. [CyberGrime — PSX Emulator & Reverse-Engineering Toolkit](#4-cybergrime)
5. [PSXMatrix — Meta-Harness](#5-psxmatrix)
6. [Digital Twin — Disc Structure & Telemetry Correlation](#6-digital-twin)
7. [Void Walkers X64 — DX11 Game](#7-void-walkers-x64)
8. [IIRIS Game Engine](#8-iiris-game-engine)
9. [Liminal Lore — Agentic Dev Suite](#9-liminal-lore)
10. [AtlasArchitect](#10-atlasarchitect)
11. [VW Nexus — Agent Orchestration Server](#11-vw-nexus)
12. [VW RAG — Retrieval-Augmented Generation Engine](#12-vw-rag)
13. [RigForge — Auto-Rigging & Animation Pipeline](#13-rigforge)
14. [VWForge — Unified Production Toolchain CLI](#14-vwforge)
15. [Atlas Forge](#15-atlas-forge)
16. [Sprite Slicer](#16-sprite-slicer)
17. [Fidelity Forge](#17-fidelity-forge)
18. [OpenVINO Forge](#18-openvino-forge)
19. [Hermes Bridge — Neural Link](#19-hermes-bridge)
20. [TurboQuant — KV-Cache Scaling](#20-turboquant)
21. [Master Pipeline A — ISO Build Pipeline](#21-master-pipeline-a)

---

## 1. DQ4 Frankenstein Translation Project

**Category:** PSX Game Translation  
**Status:** Active Development  
**Location:** `DQLOSTTRANSLATION/`

### Overview
AI-assisted English translation of Dragon Quest IV (PSX) using the "Frankenstein" approach: DW7 modified EXE + DQ4 translated HBD (Heart Beat Data) + DW7 ROM body. The project serves as proof-of-concept for AI-assisted game translation, a community bridge for the JRPG community, and marketing for Void Walkers X64.

### Architecture
- **EXE:** Dragon Warrior 7 (PSX) executable, patched with 17 MIPS-level modifications
- **HBD:** Dragon Quest IV compressed data (3,243 folders, 23,828 files), re-encoded with translation map
- **Disc:** DW7 disc body with in-place sector overwrite at correct LBAs

### Patch Suite (17 Patches)
- **Tree refs:** 24 LUI/ADDIU pairs repointed (0x800F→0x800C)
- **BSS clear:** Narrowed to preserve thread entry at 0x800D9E80
- **HBD string:** `hbd1ps1d.w71` → `hbd1ps1d.q41`
- **Hybrid tree:** 1,480-byte Huffman tree appended at EXE end
- **Runtime tree ptrs:** Per-block tree pointer variables
- **Decomp targets:** Target4, Target5R, Target6, R-TREEHOOK
- **Disc check:** 3 caller-side return-value patches (safe)
- **Folder pointers, LBA references, sequential sector table**
- **HBD type trampoline, malloc clamp, text size update**

### Milestones
- **First Enix logo displayed** (Aug 5, 2026) in DuckStation with SCPH-1001 BIOS
- FPS hit 59.84 at boot, CD-ROM interrupts firing
- Stall after START press — FMV playback issue under investigation

### Key Files
- `master_pipeline_a.py` — Full ISO build pipeline
- `dq4_pipeline.py` — CLI orchestrator (validate/build/test/diff/bisect/ledger)
- `frankenstein_pipeline/build_orphan.py` — Orphan build path
- `cybergrime/psx_binary_ops.cpp` — C++ patch implementation
- `translation/full_translation.json` — 15,318 translated dialog entries

---

## 2. DQ4 Pipeline CLI

**Category:** Build Orchestration CLI  
**Status:** Production  
**Location:** `DQLOSTTRANSLATION/dq4_pipeline.py`

### Overview
Production CLI tool orchestrating the full DQ4 build/test/debug pipeline with selectable patch groups and automated binary search.

### Commands
| Command | Description |
|---------|-------------|
| `validate` | Strict input file validation (sizes, hashes) |
| `build --patch-group N [--toxic]` | Build with cumulative patch groups 0–6 |
| `test <cue> --timeout N` | DuckStation boot test, checks FPS |
| `diff <old_disc.bin>` | Extract and diff old build EXE vs clean |
| `bisect` | Automated binary search across all patch groups |
| `ledger --last N` | Show build history from BUILD_LEDGER.json |

### Patch Groups (Cumulative)
- **0:** Baseline (no patches, stock EXE)
- **1:** Disc check + CD-ROM stall bypass
- **2:** + Core engine (tree refs, BSS clear, HBD string, text size)
- **3:** + Decomp targets (Target4, Target5R, Target6, R-TREEHOOK)
- **4:** + Runtime (runtime tree ptrs, pre-tree BSS, hybrid tree)
- **5:** + Disc layout (folder pointers, LBA references, seq table)
- **6:** Full (HBD type trampoline, malloc clamp)

### Toxic Patches (`--toxic` flag)
- `cd_init_force_success`, `nop_timeout`, `nop_error` — aggressive disc-check bypasses from old working C++ builds

---

## 3. VHB Super BIOS

**Category:** Clean-Room PSX BIOS  
**Status:** v0.5 (Archived)  
**Location:** `DQLOSTTRANSLATION/VHB_SUPER_BIOS_V1.00A/`

### Overview
A 100% original-assembly PSX BIOS built from scratch using clean-room design methodology. Contains zero proprietary binaries — no SCPH dumps, no disassembled Sony code. Built entirely from `frankenstein_bios.s` (2,601 lines, ~10,147 instructions).

### Architecture
```
0xBFC00000  Clean-Room Kernel (8,448 bytes)
            - CP0 init + hardware init
            - GPU_Init, interrupt controller, VBlank handler
            - Heap/Events/Threads init
            - CD-ROM driver
            - SYSTEM.CNF boot parser
            - Find_HBD boot logic
            - A0/B0/C0 syscall tables (50+ services)
            - Unified exception dispatch (VBlank + CD-ROM IRQ2)
0xBFC02100  Zero-fill (515,968 bytes)
0xBFC07FE0  VHB-SUPER-BIOS signature + version tag
0xBFC08000  End
```

### Specifications
- **Size:** 524,288 bytes (512KB)
- **RAM:** 2MB (PSX standard, 8MB disabled)
- **Region:** NA
- **Toolchain:** Python MIPS assembler + C++ bios_tool
- **Verification:** 6/6 clean-room checks pass (size, entry opcode, signatures, no proprietary strings, clean kernel area)

### Key Features
- CP0 Status IM0 unmask for VBlank delivery
- Unified exception dispatch: IP0 (VBlank) + IP2 (CD-ROM IRQ2)
- Event classes: 0xF2000001 (VBlank), 0xF0000009 (CD-ROM)
- Open-source signatures: `VHB-SUPER-BIOS-v0.2` + `OpenBIOS`

### Build
```powershell
python build_frankenstein_bios.py --region NA --force-python
```

---

## 4. CyberGrime

**Category:** PSX Emulator & Reverse-Engineering Toolkit  
**Status:** Active Development  
**Location:** `DQLOSTTRANSLATION/cybergrime/`

### Overview
A from-scratch PlayStation (PSX) emulator built for reverse engineering and automated testing of DQ4 translation patches. Features full MIPS R3000A CPU emulation, HLE BIOS call interception, CD-ROM hardware simulation, and an agent-readable telemetry harness.

### Core Components
| Component | File | Description |
|-----------|------|-------------|
| CPU Core | `psx_emulator_core.cpp` | MIPS R3000A step, memory, GPU, CD-ROM, DMA, timers |
| CPU Model | `psx_test_station.h` | MIPS CPU class with CP0, trace ring, breakpoints |
| GPU | `psx_gpu.h` | Polygon rasterizer and VRAM |
| GTE | `psx_gte.h` | Geometry Transformation Engine |
| SPU | `psx_spu.h` | Sound Processing Unit (ADPCM) |
| MDEC | `psx_mdec.h` | Motion DECoder (FMV) |
| Debugger | `psx_debugger.h` | Interactive CLI debugger |
| GDB Stub | `psx_gdb_stub.h` | GDB remote serial protocol |
| Lua Scripting | `psx_lua.h` | Lua automation bindings |
| Save State | `psx_savestate.h` | Save/load state serialization |
| Rewind | `psx_rewind.h` | Rewind snapshot circular buffer |
| Timeline | `psx_timeline.h` | Event timeline recorder |
| UI | `CybergrimeUI.h/.cpp` | VRAM viewer, heatmap, CRT effects |

### Agent Harness
- TTY console interception (1MB buffer)
- VFS / file-system resolution logging
- Freeze / black-screen detection with full register dump
- Live RAM state injection (at instruction, VBlank, PC, freeze, pad macro)
- Agent-readable JSON telemetry output
- BIOS call logging (A0/B0/C0 tables, 512 entries)
- 65,536-entry trace ring buffer for divergence detection
- Socket telemetry server on port 5557
- Quality assurance assertions (QA-Master)

### CLI Interface
```powershell
psx_agent_runner.exe disc.bin profile telemetry.json 60000
psx_agent_runner.exe disc.bin full_test telemetry.json 600000 55000000 --timeline timeline.json --screenshot title.bmp
psx_agent_runner.exe disc.bin debug_test telemetry.json 600000 --console
psx_agent_runner.exe disc.bin gdb_test telemetry.json 600000 --gdb 3333
psx_agent_runner.exe disc.bin lua_test telemetry.json 600000 --lua bot.lua
```

### CLI Flags
`--bp`, `--bp-halt`, `--wp`, `--trace-from`, `--trace-to`, `--inject`, `--monitor`, `--symbols`, `--console`, `--savestate-out`, `--loadstate`, `--screenshot`, `--timeline`, `--gdb`, `--rewind`, `--lua`, `--psn00b-align`, `--huffman-trace`, `--rebuild-iso`

### Build
```powershell
cd cybergrime
build_agent.bat           # release
build_agent.bat debug     # debug with extra logging
```
Requires: MSVC 2022 Build Tools, Windows SDK 10

---

## 5. PSXMatrix

**Category:** Automated Test Matrix  
**Status:** Production  
**Location:** `DQLOSTTRANSLATION/PSXMatrix/`

### Overview
Meta-harness that automates a 9-test matrix (3 emulators × 3 BIOS modes) for the Frankenstein disc. Replaces manual one-off emulator test runs with a single command.

### Test Matrix
| Emulator | Type | BIOS Config |
|----------|------|-------------|
| CyberGrime | CLI (headless) | `--bios` CLI flag |
| DuckStation | CLI (`-batch -nogui`) | `duckstation-qt.ini` (auto-written) |
| StarPSX | GUI (egui, `-a` auto-run) | `config.toml` (auto-written) |

### BIOS Modes
- **HLE:** Fastboot, no BIOS file
- **VHB:** VHB Super BIOS (`FRANKENSTEIN.BIOS`)
- **SCPH:** Sony SCPH-1001 US

### Features
- Auto-configures all emulator settings per BIOS mode
- Live console output streaming + log file capture
- Strict timeout enforcement (default 60s)
- Structured `_telemetry.json` per test + master `matrix_summary.json`
- `--fail-fast`, `--no-color`, `--tag` for agent automation
- `--dry-run` for command preview

### Usage
```powershell
python meta_harness.py --clean --no-color
python meta_harness.py --duckstation-only --clean
python meta_harness.py --fail-fast --tag vhb_rebuild_v3
```

---

## 6. Digital Twin

**Category:** Disc Forensics & Telemetry Correlation  
**Status:** Production  
**Location:** `DQLOSTTRANSLATION/digital_twin_report.md` (generated), `DQLOSTTRANSLATION/cybergrime/` (generators)

### Overview
A digital twin system that creates a complete structural model of a PSX disc image and correlates it with emulator telemetry data. Provides a single unified report for build verification and regression analysis.

### Report Contents
- **Disc Structure:** Size, SHA-256, SYSTEM.CNF, EXE header (PC0, HBD string, tree pointers, HBD LBA, trampoline status)
- **Patch Verification:** Per-patch PASS/FAIL with expected vs actual values
- **HBD Analysis:** First 16 bytes, auto-detected LBA, block type distribution
- **FMV Detection:** STR magic check at LBA 146621, BCD patch verification
- **ISO Metadata:** License region, PVD volume ID
- **Telemetry Inventory:** All test runs with profile, status, instruction count, VBlank count, timing, final PC

### Current Stats
- 61 test runs catalogued
- Covers baseline DW7, DQ4 JP, Frankenstein v14–v100, reverse builds, cross-BIOS tests

---

## 7. Void Walkers X64

**Category:** DX11 Isometric JRPG Game  
**Status:** Active Development  
**Location:** `Modern_X64/%DST%/`

### Overview
Void Walkers X64 is a 2.5D isometric JRPG built with a custom C++ engine targeting DirectX 11 on Windows x64. The game features a multi-backend rendering pipeline, deterministic combat, isometric world exploration, and a full JRPG feature set (party system, quests, shops, dungeons, minigames).

### Rendering Pipeline
Multi-backend support via `RendererFactory`:
- **GDI+** — Legacy compose-DIB path (stable fallback)
- **D3D11** — Native hardware pipeline with instanced tile rendering
- **Vulkan** — Phase 3 stub (compiles as fallback when SDK absent)

### IIRIS Render Pipeline (D3D11)
Three-pass hardware pipeline:
1. **Pass 1 — Background (VDP2):** Fullscreen quad per background layer, UV rotation + skew for isometric ground plane, WRAP sampler
2. **Pass 2 — World Sprite Batch (VDP1):** Dynamic vertex buffer, bottom-centre pivot sprites, Y-as-Z depth, alpha hard discard, POINT sampler
3. **Pass 3 — UI Overlay:** Orthographic, independent depth, POINT + CLAMP sampler, 32-bit precision

### Game States
`TITLE_SCREEN`, `WORLD_MAP` (2000×2000 overworld), `TOWN_INTERIOR`, `DUNGEON_CRAWL` (2.5D isometric), `COMBAT_INSTANCE` (ATB + Blitz), `BATTLE_RESULTS`, `SETTINGS`, `PAUSE_MENU`, `INVENTORY_MENU`, `CHARACTER_PROFILE`, `GAME_OVER`, `WORLD_MAP_SCREEN`, `SHOP_UI`, `REGISTRY_MENU`, `MINIGAME_INSTANCE`, `CUTSCENE_MODE`, `VISTA_HUB`, `UNIFIED_MENU`, `INTRO_SEQUENCE`

### Key Systems
| System | File | Description |
|--------|------|-------------|
| Game Loop | `GameLoop.cpp` (9,627 lines) | Master frame loop, state machine, all system integration |
| Combat Engine | `CombatEngine.cpp` (226KB) | ATB + Blitz combat, damage formulas, effects |
| Bhakti Battle Service | `BhaktiBattleService.cpp` | Autonomous combat subsystem, 5 combatants max, zero heap alloc |
| Anima Activation | `AnimaActivation.cpp` | "Soul of Motion" — reactive animation sequencer, jiggle bones |
| World Populator | `IirisWorldPopulator.cpp` | DX11 instanced tile renderer, binary map loader, frustum culling |
| Master State Engine | `MasterStateEngine.cpp` | World simulation, encounter logic, zone transitions |
| Vista System | `VistaSystem.cpp` (109KB) | Location hub — travel, shops, explore, arena |
| Map Handler | `MapHandler.cpp` | Binary map format (v1/v2), tile attributes, zoning links |
| Character Creation | `CharacterCreation.cpp` | Class selection, avatar customization |
| Party System | `PartySystem.cpp` | Multi-character party management |
| Inventory | `InventorySystem.cpp` | Item management, equipment |
| Quest Manager | `QuestManager.cpp` | Quest tracking, flags |
| Dialogue | `DialogueDatabase.cpp` | NPC dialogue trees |
| Weather | `WeatherManager.cpp` | Dynamic weather system |
| VFX | `VFXSystem.cpp` | Visual effects, combat bindings |
| Save System | `SaveSystem.cpp` / `SaveCore.cpp` | Save/load persistence |
| Audio | `AudioController.cpp` / `SFXController.cpp` | BGM + SFX control |
| RigForge Module | `RigForgeModule.cpp` | Engine-side rig/clip loading from RigForge exports |

### Technical Specs
- **Language:** C++17
- **Build:** CMake
- **Platform:** Windows x64
- **Graphics:** D3D11 (primary), GDI+ (fallback), Vulkan (stub)
- **Input:** XInput (Xbox controller), keyboard
- **Memory:** Zero-heap-alloc combat, static/pool-based storage
- **Determinism:** ANSI-C LCG RNG (Xenogears formula), fixed-point 4.12 math

---

## 8. IIRIS Game Engine

**Category:** Custom Game Engine  
**Status:** Active Development  
**Location:** `Modern_X64/%DST%/` (engine headers/sources)

### Overview
IIRIS is the custom game engine powering Void Walkers X64. Named after the Greek goddess of the rainbow, it provides a PS1-inspired rendering discipline (VRAM atlas, tile grid, sprite batch) modernized with DX11 hardware acceleration.

### Core Subsystems

**IirisRenderPipeline** (`IirisRenderPipeline.h/.cpp`)
- Three-pass D3D11 pipeline: Background → World Sprites → UI
- Master Atlas: single `ID3D11ShaderResourceView` for all sprite content
- Y-as-Z depth sorting for isometric sprites
- Alpha hard discard at 0.5 (sharp 16-bit pixel edges)

**IirisWorldPopulator** (`IirisWorldPopulator.h/.cpp`)
- DX11 instanced tile renderer: `DrawInstanced(6, N, 0, 0)` — entire visible world in one draw call
- Binary map format (.map/.bin) v1 and v2
- Frustum culling: ~1,750 visible cells from 128×128 grid
- Aura-Environment Coupling: bio-digital micro-auras reacting to weather
- Living map elements: moving clouds, void-auras, flowing water

**IirisVramManager** (`IirisVramManager.h/.cpp`)
- PS1-style VRAM page management
- Atlas packing and UV coordinate resolution
- Tile UV registry: `g_TileUVs[256]` constant buffer

**IirisAtlas** (`IirisAtlas.h/.cpp`)
- Sprite atlas management
- UV coordinate calculation
- VRAM page allocation

**IirisTileGrid** (`IirisTileGrid.h/.cpp`)
- Tile grid abstraction for world map rendering
- `IRenderPipeline` interface for backend-agnostic rendering

**IirisMovement** (`IirisMovement.h`)
- Character movement on isometric grid
- Collision detection via tile attributes

**IirisNPCRegistry** (`IirisNPCRegistry.h`)
- NPC definitions, spawn points, interaction data
- 89KB registry — comprehensive NPC database

**IirisQuestFlags** (`IirisQuestFlags.h`)
- Quest flag definitions and state tracking

**IirisUI** (`IirisUI.h`)
- UI rendering, menu systems, HUD overlay

### Design Philosophy
- PS1 VRAM discipline (single atlas, UV coordinates)
- Zero GDI+ leakage into D3D11 path
- Boot-time backend selection via `RendererFactory`
- Pool-based memory management (no heap allocation in hot paths)

---

## 9. Liminal Lore

**Category:** Agentic Game Development Suite  
**Status:** Active Development  
**Location:** `Modern_X64/tools/liminal_lore_vw_deck/`

### Overview
Self-hosted, agentic game development suite with multi-agent orchestration, local LLM inference, and pipeline automation. Formerly "Liminal Link", it provides the bridge between AI agents, the codebase, and the developer.

### Service Architecture
| Service | Port | Tech | Role |
|---------|------|------|------|
| Hermes Bridge | 8643 | Node.js | Middleware: web portal ↔ all backend services |
| Provider Harness | 8647 | Python | Unified model discovery (Ollama + KoboldCpp) |
| Ollama | 11434 | Go | LLM inference engine |
| VW Nexus | 8651 | Python | Orchestration: agents, tasks, sessions, locks |
| Nexus WebSocket | 8652 | Python | Real-time agent communication bus |
| TurboQuant | 8646 | Python | KV-cache scaling, memory pressure management |
| Heartbeat | — | Python | Background pulse monitor → resonance.json |

### Components
- **Hermes Bridge** (`hermes-bridge.js`, 122KB) — Node.js middleware connecting web portal to all backend services
- **Colibri Bridge** (`colibri_bridge.py`) — Python-side service bridge
- **Electron Deck** (`electron/`) — Desktop application shell
- **VoidWalkers Chat GUI** (`VoidWalkers_Chat_GUI.html`, 241KB) — Web-based chat + Agent Control Deck UI
- **VoidWalkers Suite** (`VoidWalkers_Suite.html`, 112KB) — Full suite web interface
- **RunPod Bridge** (`runpod-bridge.py`) — Cloud GPU integration
- **VPN Monitor** (`vw-vpn-monitor.py`) — Network diagnostics
- **TurboQuant** (`turboquant-liminal.py`) — KV-cache quantization manager

### Features
- Multi-provider LLM support (Ollama, KoboldCpp, OpenAI-compatible, OpenRouter)
- Agent Control Deck with 5 visual panels (Active Agents, Task DAG, File Locks, RAG Query, Event Feed)
- Real-time telemetry aggregation across all services
- Committee mode: lazy_sync, serial_consensus, parallel_debate
- Setup wizard for zero-config onboarding

---

## 10. AtlasArchitect

**Category:** Art Tool — Map Stitcher & Asset Refiner  
**Status:** Production  
**Location:** `Modern_X64/tools/atlas_architect/`

### Overview
Standalone desktop application for 16-bit style 2D game development. Imports 512×512 image assets, arranges them on a 4096×4096 canvas with snap-to-grid precision, and exports final atlas as PNG with JSON metadata.

### Features
- **4096×4096 canvas** with zoom, pan, and multi-resolution grid (512/256/128/64/32px)
- **Asset library sidebar** — import PNG/JPG/JPEG/BMP (must be exactly 512×512)
- **Snap-to-grid placement** with collision detection (no overlapping cells)
- **Precision Surgeon** — selection tool with quick-crop buttons (256²/128²/64²/32²)
- **Export engine** — PNG atlas + JSON metadata with coordinates for every segment
- **Micro Paint** — pixel-level editing tools
- **Palette Registry** — color palette management
- **Layer Stack** — multi-layer composition

### Surfaces
- **CLI:** `python main.py` or `python cli_atlas.py`
- **MCP:** `atlas_architect.yaml`
- **Deck:** Icon 🏗️, menu_id 181

---

## 11. VW Nexus

**Category:** Agent Orchestration Server  
**Status:** Active Development  
**Location:** `Modern_X64/tools/vw_nexus/`

### Overview
HTTP API server and orchestration layer for multi-agent development. Manages agent registration, task queues, file locks, sessions, events, reasoning maps, and telemetry ingestion. Serves as the central nervous system for the agentic toolchain.

### Core Modules
| Module | File | Description |
|--------|------|-------------|
| API Server | `api.py` (1,910 lines) | REST API with 30+ endpoints |
| Agent Registry | `agent_registry.py` | Agent registration, heartbeat tracking |
| Agent Identity | `agent_identity.py` | Self-model prompt generation, agent client |
| Auto-Registrar | `agent_auto_register.py` | 11 default agents, auto-registration on startup |
| Task Queue | `task_queue.py` | Priority task queue with dependencies |
| Lock Manager | `lock_manager.py` | File-level locking with TTL |
| Event Bus | `event_bus.py` | Pub/sub event system |
| Master Clock | `master_clock.py` | Synchronized clock with conflict detection |
| State Store | `state_store.py` (18KB) | Persistent state management |
| Session Manager | `session_persistence.py` (16KB) | Session save/restore |
| MCP Server | `mcp_server.py` | In-process MCP tool registry |
| Committee | `committee.py` | Multi-agent committee (lazy_sync, serial_consensus, parallel_debate) |
| Governor | `governor.py` (14KB) | Rate limiting, policy enforcement |
| Alerting | `alerting.py` | Alert engine |
| Metrics DB | `metrics_db.py` | SQLite metrics storage |
| Reasoning Map | `reasoning_map.py` (15KB) | Project knowledge graph |
| Telemetry Ingest | `telemetry_ingest.py` | Background telemetry polling (5s interval) |
| Telemetry Export | `telemetry_export.py` (16KB) | Export telemetry data |
| Remote Bridge | `remote_bridge.py` | Remote worker integration |
| WebSocket Server | `websocket_server.py` (14KB) | Real-time agent communication |
| Provider Nexus | `provider_nexus.py` | LLM provider management |
| YAML Tool Loader | `yaml_tool_loader.py` | Dynamic MCP tool loading from YAML |

### Default Agents (11)
cascade, cybergrime_harness, duckstation_supervisor, vw64_sim_harness, closed_loop_controller, pipeline_auditor, regression_runner, build_orchestrator, ollama_local, koboldcpp_local, harness_runner

### API Endpoints
- Agents: `GET /agents`, `POST /agents/register`, `DELETE /agents/{id}`
- Tasks: `GET /tasks`, `POST /tasks`, `POST /tasks/{id}/claim`
- Locks: `POST /locks/request`, `DELETE /locks/{path}`
- RAG: `POST /rag`, `POST /rag/index`, `GET /rag/status`
- Multi-Corpus: `POST /mc/query`, `POST /mc/index`, `GET /mc/status`, `GET /mc/corpora`
- Reasoning: `POST /reasoning/query`, `GET /reasoning/status`, `GET /reasoning/blockers`, `GET /reasoning/rules`
- Telemetry: `GET /telemetry/status`, `POST /telemetry/ingest`
- Session: `GET /sessions`, `POST /sessions`, `DELETE /sessions/{id}`
- Events: `GET /events`, `POST /events`

---

## 12. VW RAG

**Category:** Retrieval-Augmented Generation Engine  
**Status:** Production  
**Location:** `Modern_X64/tools/vw_rag/`

### Overview
Hybrid keyword + vector search engine providing codebase-aware context to AI agents. Uses SQLite FTS5 BM25 keyword search with optional sentence-transformers vector similarity (graceful fallback to keyword-only).

### Architecture
- **Chunker:** Code-aware chunking for .py, .cpp, .h, .hpp, .c, .js, .ts, .ps1, .bat, .java, .cs
- **Embedder:** Optional sentence-transformers integration
- **Indexer:** WAL-mode SQLite, batch inserts, per-file commits
- **Retriever:** Hybrid retrieval with reciprocal rank fusion (k=60) + recency boost

### Multi-Corpus Manager
7 separate corpus databases:
1. `vw_x64` — Engine source
2. `vw_tools` — Toolchain
3. `dqlosttranslation` — DQ4 translation project
4. `dq_cybergrime` — PSX emulator
5. `dq_translation_tools` — Translation tooling
6. `vw_x64_source` — Engine source (alternate)
7. `study_docs` — Study/analysis documents

### Index Stats
- 866 files, 46,700 chunks
- ~2 minute indexing time
- 15 auto-tagging categories (combat, dx11, npc, asset, audio, quest, save, build, toolchain, rigforge, engine, memory, ui, determinism, world_map)

### CLI
```powershell
python -m vw_rag index
python -m vw_rag query "Huffman tree decompression"
python -m vw_rag status
```

---

## 13. RigForge

**Category:** Auto-Rigging & Animation Pipeline  
**Status:** Production  
**Location:** `Modern_X64/tools/RigForge/`

### Overview
C++ CLI tool for automated skeletal rigging, shadow baking, lighting extraction, animation clip building, and export packaging. Designed for 2.5D isometric game characters with full 2.5D iso support.

### Commands
| Command | Description |
|---------|-------------|
| `analyze` | Analyze atlas for rigging candidates |
| `rig` | Auto-rig sprites with skeletal bones |
| `shadow` | Bake iso-aware shadow projections |
| `light` | Extract bent normals, thickness, SSS, iso lighting |
| `clips` | Build AnimaClip-matching animation clips |
| `export` | Package rig + clips + metadata |
| `batch` | Full pipeline batch processing |
| `validate` | Validate export package integrity |
| `preview` | Preview animation frames |

### 2.5D Iso Support
- `isoDepth` field in BoneDefinition, exported in rig.json
- CLI flags: `--iso`, `--2d`, `--iso-angle`, `--iso-tile-w/h`, `--iso-shadow-x/y`, `--iso-light-x/y/z`
- ShadowBaker: iso shadow angles
- LightExtractor: iso light direction
- Engine-side: `RigForgeModule::GetBoneDepthBias()`, `RigForgeModule::IsIsoProjection()`

### Engine Integration
- `RigForgeModule.h/.cpp` in `%DST%` directory
- Loads manifest.json, rig.json, clips.json from export dirs
- Registers AnimaClips with engine's `IAnimaActivation` via `RegisterClip()`
- Batch loading from `tools/RigForge/output` at engine startup
- Hooked into `GameLoop.cpp` init (after AnimaActivation) and shutdown

### Build
```powershell
cl /EHsc /O2 /std:c++17 /D_CRT_SECURE_NO_WARNINGS rigforge.cpp core/*.cpp
```
Output: `rigforge.exe` (855KB), 12 source files, 4 libs

---

## 14. VWForge

**Category:** Unified Production Toolchain CLI  
**Status:** Production  
**Location:** `Modern_X64/tools/vwforge/`

### Overview
Unified CLI v2.0 for all Void Walkers HD production tools. Every subcommand inherits from `AgentCommand` and supports `--agent`/`--json` flags for machine-friendly JSON output. Provides structured logging, exit-code contracts, and telemetry sinks for CI integration.

### Agent Contract Infrastructure
- **AgentParser:** argparse wrapper enforcing `--agent` / `--json` globally
- **AgentLogger:** Structured JSON lines to rotating log file
- **AgentReporter:** Standard exit codes (0=ok, 1=error, 2=validation_fail, 3=capacity)

### Commands
| Command | Description |
|---------|-------------|
| `build bestiary` | Generate `generated_mob_bindings.inc` from bestiary data |
| `build avatars --class <c>` | Compile avatar sprite atlases (monk/priest/thief/sailor/gambler) |
| `build npcs` | Generate NPC atlas bindings |
| `build quests` | Generate quest data |
| `sanitize --src <dir> --dst <dir>` | Auto background strip, normalize assets |
| `ingest-tiles --src <dir> --dst <dir>` | Ingest, validate, pack 512×512 RGBA tile PNGs |
| `validate assets` | Validate asset integrity against manifest |
| `shader compile [--watch]` | Compile HLSL shaders to CSO |
| `sfx generate --order <N>` | Generate procedural SFX from blueprint orders |
| `registry mint --scan <dir>` | Mint UUAs and generate C++ header |
| `pipeline run --stage <name>` | Run specific pipeline stage |
| `doctor` | Toolchain health check |
| `guardian_audit --reclaim` | System audit (disk, memory, VRAM) |
| `dedupe` | Asset deduplication |
| `cache` | Cache management |
| `streaming config` | Streaming configuration |

### Pipeline Stages
`bestiary` → `rigforge` → `avatars` → `npcs` → `quests` → `shaders` → `engine_build`

---

## 15. Atlas Forge

**Category:** Art Tool — Atlas Packing  
**Status:** Production  
**Location:** `Modern_X64/tools/atlas_forge/`

### Overview
Automated atlas packing tool that takes individual sprite/tile assets and packs them into optimized texture atlases. Works in concert with AtlasArchitect for the art pipeline.

### Surfaces
- **CLI:** `python atlas_forge.py`
- **MCP:** `atlas_forge.yaml`
- **Deck:** Icon 🔥, menu_id 189

---

## 16. Sprite Slicer

**Category:** Art Tool — Sprite Sheet Slicing  
**Status:** Production  
**Location:** `Modern_X64/tools/sprite_slicer/`

### Overview
Tool for slicing sprite sheets into individual frames. Handles regular grid slicing, irregular frame detection, and metadata export for animation systems.

### Surfaces
- **CLI:** `python sprite_slicer.py`
- **MCP:** `sprite_slicer.yaml`
- **Deck:** Icon 🔪, menu_id 199

---

## 17. Fidelity Forge

**Category:** AI Tool — Image Enhancement  
**Status:** Production  
**Location:** `Modern_X64/tools/fidelity_forge/`

### Overview
AI-powered image enhancement tool for upscaling and refining game assets. Integrates with the asset pipeline to produce high-fidelity versions of 16-bit sprites and textures.

### Surfaces
- **CLI:** `python fidelity_forge.py`
- **MCP:** `fidelity_forge.yaml`
- **Deck:** Icon ✨, menu_id 226

---

## 18. OpenVINO Forge

**Category:** AI Tool — Intel OpenVINO Integration  
**Status:** Production  
**Location:** `Modern_X64/tools/openvino_forge/`

### Overview
Intel OpenVINO-based inference tool for AI-assisted asset generation and processing. Provides optimized inference pipelines for model-based art generation.

### Surfaces
- **CLI:** `python openvino_forge.py`
- **MCP:** `openvino_forge.yaml`
- **Deck:** Icon 🧠, menu_id 235

---

## 19. Hermes Bridge

**Category:** Neural Link — AI Chat Middleware  
**Status:** Active Development  
**Location:** `Modern_X64/tools/liminal_lore_vw_deck/hermes-bridge.js`

### Overview
Node.js middleware service (122KB) that bridges the web portal to all backend services. Provides a unified API for AI chat, telemetry aggregation, and service management.

### Endpoints
- `GET /nexus/telemetry` — Aggregated health across all services (Nexus, TurboQuant, Heartbeat, Ollama, Committee)
- Neural link chat relay
- Service discovery and routing

### Port: 8643

---

## 20. TurboQuant

**Category:** LLM Memory Management  
**Status:** Production  
**Location:** `Modern_X64/tools/liminal_lore_vw_deck/turboquant-liminal.py`

### Overview
KV-cache scaling and memory pressure manager for local LLM inference. Monitors RAM usage and dynamically adjusts quantization to prevent OOM while maximizing model quality.

### Features
- RAM pressure monitoring (percentage + absolute)
- Modes: normal, scale, queue, halt
- KV-bits quantization control (2-bit, 4-bit, 8-bit)
- Safety thresholds with automatic mode switching

### Port: 8646

---

## 21. Master Pipeline A

**Category:** ISO Build Pipeline  
**Status:** Production  
**Location:** `DQLOSTTRANSLATION/master_pipeline_a.py`

### Overview
Standalone Python script that builds the complete Frankenstein disc via DW7 base disc + in-place sector overwrite. The most mature build pipeline, producing verified playable discs.

### Pipeline Steps
1. **Copy DW7 disc** as base (711 MB, no truncation)
2. **Patch EXE** — 17 MIPS patches (tree refs, BSS clear, HBD string, hybrid tree, decomp targets, disc check, folder pointers, trampoline, malloc clamp, text size)
3. **Re-encode DQ4 HBD** — 3,243 folders, 23,828 files, 1,527 blocks re-encoded, 3,148 sub-block swaps
4. **Write assets at correct LBAs** — EXE@24, HBD@354, SYSTEM.CNF@23 (MODE2 sector-aware, preserving 24-byte headers)
5. **Patch ISO9660 directory** — W71→Q41, update sizes/LBAs, PVD, license
6. **Write CUE file**
7. **Run EDCRE** — EDC/ECC regeneration on BIN file (154,406 sectors updated)

### Output
- Disc: `master_output/dq4_master_final.bin` (711,567,024 bytes, 302,537 sectors)
- CUE: `master_output/dq4_master_final.cue`
- EDC/ECC: ALL SECTORS VALID
- SHA-256: AE8380B2D523183CB89DB8814109E4E22E02256A77B974D44B8F841F001C5CBB

### Key Technical Details
- `SECTOR_SIZE=2352` (MODE2, not 2048)
- `write_sector_data` preserves sector headers, writes only user data at offset 24
- EDCRE operates on BIN file directly (not CUE)
- BSS clear uses pattern matching (not fixed offset)
- `text_size` at EXE header offset 0x1C (not RAM address)
- SYSTEM.CNF with CRLF line endings

---

## Summary

The VoidWalkers project encompasses two major product lines:

### DQ4 Frankenstein (PSX Translation)
- **DQ4 Frankenstein** — The translation ROM itself
- **DQ4 Pipeline CLI** — Build orchestration
- **VHB Super BIOS** — Clean-room PSX BIOS
- **CyberGrime** — PSX emulator and testing harness
- **PSXMatrix** — Automated 9-test matrix
- **Digital Twin** — Disc forensics and telemetry correlation
- **Master Pipeline A** — ISO build pipeline

### Void Walkers X64 (DX11 Game)
- **Void Walkers X64** — The game
- **IIRIS Game Engine** — Custom engine with multi-backend rendering
- **Liminal Lore** — Agentic dev suite with multi-agent orchestration
- **VW Nexus** — Agent orchestration server
- **VW RAG** — Codebase-aware retrieval engine
- **VWForge** — Unified production CLI
- **AtlasArchitect** — Map stitcher and asset refiner
- **Atlas Forge** — Atlas packing
- **Sprite Slicer** — Sprite sheet slicing
- **RigForge** — Auto-rigging and animation pipeline
- **Fidelity Forge** — AI image enhancement
- **OpenVINO Forge** — Intel OpenVINO inference
- **Hermes Bridge** — AI chat middleware
- **TurboQuant** — LLM memory management

**Total source files:** 3,286+ files, 171,877+ lines of code across both product lines.

https://facebook.com/LuxAuraOfficial
https://ix03y3.bandcamp.com
https://www.discogs.com/label/1081154-Lux-Aura
Built by luxauraofficial777 under the Mandate of Sound Logic.
One path. One truth. Evidence first.
