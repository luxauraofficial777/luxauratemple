# THE TRANSITION ERA
## JRPG Engine Architecture and Hardware Exploitation Across Six Studios, 1983–2006

**Doc ID:** VW-DEEP-WP-002 · Rev A · 2026-09-18
**Scope:** proprietary engine architectures, custom rendering, memory management, and hardware
exploitation across Nihon Falcom, tri-Ace/Wolf Team, Square, Sega, Capcom/Game Arts, and Atlus.
**Evidence hierarchy:** [VERIFIED] primary source or authoritative reverse-engineering; [STRONG]
multiple independent sources; [WEAK] single secondary source; [UNVERIFIED] claimed but unconfirmable;
[PRESS] developer interview; [HW] hardware datasheet; [RE] community reverse-engineering.
Bypass game design, narrative, and surface-level mechanics.

---

## 0. Method and honest boundaries

This paper is a **research-and-synthesis** document. Unlike the Nakamura–Yamana deep study
(VW-DEEP-WP-001), no ROM-level local measurement was possible — no cartridges or disc images
for any of these six studios are present in the project tree. All claims are therefore tiered
as documented/press/hardware/RE, with honest gaps where the public record is thin. A
measurement roadmap for each studio is offered in §8.

---

## 1. Nihon Falcom — Ys & the Trails Series

### 1.1 The PC-8801 scroll problem [HW + RE + PRESS]

**Corrected premise:** the PC-8801mkIISR does **not** contain a µPD7220 GDC — that chip
lives in the PC-9801 series. The PC-8801's graphics plane (640×200, 8 colors, ~48KB at
$C000–$FE7F) has **no hardware scrolling of any kind** for the bitmap layer. The text plane
can scroll via DMA channel 2 ($64/$65), but the graphics plane — where Ys lives — cannot
[VERIFIED: vraminfo.html; MAME pc8801.cpp].

The critical enabler was the PC-8801mkIISR's **ALU access mode** (I/O $32 bit6): a
read-modify-write path that accesses all three R/G/B planes simultaneously through an
internal 8-bit-wide buffer, enabling **VRAM-to-VRAM copy** without loading values into the
CPU register. This was a binary-write acceleration, not a scroll register — but it made
per-frame full-screen tile-shift operations feasible on the 4 MHz Z80 [VERIFIED: vraminfo;
highriskrevolution.com/通史1].

The SR's text-plane display no longer stalled the CPU (unlike the V1S/V1H, where text refresh
via DMAC froze the CPU). This mattered because the standard hiding trick for mid-frame scroll
redraws — masking the working area behind the text plane — was impossible on V1 without a
CPU stall [VERIFIED: vraminfo].

### 1.2 Ys I scroll implementation [STRONG → PRESS]

Hashimoto Masaya's "88用フルカラーの全画面スクロール" (full-color screen scroll for PC-88)
was prototyped ~November 1986, grown out of the Romancia development environment. The entire
Ys I game was built around this routine [VERIFIED: highriskrevolution通史5].

The map scroll is **entirely software**: per-frame partial redraw of tiles using the ALU for
plane operations, with the text plane serving as hidden scratch/masking area. The exact
per-pixel/per-8-pixel granularity of horizontal movement is **not documented** in accessible
primary sources; contemporary PC-88 scroll programs shipped in 8-dot vertical / 16-dot
horizontal steps, but Ys's execution pacing was explicitly praised as "滑らかな" (smooth)
[STRONG: nijiyume; WEAK on granularity].

Budget: 64KB user RAM, data loaded from two floppies. Map and scenario data shaped by the
constraint. Main-memory and VRAM budgets were the binding forces [STRONG: 通史1/通史5].

### 1.3 FM synthesis and driver architecture [VERIFIED + STRONG]

Hardware: YM2203 (OPN: 3ch FM + 3ch SSG) internal on mkIISR/TR; YM2608 (OPNA) from
8801FA/MA. Ys III on PC-98 supported YM2608 via the 26K board [VERIFIED: tk-nz.game.coocan.jp].

The sound driver lineage runs through MUCOM88 (Yuzo Koshiro's driver); tempo is tied to the
machine's interrupt/timer period — tempo shifts when ported to machines with different periods,
confirming an **interrupt-driven** architecture [STRONG: shmuplations Ys interview].

### 1.4 PC-88 → PC-9801 transition [VERIFIED]

Development moved to PC-9801VM (V30 CPU, 8086 codegen, 640KB RAM, MS-DOS). Assembly toolchain:
AS/LK assembler with ~30-second builds vs ~10-minute floppy CP/M on PC-88. Map editor built by
Kurata/Oketani in N88 assembler on the 98. Ys shipped on PC-88 (June 1987) ahead of PC-9801
(August 1987) and FM77AV (October 1987) [VERIFIED: 通史4/通史5].

### 1.5 The Trails (Kiseki) scripting engine [VERIFIED + STRONG]

**Engine identity:** the classical Sora-era (2004) engine has **no published official name**.
From Sen no Kiseki onward, Falcom used Sony's **PhyreEngine**. Starting with Kuro no Kiseki
(2021), the engine was rebuilt in-house as the **FDK (Falcom Developer Kit)**, with rendering
and sound reconstructed from scratch. The FDK was developed over ~3 years with a team of 5
(2 programmers, 1 planner, 2 designers) and is reusable across titles [VERIFIED: AUTOMATON
2025; Game Creators interview].

**Script VM:** event scene scripts are **bytecode functions→instructions**, each opcode
+ operands packed per instruction. Sora-era scripts are `._SN` per-map files inside
`ED6_DT01.dat`/`ED6_DT21.dat`; Zero/Ao scripts are `.bin` in `data/scena`. Community
decompilers (EDDecompiler, SenScriptsDecompiler, KuroTools) prove the format-driven
workflow [STRONG: GitHub repos].

**Text volume:** 空の軌跡SC = ~2.3MB main scenario + ~1MB quest + ~2MB town dialogue ≈
**5 MB+ of text**; FC = 3.3 MB. Per-flag NPC dialogue is an officially stated design pillar:
~200 named NPCs/chapter, per-flag lines for every state combination [VERIFIED: denfaminicogamer
2021 interview; Falcom column 2006].

---

## 2. tri-Ace / Wolf Team — Tales, Star Ocean, Valkyrie Profile

### 2.1 Wolf Team's audio-genetics [VERIFIED]

Founded 1986 as Telenet sphere, independent 1987. The core that later formed tri-Ace
(Yoshiharu Gotanda, Masaki Norimoto, Joe Asanuma, Motoi Sakuraba, etc.) left during a 1993
restructuring and founded tri-Ace in March 1995. Pre-Tales DNA: PC-98 MIDI-savvy sound
(Granada 1990, Roland MT-32 patches) plus a 1991 corporate pivot to CD-ROM/Mega-CD
(laserdisc FMV ports: Time Gal, Ninja Hayate) — a team already obsessed with cramming
CD-scale audio into constrained storage [VERIFIED: shmuplations Wolf Team interview].

### 2.2 Tales of Phantasia — the Flexible Voice Driver [VERIFIED + STRONG]

**Cartridge:** 48 Megabit — tied with Star Ocean as the largest SFC cartridge ever produced.
Price: ¥11,800 at launch. 16 Megabits allocated to sound and voice data [VERIFIED:
arcade-history.com; VGMO review].

**The SPC700 streaming problem:** The S-SMP/S-DSP subsystem has 64 KB SRAM shared between
the 8-bit SPC700 processor and the 16-bit DSP. BRR (Bit Rate Reduction) samples at 32 kHz
mono occupy 9 bytes per 16-sample frame → **~3.6 seconds of mono audio** fits in 64 KB
[VERIFIED: ipfs/SNES SPC700 wiki]. A multi-minute song with vocals cannot fit.

Wolf Team's solution — the **FVD (Flexible Voice Driver)** — streams BRR audio blocks from
ROM through the 65816 CPU via the APU communication ports ($2140–$2143, no DMA channel to
the APU exists) in real-time while the sequenced instrumental plays. The 65816 polls/feeds
the direction registers at a fixed cadence, pumping compressed blocks into the SPC700's
input buffer while its own driver processes the sequenced backing track
[STRONG: spc700-sound-format wikibin; nesdev forums].

The practical consequence: SPC file dumps cannot capture the streaming vocals — "the vocal
parts were heavily downsampled" (VGMO); instrumentals remain sequenced. The OPFG review
notes "16-MEGS just for sound and voice" [STRONG: topping.zophar.net/vop; VGMO].

### 2.3 Star Ocean (SFC) — S-DD1 and the 48-Megabit ceiling [VERIFIED]

**Cart:** 48 Mb, S-DD1 chip (lossless arithmetic + Golomb coding, "ABS Lossless Entropy
Algorithm"), PCB SHVC-LN3B-01, 64 KB SRAM battery save, LoROM/FastROM 120 ns. The S-DD1
compressed "almost all graphics and map data." neviksti's no-S-DD1 hack yields a
**96 Mb (12 MB) uncompressed ROM** — proving an effective ~2:1 lossless ratio. The S-DD1
was later reverse-engineered into emulators (bsnes sdd1.cpp) [VERIFIED: snescentral.com;
superfamicom.org].

Field Programmer: Hiroya Hatsushiba (the Wolf Team audio programmer who built the same voice
infrastructure reused in ToP). Total Programmer: Gotanda [STRONG: superfamicom.org credits].

Mode 7 usage: software-rendered only (the SFC has no hardware sprite scaler; CPU stretches
sprites into WRAM then uploads to VRAM for treasure pop-out effects; limited by extra tile
constraints) [STRONG: nlaha wiki].

### 2.4 PS1 — GTE, particles, and the 60 fps question [VERIFIED + WEAK]

GTE (COP2): fixed-point matrix/vector ops for transform+light+projection. GPU: 2D rasterizer
with 1024×512 band limits, Z/OT ordering, no z-buffer. Hardware sprite rotation via GPU
"screen-to-screen" blits [VERIFIED: PSY-Q docs; pikuma.com].

Valkyrie Profile's 2D side-scroll was explicitly chosen for the PS1's "limited power"
(Famitsu 10/8/99) — framed as both aesthetic and hardware decision [VERIFIED: RPGFan
interview].

The "60 fps combat" claim for VP and SO2 is a **forum anecdote only** — no authoritative
emulator measurement, manual, or developer statement confirms it. **UNVERIFIED** [WEAK:
ngemu.com forum].

tri-Ace today continues self-built engines (ASKA; Gotanda still actively coding; R&D ~10
people; engine hooked into Maya) [VERIFIED: cgworld.jp 2020 interview].

---

## 3. Square — Final Fantasy IV through XII

### 3.1 The 16-bit scaling strategy [VERIFIED]

| Title | Size | Mapper | SRAM | Notes |
|---|---|---|---|---|
| FF IV (1991) | **8 Mb** | LoROM, SlowROM | 64 KB |  |
| FF V (1992) | **16 Mb** | HiROM, SlowROM | 64 KB |  |
| FF VI (1994) | **24 Mb** | HiROM, FastROM 120ns | 64 KB | 3 MiB — SNES capacity ceiling |

[VERIFIED: superfamicom.org]

FF VI made "more extensive use of Mode 7 than its predecessors" — the world map is Mode 7.
Battle frame Mode 7 is **unverified** [VERIFIED: Wikipedia citing developer statements].

Engine split: field/map module + battle module; battle itself split into mechanics and
graphics. Graphics use SNES-native **4bpp planar format** with compressed `.lz` files [STRONG:
github.com/everything8215/ff6 disassembly].

### 3.2 FF VII–IX — the SGI pipeline [VERIFIED]

Square invested **~$21 million** in SGI workstations and software: **SGI Onyx** supercomputers
for rendering, with **Softimage|3D** for modeling/animation, **PowerAnimator** (Alias) for
additional tools, and **N-World**. Both Softimage and Alias were used — the "Softimage vs
Alias" question is a false dichotomy [VERIFIED: Wikipedia FFVII].

FFVII's test battle (Terra, Locke, Shadow in 3D) on SGI influenced the move to PlayStation.
The pipeline: 3D models animated in Softimage on SGI → rendered as CG for FMV (Visual Works
in-house) + pre-rendered field backgrounds baked as static 16-bit images loaded into PS1 VRAM.

**FMV codec:** Sony MDEC (Motion Decoder) hardware, the "BS v2/v3/v3dc" codec family,
interleaved XA-ADPCM audio, typical 320×240 @ 15 fps in 2336-byte CD sectors [STRONG:
github.com/WonderfulToolchain/psxavenc].

**BGM: software-sequenced on the SPU** (24-channel ADPCM), NOT CD/XA audio [STRONG:
MusicBrainz; sound programmer credits].

**Field engine:** bytecode scripts (opcodes 00=RET, 01=REQ, 02=REQSW, 24=WAIT, 5F=NOP…),
LGP archives with LZ decompression, pre-rendered backgrounds composited with real-time
3D character polygons [STRONG: wiki.ffrtt.ru].

**PS1 constraints:** 2 MB main RAM, 1 MB VRAM, R3000A @ 33.8688 MHz, no z-buffer [VERIFIED].

### 3.3 FF X — the VU skinning question [VERIFIED + UNVERIFIED]

**Hardware:** Emotion Engine @ 294.912 MHz (MIPS R5900), 32 MB RDRAM, Graphics Synthesizer
with 4 MB eDRAM @ 147.456 MHz, ~2.4 Gpixel/s fill [VERIFIED].

FF X was "the last one where planners could directly edit data by hand" — script data already
used pipeline automation since FFVII. The game moved to full 3D environments with free
camera [STRONG: Famitsu 2021 interview with Kitase's team].

The **"MILLION" VU1 microcode for bone-matrix-palette skinning and facial morph-transfer** —
the most cited technical claim about FFX — has **zero public sources** located (English or
Japanese). The specific mechanism remains **UNVERIFIED** [HONEST GAP].

### 3.4 FF XII — seamless transitions and streaming [VERIFIED]

Developer-confirmed: seamless field↔battle transitions with real-time command input [VERIFIED:
PlayStation Blog 2017].

"Graphics resources were cut to roughly half of the original plan to lighten the load for
seamless battle" (Battle Ultimania, p.78) [VERIFIED: ja.wikipedia.org citing Ultimania].

The field is a full 360° polygon+texture 3D world. Interior regions are **separate field maps
reachable via the world map** — "zone-free seamless open world" is **not supported** by
sources; it is area-based with seamless battles inside areas [VERIFIED: ja.wikipedia.org].

Frame rate: **~30 fps on original PS2** (PCSX2 testers note); 60 fps appears only in The
Zodiac Age remaster [VERIFIED: PCSX2 forums; Digital Foundry].

Streaming internals (double-buffer DVD reads, block-group tables, LOD scheduling, resident
memory layout): **no public source found**. All secondary claims are unsubstantiated
[HONEST GAP].

---

## 4. Sega — Phantasy Star (8/16-bit) and Phantasy Star Online

### 4.1 PS I dungeons — the RLE frame-swap [VERIFIED: RE]

**Correction of premise:** the SMS Z80 runs at **3.579545 MHz** (NEC 780C clone), not 2 MHz.
The VDP is planar 8×8 tiles, 4bpp, two 16-color palettes.

From the disassembly (Maxim, SMS Power 2020): every walk/turn step is a **precomputed "frame"
of data**. Per frame the Z80 **RLE-decompresses tile patterns** into the upper or lower 8KB
of VRAM and the tilemap (name table) into RAM, then waits for VBlank to display. Walls at
different depths are **pre-scaled column strips baked into tiles** and streamed, not resampled
per-column by the CPU at runtime. Frame rate is set by decompression time (turning costs more
than walking → inconsistent FPS) [VERIFIED: smspower.org forums; ps1disasm on GitHub].

Original intent: a "wireframe 3D dungeon computed in the program itself" with art laid over.
The "draw-all-frames" approach consumed ~3 MB of the 4-Mbit cart; RLE on-the-fly was added
to fit, which **unintentionally** slowed rotation 4–5× and cured motion sickness
[STRONG: smspower.org interview translation].

### 4.2 PS II/IV — the 3D dungeons were dropped [VERIFIED]

**PS II (1989):** dungeons are **top-down**, not 3D. Naka (2017 interview): wanted 3D but
the 4→6 Mbit cart and ~2.5-month crunch made it impossible [VERIFIED: sega-16.com].

**PS IV (1993):** Kodama confirmed: the MD VDP has **zero scaling/blend hardware**; MD 3D
could not match SMS tech without full floor/ceiling rotation; memory costs killed it.
PS IV **drops first-person dungeons entirely** (top-down, like II). The "pseudo-3D" people
remember is parallax-scaled large backgrounds used to give towers an illusion of height on
otherwise flat maps [STRONG: shmuplations PSIV interview; tcrf.net].

### 4.3 Phantasy Star Online — the network architecture [VERIFIED: PRIMARY]

**Hybrid: client-server (lobby) + peer-to-peer (in-party).** Setsumasa (program director):
"PSOのネットワークシステムは、クライアント・サーバ型とPeer to Peerの複合型"
[VERIFIED: ascii.jp 2002 interview].

**Transport split:** movement ≈ 80% of all traffic, sent over **UDP** (loss-tolerant); chat,
Simple Mail, item transfers on **TCP/IP** through the server (reliable)
[VERIFIED: Setsumasa 2002].

**Latency budget:** designed for **300–600 ms** Japan↔Europe links. Full lockstep sync was
abandoned up-front [VERIFIED: 4Gamer 2010].

**Movement quantization:** at the instant a direction is pressed the destination is fixed; a
movement packet to the decided coordinates is sent and the client processes **6 frames at
30 fps at once** — movement packets fire at most every ~200 ms → effectively ~5 Hz
worst-case. "No jumping" was a network-engine decision [VERIFIED: 4Gamer 2010;
polygon.com interviews].

**Enemy sync:** regular enemy positions are **NOT synchronized** between clients (each player
sees them elsewhere; only death/damage events shared). **Bosses are:** leader client runs boss
AI, picks an action from 10+ second behavior patterns, broadcasts the choice; recipients run
local AI + filler actions to mask desync [VERIFIED: 4Gamer 2010; bumped.org].

**Party-leader authority:** only the party leader authorizes drops. A leader that disconnects
mid-boss wipes the drops — a well-known lag/desync artifact [VERIFIED: sylverant.net wiki].

**Wire protocol:** typed, length-prefixed commands (0x60 = game-command broadcast, 0x62 =
peer-game-command, 0x6C/6D on v3). Subcommand 0x60 = "request drop from party leader";
leader replies 0x5F picking the item; 0x5A = request to pick up. [VERIFIED: sylverant.net;
newserv source].

**Dreamcast hardware:** SH-4 200 MHz, 16 MB SDRAM, PowerVR2. 33.6k modem (EU/JP early) or
56k V.90 (NA, later JP). PPP dial-up; DC included full TCP/IP stack + browser (Dream
Passport/PlanetWeb) in firmware [VERIFIED: Wikipedia; handwiki.org].

**No documented engine reuse** from Sonic Adventure ("LANTERN" engine) — team/experience
transfer only, not code inheritance [WEAK: polygon.com interviews].

---

## 5. Capcom & Game Arts — Breath of Fire and Lunar

### 5.1 Breath of Fire III/IV — isometric billboarded 3D [STRONG]

Both BoF III (1997) and BoF IV (2000) use **billboarding**: character sprites are 2D images
facing the camera, placed in 3D environments. Giant Bomb's wiki explicitly lists both titles
as canonical billboarding examples [VERIFIED: giantbomb.com/wiki/Concepts/Billboarding].

**BoF IV specifics:** the camera can rotate 360° around the player on the world map; sprites
render as unskewed "perfect rectangles" regardless of camera angle — separate view/projection
applied to the billboard position only, sprite quad kept axis-aligned. The 3D environment
(terrain, water) is polygon; characters/enemies are 2D sprite billboards. Cannot tilt the
camera (unlike BoF III, which supports limited tilt) [STRONG: MonoGame community thread;
Wikipedia BoFIV].

**Capcom Development Studio 3**: same director (Ikehara Makoto), same artist (Yoshikawa
Tatsuya) for both titles. PC port by SourceNext (May 2003 JP) added a 2D sprite smoothing
filter [VERIFIED: Wikipedia].

**Honest gap:** no Capcom primary source describes the billboard math implementation, the
polygon count of the terrain meshes, or the sprite-batching pipeline. The claim is
corroborated only by community RE observation and gameplay video analysis.

### 5.2 Lunar — Sega CD/Saturn FMV [STRONG + VERIFIED]

**Lunar: Silver Star Story Saturn:** FMV files live in the **CPK folder**; the MPEG version
uses a custom Sega MPEG streaming library with hardcoded mux parameters — video ~1.1 Mbps
CBR MPEG elementary stream, audio 192 kbps / 44.1 kHz dual-mono MPEG Layer II, resolution
320×224 (not the card's 704×480 max), sectors in Mode 2. The custom library is the reason
extracted .MPG files appear "corrupted" — headers/mux must match [STRONG: segaxtreme.net
translation thread].

**Saturn audio buffering:** Lunar SSS audio is 8-bit PCM, 16 kHz mono, split to fit ~512 KB
work RAM. Alex's 28-second intro narration stored as 432 KB + 32 KB files — oversized clips
crash/fail to load. Every audio clip must fit alongside the sound driver's own resident
footprint [STRONG: segaxtreme.net].

**PS1 audio:** 8-bit PCM at 37.8 kHz (2.4× the Saturn bitrate) — the reason NA VO ran out
of RAM on PS1 [STRONG: segaxtreme.net].

**Honest gap:** no public Game Arts source describes the Sega CD Lunar's video data layout,
the exact Delta-frame / palette-swap animation technique used for the cutscenes, or the
interrupt-driven update cadence. The Mega-CD's 68000 + VDP dual-CPU FMV pipeline is
well-documented in hardware specs but the Lunar-specific implementation is not.

---

## 6. Atlus — Persona UI and Transition Pipelines

### 6.1 Engine lineage [VERIFIED + STRONG]

**PS2 era:** one shared unnamed in-house engine across SMT III: Nocturne, Digital Devil Saga,
Persona 3, Persona 4. Reverse-engineering shows a Criterion RenderWare-derived asset layer
(RMD models, TXD/TMX textures, SPR sprite containers, packed in CRI .CVM archives)
[STRONG: amicitia.miraheze.org; shrinefox guide]. **Official internal name: undocumented.**

**Catherine (2011):** used Gamebryo — NOT the P5 engine [VERIFIED: ja.wikipedia.org].

**Persona 5 (2016):** new "GFD" engine, GNM API on PS4, DX11 on PC (P5R). Started post-Catherine
(2011), finished August 2011; shaders and event-scene creator in-house [VERIFIED: CGWORLD
Vol.218; personacentral.com]. The abbreviation GFD is **never decoded by Atlus**
[STRONG: opengfd GitHub; pcgamingwiki].

### 6.2 The UI render system [VERIFIED]

**Separate screen-space pass:** DF's P5R console table shows UI resolution independent of 3D
resolution — PS4 Pro: 3D at 2160p, UI stays at 1080p; Switch: 3D at 810p/540p, UI at
1080p/720p [VERIFIED: Digital Foundry].

**Assets are textures, not procedural vector shapes.** Sutoh (first dedicated Atlus UI designer,
Nocturne→P5→Catherine) confirmed: Photoshop layout → motion-designer pose → custom tool
renders the menu's 3D model to a 2D illustration/texture cut; elements "spread like Tetris"
for memory packing. Toolchain unchanged since ~1999 (Photoshop, Illustrator, After Effects)
[VERIFIED: CEDEC+KYUSHU 2017; personacentral.com; siliconera].

**Animations:** data-driven transforms on per-part UI elements; designer-specified motion data
implemented frame-by-frame by dedicated UI programmers; spec papers of >1,000 pages handed
designers to programmers. Not per-frame mesh deformation in the renderer sense
[VERIFIED: CEDEC+KYUSHU 2017].

### 6.3 Correcting the "vector-based GUI" premise [VERIFIED]

**Nothing in the documented record says the Atlus UI is a vector-based GUI overlay system.**
The "vector" label comes from visual-style taxonomy (Game UI Database tags P5 "Art & Vector"
— a design look) and from fan recreations (CodePen/Unity), not from Atlus. The only shader
work Atlus published for P5 is the in-house manga-style 3D shader for world/characters, not
the UI [VERIFIED: gameuidatabase.com; CEDEC+KYUSHU 2017].

Two caveats where the claim is *partly right*: (a) skewed/rotated cards imply **transformed
textured quads** (vertex transforms, "vector-ish"); (b) PS2-era GS UI is necessarily primitive
draw calls with CPU-computed geometry per frame (fixed-function GS, no shaders). Both are
architectural inferences, **not Atlus statements**.

### 6.4 Transitions and memory [VERIFIED + UNVERIFIED]

**Pre-rendered vs real-time split:** anime cutscenes are CRI Sofdec (USM) video, produced by
Production I.G/Domerica. The warp/"storm" menu-to-menu transitions are **real-time composited
over the live frame** (show live 3D behind, resolution-independent) — but the exact technique
(shader curves vs textured-quad storm) is **UNVERIFIED**.

**Zero-latency menu:** Sutoh confirmed menu "pops up without any lag whatsoever" — GUI data
kept resident in memory [VERIFIED: personacentral UI interview].

**Memory figures:** none published by Atlus. **Text layer cache:** nothing documented.
**60fps UI:** likely false on PS3/PS4 (DF measured 30fps gameplay); only true for P5R
current-gen/PC at 60fps [VERIFIED: Digital Foundry].

---

## 7. Cross-studio synthesis

### 7.1 The recurring engineering patterns

| Pattern | Studios employing it | Constraint |
|---|---|---|
| **Software scroll via ALU/block-copy** | Falcom (PC-88), Phantasy Star (SMS) | No HW scroll on target VDP |
| **Audio streaming through CPU→APU ports** | Wolf Team/Tales (SPC700) | 64 KB APU SRAM |
| **Lossless ROM compression** | Star Ocean S-DD1, DQ LZSS, FF .lz | Cartridge byte budgets |
| **Billboarded 2D-in-3D** | Capcom BoF IV, tri-Ace VP | PS1 fill-rate / sprite limits |
| **Precomputed frame-swap (pseudo-3D)** | Phantasy Star I (SMS) | No 3D HW; fit 4 Mbit |
| **Pre-rendered backgrounds + 3D chars** | Square FF VII–IX (PS1) | 2 MB RAM, 1 MB VRAM |
| **Vector-unit skinning** | Square FF X (PS2 VU1) | 32 MB RDRAM |
| **Predictive client-server with UDP** | Sega PSO (DC) | 33.6k/56k modem, 300–600 ms |
| **Texture-atlas UI with resident memory** | Atlus P5 (PS4 GNM) | VRAM budget, zero-latency UX |
| **Hybrid 2D/3D with stock-market packing** | Atlus P5 textures "like Tetris" | Tiny color maps (256×256) |

### 7.2 The "no hardware X → fake it in software" doctrine

Every studio in this study confronted at least one missing hardware feature and solved it in
software at the cost of CPU time or development complexity:

- **Falcom:** no bitmap scroll → ALU plane copy + text-plane masking
- **Sega PS1:** no 3D HW → precomputed RLE frame-swap
- **Wolf Team:** no DMA to APU → CPU-polled block streaming over $2140–$2143
- **Square:** no skeletal animation HW on PS1 → CPU-driven vertex skinning under 2 MB RAM
- **Capcom:** no real-time 3D sprites → billboarded quads on GPU

### 7.3 Memory as the universal tax

| Platform | RAM | VRAM | Studios' responses |
|---|---|---|---|
| PC-8801 | 64 KB user | ~48 KB GVRAM | Falcom: text-plane-as-scratch, ROM-based streaming from floppies |
| SMS | 8 KB | 16 KB VRAM | PS1: RLE frame precompute |
| SFC | 128 KB WRAM | 64 KB VRAM | Wolf Team/Square: SPC700 streaming, S-DD1 compression, .lz packs |
| PS1 | 2 MB | 1 MB | Square: pre-rendered BGs; tri-Ace: sprite-based combat |
| PS2 | 32 MB | 4 MB eDRAM | Square: VU microcode; Atlus: tiny texture budgets |
| Dreamcast | 16 MB | — | Sega/SonicTeam: UDP predictive sync, 5 Hz movement packets |

### 7.4 The honest measurement frontier

The following claims remain UNVERIFIED or WEAK across all six studios:

| Claim | Status | What would settle it |
|---|---|---|
| Ys I horizontal scroll exact pixel-granularity | WEAK | PC-8801 ROM disassembly or Falcom internal docs |
| ToP FVD block-pump timing (port polling rate) | WEAK | SFC SPC700 loop trace or Wolf Team source |
| VP/SO2 60 fps battle framerate | UNVERIFIED | Emulator frame-counter measurement |
| FF X MILLION VU1 microcode skinning | UNVERIFIED | Square/Enix CEDEC talk or leaked source |
| FF XII DVD streaming internals | UNVERIFIED | Square/Enix CEDEC talk |
| PSO exact DC packet-rate Hz | WEAK | Packet capture from DC serial link |
| Atlus P5 GFD engine render pass internals | UNVERIFIED | GDC/CEDEC talk by Atlus engineers |
| BoF IV billboard transform math | WEAK | Capcom internal doc or ROM RE |
| Lunar Sega CD FMV codec details | WEAK | Game Arts internal doc or full RE |

---

## 8. Honest gaps and measurement roadmap

### 8.1 What is NOT publicly documented

1. **Falcom:** exact PC-88 scroll pixel-step size; Sora-era engine module boundaries;
   FM driver IRQ timer internals; Kiseki text-rendering memory budget on PSP (32 MB).
2. **Wolf Team/tri-Ace:** ToP voice sample rate / bit depth / codec variant (only
   "heavily downsampled"); exact FVD block-pump protocol; VP particle system architecture.
3. **Square:** FF X MILLION VU1 microcode details; FF XII streaming block-group table;
   FF VI compression LZ-variant specifics.
4. **Sega:** PSO DC wire-format byte-level documentation (newserv admits DC support was
   "never tested with the Dreamcast versions"); PSO engine internals (model formats, PVRTex
   pipeline).
5. **Capcom/Game Arts:** BoF IV billboard math; Lunar Sega CD FMV frame layout; Lunar
   Saturn memory-management interrupt cadence.
6. **Atlus:** PS2-era engine name; GFD abbreviation meaning; render pipeline internals;
   memory budget for UI.

### 8.2 Measurement opportunities

Any future effort to convert WEAK/UNVERIFIED claims to VERIFIED would require:
- **PC-8801 disassembly** of Ys I scroll routine (the MAME source for pc8801.cpp provides
  the hardware model; a game-level ROM trace would answer the pixel-step question).
- **SFC SPC700 execution trace** of ToP during the opening theme (set breakpoints on
  $2140–$2143 writes from the 65816 side → measure block-pump cadence).
- **PS1 GTE profiler** for VP/SO2 battles (frame-time instrumentation in an emulator).
- **PS2 VU1 microcode dump** for FF X (read from EE memory at runtime → disassemble the
  MILLION program).
- **PSO DC packet capture** via serial link tap (the Dreamcast has an exposed serial header;
   a logic analyzer on the modem link would produce exact packet timing).

---

## Appendix A — Source registry (key URLs)

- [vraminfo] mydocuments.g2.xrea.com/html/p8/vraminfo.html — PC-8801 VRAM/ALU reference
- [MAME pc8801.cpp] github.com/mamedev/mame — src/mame/nec/pc8801.cpp
- [highriskrevolution] highriskrevolution.com/wp/gamelife/ — 通史1–5 Falcom history
- [nijiyume ys3] www14.big.or.jp/~nijiyume/fal002/ — Ys scroll commentary
- [shmuplations Ys] shmuplations.com/ys/
- [AUTOMATON 2025] automaton-media.com — Kiseki 2025 interview
- [vgmonline] vgmonline.net/talesphantasiabox/
- [snescentral] snescentral.com — S-DD1 article
- [superfamicom.org] superfamicom.org — FF IV/V/VI/SO headers
- [smspower PS1] smspower.org/forums/17360 — PS1 dungeon disassembly
- [4Gamer PSO 2010] 4gamer.net — CEDEC PSO/PSU network architecture
- [ascii.jp PSO 2002] ascii.jp — Setsumasa interview
- [sylverant.net] sylverant.net/wiki — PSO packet documentation
- [wiki.ffrtt.ru] wiki.ffrtt.ru — FF7 field script opcodes
- [psxavenc] github.com/WonderfulToolchain/psxavenc — PS1 MDEC codec
- [Digital Foundry P5R] digitalfoundry.net — P5R resolution analysis
- [personacentral CEDEC] personacentral.com — P5 UI workflow
- [segaxtreme Lunar] segaxtreme.net — Lunar SSS Saturn translation thread
- [giantbomb billboarding] giantbomb.com/wiki/Concepts/Billboarding
- [cgworld tri-Ace 2020] cgworld.jp — tri-Ace engine interview
- [polygon PSO] polygon.com — PSO developer interviews (2020)
