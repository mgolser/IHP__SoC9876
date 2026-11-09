# SoC9876 v.1.0.0

Mixed-signal test chip in IHP SG13CMOS5L, top cell `SoC9876`, die 3657 x 4292 um (15.70 mm2), seal ring and metal fill included.

- `release/v.1.0.0/gds/SoC9876.gds.gz`: SHA-256 `26a35fc2c0930d10c57ed019fe13beb40c0a023fc34794a29e0a6c66c46e9128`, MD5 `c82fdd22ed20fc976e2af1911d0a4702`
- PDK `ihp-sg13cmos5l` commit `a49358cc0b20635fcb53c0940505e3563763fc7a`, IIC-OSIC-TOOLS 2026.10, KLayout 0.30.12
- IHP precheck on this file: 0 violations (`release/v.1.0.0/doc/precheck.txt`)
- `release/v.1.0.0/netlist/SoC9876.cdl.gz`: full-chip CDL at transistor level, top `.SUBCKT SoC9876`, SHA-256 `4f51b108e290678a39e7694293f53a09cb7936cbde021a2446e25e22c6474ce6`, MD5 `605764d907f01f8f4a847d31e311a916`
- Full-chip LVS with the KLayout deck of the PDK: match in three modes, all blocks at transistor level (`release/v.1.0.0/doc/lvs/`)
- The seal ring text "Calculated area: 1.6e-05 sq mm" is an annotation error of the seal ring cell, the area is 15.70 mm2.
