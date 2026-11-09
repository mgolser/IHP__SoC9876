# SoC9876 LVS report

Full-chip LVS with the KLayout LVS deck of the PDK matches in all three modes, with every block at transistor level in modes 1 and 2. The result lines are in `lvs_summary.txt`.

## Tools and options

KLayout 0.30.12 in IIC-OSIC-TOOLS 2026.10, PDK `ihp-sg13cmos5l` commit `a49358cc0b20635fcb53c0940505e3563763fc7a`, deck `libs.tech/klayout/tech/lvs/run_lvs.py`. Each mode extracts the layout netlist and then compares it with the reference CDL:

```
python3 run_lvs.py --topcell=chip_top --run_mode=deep --no_series_res --no_simplify --layout=chip_top_lvs.gds --net_only
python3 run_lvs.py --topcell=chip_top --run_mode=deep --no_series_res --no_simplify --layout_netlist=chip_top_lvs_layout.cir --netlist=chip_top_lvs.cdl --combine_devices
```

The comparison runs in strict port mode with `flag_missing_ports`, so a missing or mislabeled top-level port fails. Pass criterion: `Congratulations! Netlists match.` and no `Netlists don't match` line in the log.

## Compared data

| Item | SHA-256 |
|---|---|
| Compared layout `chip_top.gds.gz`, top cell `chip_top`, without metal fill | `f9f2ac6a13b8419211b24f8a4d6129f2dca2dcbff7972e4b9a30cc7891d814ad` |
| Reference CDL `chip_top_full.cdl`, top `.SUBCKT chip_top` | `381028436d580f341f7e5d34f2259944adcc87a490693ddd99be67686d02c7d0` |
| Release CDL `release/v.1.0.0/netlist/SoC9876.cdl.gz` | `4f51b108e290678a39e7694293f53a09cb7936cbde021a2446e25e22c6474ce6` |
| Release CDL, uncompressed | `6b89de43b311ce2fbed9bfb403cc4fde7abcc2f0ee753a3380674e2058c52a2f` |
| Compared layout with metal fill | `9498cacc120760003251d6031dbfb06338e88241db56f2903bc288d71eeeffe1` |
| Final layout, top cell `chip_top` | `8b5a28729a9aa96abc3a9dbfe51f5276119f557334a28379aa7cf7ff42007ede` |
| Release GDS `release/v.1.0.0/gds/SoC9876.gds.gz`, top cell `SoC9876` | `26a35fc2c0930d10c57ed019fe13beb40c0a023fc34794a29e0a6c66c46e9128` |

- The release CDL is the reference CDL with the top `.SUBCKT` renamed from `chip_top` to `SoC9876` (same 68 ports in the same order) and its two header comment lines changed. `diff` shows only these three lines.
- The CDL that modes 1 and 2 read differs from the reference CDL in comment lines only. `zcat SoC9876.cdl.gz | grep -v '^\*' | sed '0,/^\.SUBCKT SoC9876 /s//.SUBCKT chip_top /' | sha256sum` gives `18c8c339f946af6bdccb1cc9a8772841f77dd88254673a975dcef65dbdd1826c`, the same as `grep -v '^\*'` on the CDL of both runs.
- The CDL is self-contained: 244 `.SUBCKT`, 5038 device lines, no `.include`, every called subcircuit defined.

## Results

| Mode | Layout side | Schematic side | Devices to compare, layout / schematic | Circuits matched | Result |
|---|---|---|---|---|---|
| 1 Full | all blocks and IO cells with their devices | reference CDL | 68502 / 4768 | 116 of 116 | match |
| 2 Border labels | as mode 1, every block cell keeps only the labels of its boundary pins | reference CDL | 68502 / 4768 | 116 of 116 | match |
| 3 IO abstract | pad ring and 16 analog blocks as pin abstracts (1744 pins), digital blocks with their devices | pin-only subcircuits for the 17 abstracts, digital block CDLs | 10285 / 484 | 83 of 83 | match |

The device counts are those the deck prints per circuit definition, before devices are combined. Mode 2 shows that every connection inside a block exists in metal. Mode 3 checks the top-level routing alone, with a label on every pin piece of an abstract.

## Scope

- No blackbox in modes 1 and 2: all 27 blocks are compared with their devices (`ABSTRACT 0 blocks`), no device is excluded.
- IO cells use the PDK CDL `libs.ref/sg13cmos5l_io/cdl/sg13cmos5l_io.cdl`. The supply pad `sg13cmos5l_IOPadVddA` (derived from `sg13cmos5l_IOPadVdd`) and the two core ESD clamp cells have their own CDL. The 65 tap devices of 34 IO cells take area and perimeter from the extraction, so taps are compared by their nets.
- Substrate and ground: no block CDL contains `sub!`, the block CDLs tie substrate terminals to the block ground pin, and all block grounds connect to `VSS`. The `sub!` pin of every IO cell connects to `VSS` at the top level.
- Supply alias: the `vdd` pin of the multiplier tree has no net in the top-level connection list and is mapped to `VDD_MUL`. This is the only alias.
- Block pin check: layout pins without a CDL pin are only extra supply rails and straps of nets the CDL has, and three spare current outputs. No CDL pin lacks a layout pin.

## Link to the final GDS

- Metal fill: XOR of the filled against the compared layout, flat per layer without the fill layers x/22: no layer differs. Each of the 17 fill cell types holds one shape on one x/22 layer, no text and no child cell.
- Final layout against the filled compared layout: XOR differs only inside the four 480 um corner windows, and there only on TopMetal1 126/0 (logos), the fill blocking layers x/23 and 160/0, and the fill layers x/22.
- The final layout carries three TopMetal1 logos (SW, SE, NW) instead of four. Each is a floating island: AND of logo and keep-out with every other drawn shape (fill included, marker layers excepted) is 0, no contact with other TopMetal1 or TopVia1 (nearest 33.4 um), 0 texts.
- Cell by cell: 304 common cells are equal (shape count, XOR, texts and child instances per layer). Only the three logo cells differ, on logo and keep-out layers. The shapes of the top cell are equal. The fill cell has 6483 of 1908043 placements fewer, each touching a new keep-out, and 0 new ones.
- Release GDS: one root cell `SoC9876`. `LayoutDiff` against the final layout with the top cell renamed is equal (309 of 309 cells), `strmxor -u -l` finds no differences.

## Regular DRC, submitter review

The classification below is the submitter's review, not a foundry waiver.

- Full regular deck on the filled compared layout (`9498cacc…`), `run_drc.py` with `--mp=2 --density_thr=1` and `sg13cmos5l_maximal` with `threads=1`: 32 of 32 tables, `Signal number` 0 in 43 logs, 406 markers, all on recommended rules on the IHP bond pad and IO cells: `Pad.fR_TM1` 191, `Pad.d1R` 215.
- Antenna (`--antenna`) on the same layout: 15 `Ant.e` (5 each on Metal3, Metal4, TopMetal1) at the dummy PMOS of the IHP `sg13cmos5l_LevelDown` in the five `sg13cmos5l_IOPadIn` cells. Its gate, source, drain and well tap sit on the `vdd` ring band from Metal1 on, and the pad ring alone gives the same ratios.
- Final layout: the four corner windows of the filled compared and the final layout, all tables except density, FEOL, antenna and `maximal` included: no marker differs, `Signal number` 0. The IHP precheck on the release GDS, density included, reports 0 violations (`release/v.1.0.0/doc/precheck.txt`).
