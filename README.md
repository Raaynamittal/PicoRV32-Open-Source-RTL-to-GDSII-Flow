# PicoRV32 — Open-Source RTL-to-GDSII Flow

This repository documents running [PicoRV32](https://github.com/YosysHQ/picorv32),
a well-known open-source RISC-V (RV32I) CPU core by YosysHQ, through a
complete chip physical-design flow using entirely free and open-source
tools — no proprietary/commercial EDA software.

**Important:** the PicoRV32 CPU design itself is third-party IP, written
by Claire Xenia Wolf / YosysHQ (see license header in `src/picorv32.v`).
It is **not** my design. My contribution here is the flow configuration,
debugging the toolchain, and running/verifying the physical design
process — this repo is a toolchain-validation exercise, done alongside
my own original hardware design project
([conv_top CNN accelerator](LINK_TO_YOUR_OTHER_REPO)).

## Toolchain

- **Synthesis:** Yosys
- **Place & route, DRC-adjacent checks:** OpenROAD
- **Physical verification (DRC/LVS):** Magic, Netgen
- **Flow orchestration:** LibreLane
- **Process:** SkyWater SKY130 (open-source PDK)

## What I actually did

- Wrote the LibreLane flow configuration (`config.json`), including a
  custom step sequence that skips a specific broken intermediate timing
  checkpoint (`STAPrePnR`) caused by a known version-compatibility issue
  between the LibreLane scripts and the installed OpenSTA binary in this
  environment, while preserving the steps that matter for a real
  physical result (place, route, DRC, LVS).
- Diagnosed and resolved multiple environment issues along the way:
  a stale PDK rulebook format, an incorrect default fabrication process,
  and the OpenSTA/OpenROAD version mismatch mentioned above.
- Ran the full flow end-to-end and verified the result directly from the
  tool reports (not just "it didn't crash").

## Result

**DRC: 0 violations. LVS: `Circuits match uniquely.`**

A complete, verified physical layout (GDSII) was produced for
PicoRV32 — a real, independently-designed RISC-V core — using a
100% open-source RTL-to-GDSII flow (Yosys, OpenROAD, Magic, Netgen,
orchestrated by LibreLane, targeting the SkyWater SKY130 process).
Both manufacturing-rule correctness (DRC) and layout-vs-schematic
electrical equivalence (LVS) passed cleanly.

## Files

- `src/picorv32.v` — unmodified PicoRV32 source (ISC License, YosysHQ)
- `config.json` — LibreLane flow configuration (my work)
- `results/` — final GDS layout and DRC/LVS reports
