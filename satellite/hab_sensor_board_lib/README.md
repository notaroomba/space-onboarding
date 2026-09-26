# HAB_Sensor_Board KiCad library

Shareable library of the **non-stock** parts used by `hab_sensor_board`. Everything
else in that project comes from the stock KiCad libraries and needs nothing extra.

Built and verified against KiCad 10.0.6.

```
hab_sensor_board_lib/
├── HAB_Sensor_Board.kicad_sym          symbols
├── HAB_Sensor_Board.pretty/            footprints
│   ├── CON_522711069_MOL.kicad_mod
│   ├── CONN9_MSD-4-A_SSD.kicad_mod
│   └── LOGO.kicad_mod
├── sym-lib-table                       drop-in project table
└── fp-lib-table                        drop-in project table
```

## Contents

| Part | Symbol | Footprint | Notes |
|---|---|---|---|
| Molex 52271-1069 FFC/FPC, 10 circuit, 0.5 mm, right-angle SMT | `CON_522711069_MOL` | `CON_522711069_MOL` | 10 signal pads + `P1`/`P2` board locks |
| microSD / TransFlash socket, MSD-4-A | `CONN9_MSD-4-A_SSD` | `CONN9_MSD-4-A_SSD` | 8 card contacts, `CD` detect, shell tabs `10`–`13`, 2 NPTH |
| Board logo (satellite artwork) | — | `LOGO` | Graphic only, no pads. F.SilkS + F.Fab |

No 3D models are referenced, so the library is self-contained.

### Provenance

* Both connector footprints came from the personal `Extras` library
  (`~/Documents/Kicad/Extras.pretty`), which is outside the project and was not
  shipped with it.
* `LOGO` existed **only** inside `hab_sensor_board.kicad_pcb` as a loose footprint —
  it was never in any library. It has been extracted here so it survives.
* The project contained **no custom symbols**: every `lib_id` in the schematic
  resolves to a stock KiCad library. The two symbols here were written for this
  library so the custom footprints are usable standalone, with pin numbers that
  match the pads (see below).

## Pin/pad mismatch this library fixes

The schematic pairs these footprints with generic stock symbols, and the pad
names do not line up:

| Ref | Stock symbol | Pin driven by schematic | Matching pad in footprint |
|---|---|---|---|
| J1, J2 | `Connector_Generic_MountingPin:Conn_01x10_MountingPin` | `MP` → GND | none (footprint has `P1`, `P2`) |
| J4 | `Connector:Micro_SD_Card` | `SH` → GND | none (footprint has `10`–`13`, `CD`) |

Those pads currently carry GND in the PCB, but nothing in the netlist puts them
there — the assignment was made by hand in pcbnew and will be dropped the next
time *Update PCB from Schematic* runs. The symbols in this library declare
`P1`/`P2` and `10`–`13`/`CD` directly, so the connection survives a re-import.

Swapping J1/J2/J4 over to these symbols is optional and is **not** done for you;
it changes the netlist and should be re-checked with ERC/DRC afterwards.

## Install

**Per project** (recommended for sharing) — copy this whole folder into the
project, then either copy the bundled `sym-lib-table` / `fp-lib-table` into the
project root, or add via *Preferences → Manage Symbol/Footprint Libraries →
Project Specific Libraries*:

```
Nickname: HAB_Sensor_Board
Symbols:    ${KIPRJMOD}/hab_sensor_board_lib/HAB_Sensor_Board.kicad_sym
Footprints: ${KIPRJMOD}/hab_sensor_board_lib/HAB_Sensor_Board.pretty
```

**Globally** — add the same nicknames under the *Global Libraries* tab with an
absolute path to wherever you unpacked this folder.

The `HAB_Sensor_Board` nickname is what the symbols' `Footprint` fields point at;
renaming the nickname breaks that link.

## Verify before you fab

`CON_522711069_MOL` and `CONN9_MSD-4-A_SSD` were drawn by hand, not generated
from a datasheet by a tool. Check the pad geometry, and in particular the
`CD` / shell-tab assignment on the microSD socket, against the vendor drawing
before committing to a fabrication run.
