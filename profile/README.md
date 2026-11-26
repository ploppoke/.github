# ploppp
Programmers Larping Occassional Pokémon Patch Programming

We build experimental Game Boy Advance Pokémon decompilation features, modern compiler toolchains, source-level mod runners, and ROM inspection tools—centered around [`pokeemerald-fufufu`](https://github.com/ploppoke/pokeemerald-fufufu), our fork of `pokeemerald-expansion`.

## Quick map

| If you want to… | Go here |
|---|---|
| Play with the game base, Pokefish battle AI, or build a ROM | [`pokeemerald-fufufu`](https://github.com/ploppoke/pokeemerald-fufufu) |
| Build a GBA ROM with modern Clang and optional code/text obfuscation | [`gba-omvll`](https://github.com/ploppoke/gba-omvll) & [`OBFUSCATION.md`](https://github.com/ploppoke/pokeemerald-fufufu/blob/main/OBFUSCATION.md) |
| Apply clean source-code mods and rebuild instead of binary-patching `.gba` files | [`PatchouliKnowledgeAdvance`](https://github.com/ploppoke/PatchouliKnowledgeAdvance) |
| Read Pokémon dialogue, symbol tables, or diffs out of a compiled `.gba` ROM | [`gba-tools`](https://github.com/ploppoke/gba-tools) |
| Check which compiler, expansion version, or tools produced a `.gba` ROM | [`gba-fingerprint`](https://github.com/ploppoke/gba-fingerprint) |

---

## Projects

### [`pokeemerald-fufufu`](https://github.com/ploppoke/pokeemerald-fufufu) — The game base, Pokefish AI, and Clang/O-MVLL support
Our main Game Boy Advance Pokémon Emerald decompilation fork, built on top of [`rh-hideout/pokeemerald-expansion`](https://github.com/rh-hideout/pokeemerald-expansion). Alongside standard GCC builds, it includes:

- **Pokefish & NNUE-style battle evaluation (`AI_DISTILL_STUDENT=1`)**: Traditional Pokémon AI scores one move at a time with giant lists of hand-written if/else rules and often peeks at the player's hidden party data. Pokefish works more like a modern chess engine (such as Stockfish's compact **NNUE** value evaluator): it uses a small, fast integer value model trained offline to judge whole-team matchups, knockout thresholds, speed control, hazards, and pivot paths inside the GBA's tight CPU budget. It never cheats by reading unrevealed data from memory—instead, it tracks only what you have actually shown in battle, samples plausible sets for your unrevealed moves or items, and solves each turn as a simultaneous-move game (weighing your attacks and switches against its own) using a resumable search that ticks within hardware frame limits.
- **Crony Intel (`AI_INTEL=1`) & Opening Books (`AI_BOOK=1`)**: Gym trainers and route "cronies" remember what you revealed to them and pass that intelligence up the chain to their boss—so a gym leader like Norman can adjust his lineup and strategy around the specific Pokémon, moves, items, and abilities his trainers actually saw you use—paired with offline-generated opening books for smart leads and early-turn plans.
- **Modern Clang & compile-time obfuscation (`USE_CLANG=1`, `OBFUSCATE=1`)**: Optional support for building the ROM with a self-hosted ARMv4T Clang + `picolibc` toolchain and passing selected files through O-MVLL at compile time to scramble control flow, constants, and strings (documented in [`OBFUSCATION.md`](https://github.com/ploppoke/pokeemerald-fufufu/blob/main/OBFUSCATION.md)).
- **Engine, portability, and test fixes**: Fixes five upstream battle engine bugs (status-form triggers, doubles AI move fallbacks, redirected Sky Drop targets, turn-order array bounds, and animation RNG isolation so hit splats don't shift battle luck), makes ARMv4T/Thumb-1 assembly and script jumps portable across GCC and Clang without pulling in bulky unwinder libraries, and fixes CI/test edge cases across the 5,465-test Hydra suite.

### [`gba-omvll`](https://github.com/ploppoke/gba-omvll) — Self-hosted Clang + O-MVLL toolchain for the GBA
Most GBA projects rely on prebuilt GCC toolchains like devkitARM. `gba-omvll` provides the build scripts, configuration (`gba_omvll.py`), and bring-up guide to build a complete, modern LLVM/Clang cross-compiler from source for the GBA's 16.78 MHz ARM7TDMI (`armv4t` / Thumb-1) processor, paired with `compiler-rt` builtins, `picolibc`, and the O-MVLL obfuscation pass plugin.

### [`o-mvll`](https://github.com/ploppoke/o-mvll) & [`picolibc`](https://github.com/ploppoke/picolibc) — Bare-metal GBA obfuscator & C library forks
O-MVLL is an LLVM compiler plugin originally designed to protect Android and iOS apps. Because a Game Boy Advance has no operating system and runs 16-bit Thumb-1 instructions, standard mobile obfuscation passes crash or fail to assemble out of the box. Our [`o-mvll`](https://github.com/ploppoke/o-mvll) fork adapts the passes for 32-bit ARMv4T bare metal and teaches the string encoder to locally encrypt not just standard C strings, but also Pokémon's custom `0xFF`-terminated `COMPOUND_STRING` dialogue arrays in selected source files—decoding them on the stack right before use so plaintext strings disappear from the ROM image. [`picolibc`](https://github.com/ploppoke/picolibc) provides the lightweight bare-metal C standard library.

### [`gba-tools`](https://github.com/ploppoke/gba-tools) — Charmap-aware ROM & ELF inspection toolkit
If you run the standard Unix `strings` command on a Pokémon ROM, almost all in-game dialogue is invisible—because Pokémon games don't use ASCII text; they use a custom byte table (`charmap.txt`) where letters have their own byte values and strings end with `0xFF` instead of `0x00`. `gba-tools` is a dependency-free CLI suite (`gba-strings`, `gba-text`, `gba-sym`, `gba-table`, `gba-ref`, `gba-diff`, `gba-info`, `gba-patch`) that speaks Pokémon's text encoding natively, dumps and verifies strings and pointer tables from `.gba` ROMs and `.elf` builds, and proves whether text was left in plaintext or encoded in a build.

### [`gba-fingerprint`](https://github.com/ploppoke/gba-fingerprint) — ROM compiler & codebase fingerprinting
When you only have a compiled `.gba` file, it can be hard to tell what base or tools created it. `gba-fingerprint` scans and compares ROMs and ELFs (without modifying them) to identify the compiler that built them (1999 Nintendo AGBCC, devkitARM GCC, Wonderful GCC, or Clang), match them against specific `pokeemerald-expansion` releases, and report shared data tables and structural layouts.

### [`PatchouliKnowledgeAdvance`](https://github.com/ploppoke/PatchouliKnowledgeAdvance) — Source-level "decomp-recomp" mod runner
Traditional ROM hacks patch raw bytes inside a finished `.gba` binary, which breaks easily when things move around in memory. `PatchouliKnowledgeAdvance` (PKA) takes a "decomp-recomp" approach: a small JSON manifest describes clean source and asset edits on top of `pokeemerald-fufufu`, PKA applies them in an isolated worktree, compiles a fresh ROM with Clang, and verifies the result in a headless emulator (demonstrated with the Scarlet Mansion / Cirno scenario).

---

## AI policy
Sure, see `AGENTS.md`.

## Community
Never should've come here

[![Our Discord](https://img.shields.io/badge/MiSTfits-Discord-%235865F2.svg)](https://discord.gg/5BUJ3VT8f)
