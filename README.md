# plappp
Programmers Larping Actual Pokémon Patch Programming

This repository holds the organization profile rendered on **[github.com/ploppoke](https://github.com/ploppoke)** (see [`profile/README.md`](profile/README.md)).

## Quick map

| If you want to… | Go here |
|---|---|
| Play with the game base, Pokefish battle AI, or build a ROM | [`pokeemerald-fufufu`](https://github.com/ploppoke/pokeemerald-fufufu) |
| Build a GBA ROM with modern Clang and optional code/text/data-table obfuscation | [`gba-omvll`](https://github.com/ploppoke/gba-omvll) & [`OBFUSCATION.md`](https://github.com/ploppoke/pokeemerald-fufufu/blob/main/OBFUSCATION.md) |
| Apply clean source-code mods and rebuild instead of binary-patching `.gba` files | [`PatchouliKnowledgeAdvance`](https://github.com/ploppoke/PatchouliKnowledgeAdvance) |
| Read Pokémon dialogue, symbol tables, or diffs out of a compiled `.gba` ROM | [`gba-tools`](https://github.com/ploppoke/gba-tools) |
| Check which compiler, expansion version, or tools produced a `.gba` ROM | [`gba-fingerprint`](https://github.com/ploppoke/gba-fingerprint) |

## Projects at a glance

- **[`pokeemerald-fufufu`](https://github.com/ploppoke/pokeemerald-fufufu)** — Our `pokeemerald-expansion` fork featuring **Pokefish** (a chess-engine-inspired battle AI with an offline-trained NNUE-style compact integer value model, leak-free belief sampling over revealed information only, and resumable anytime matrix-game search), **Crony Intel** (route/gym trainers report what they saw of the player's team to the boss ahead) and **Opening Books**, optional **Clang + O-MVLL obfuscation** builds (`USE_CLANG=1`, `OBFUSCATE=1`), and fixes for five upstream battle engine bugs, ARMv4T GCC/Clang portability, and the 5,465-test Hydra suite.
- **[`gba-omvll`](https://github.com/ploppoke/gba-omvll)** — Scripts, GBA policy (`gba_omvll.py`), and bring-up docs to build a self-hosted ARMv4T/Thumb-1 Clang + `picolibc` + `compiler-rt` + O-MVLL toolchain from source without devkitARM.
- **[`o-mvll`](https://github.com/ploppoke/o-mvll) & [`picolibc`](https://github.com/ploppoke/picolibc)** — Our GBA-ported fork of the O-MVLL LLVM obfuscator (32-bit ARMv4T Thumb-1 bare metal; stack-decoded local encryption for C strings and Pokémon's `0xFF`-terminated `COMPOUND_STRING` game text; and a compile-time struct-table pass that encrypts species, ability, move, and item fields so no plaintext name, description, or base stat survives in the ROM), paired with `picolibc`.
- **[`gba-tools`](https://github.com/ploppoke/gba-tools)** — Dependency-free CLI tools (`gba-strings`, `gba-text`, `gba-sym`, `gba-table`, `gba-ref`, `gba-diff`, `gba-info`, `gba-patch`) that natively decode Pokémon's custom `charmap.txt` text encoding from `.gba` ROMs and `.elf` builds.
- **[`gba-fingerprint`](https://github.com/ploppoke/gba-fingerprint)** — Read-only `.gba` / `.elf` scanner that identifies which compiler built a ROM (AGBCC, devkitARM GCC, Wonderful GCC, or Clang), which `pokeemerald-expansion` version it matches, and what structural layouts it shares.
- **[`PatchouliKnowledgeAdvance`](https://github.com/ploppoke/PatchouliKnowledgeAdvance)** — Reproducible "decomp-recomp" source-mod runner that applies guarded source/asset manifests to `pokeemerald-fufufu`, rebuilds a fresh ROM with Clang, and verifies it in an emulator.

## AI policy
Sure, see `AGENTS.md`.
