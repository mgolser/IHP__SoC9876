# SoC9876 Datasheet

Mixed-signal test chip in IHP SG13CMOS5L (130 nm CMOS, Metal1 to Metal4 and TopMetal1), top cell `SoC9876`, die 3657 x 4292 um including the seal ring. All values are simulation results at schematic or extracted level over -40 to +125 °C, no silicon data exists. Block figures are in `doc/Specification.md`.

## Function

- Digital core: SPI slave, register map, 16 DDS sine generators (32-bit phase accumulator at 50 MHz) and a control bus to the analog blocks.
- 16 analog channels, each with an 8-bit current-steering DAC, a trimmable low-pass filter and a programmable gain stage.
- 16-input analog multiplier tree with its own bandgap reference.
- Two 12-bit SAR ADCs with input multiplexers for on-chip measurement, two trimmable reference current generators.
- Test structures: a clock multiplier (DCO in a PLL) and an OTA with all pins on pads.

## Pinout

116 ring positions (W 30, N 28, E 30, S 28): 107 bond pads and 9 core ESD clamp positions without bond pad. W and E count from bottom to top, N and S from left to right. The digital IO cells run on `IOVDD`/`IOVSS` with the ring band `vdd` (`VDDD`) as core supply. `sg13cmos5l_IOPadVddA` is `sg13cmos5l_IOPadVdd` with its TopMetal1 straps taken to the core edge instead of the ring band `vdd`, so each 1.5 V domain has its own supply. Analog pins use `sg13cmos5l_IOPadAnalog`, at `padres` (587 Ohm secondary protection) where they drive gates, at `pad` otherwise.

| Net | Positions | IO cell | Domain | Function |
|---|---|---|---|---|
| `VSS` | W01 W08 W10 W12 W14 W19 W21 W25 W28 W30 E09 E12 E19 S01 S06 S09 S22 | `sg13cmos5l_IOPadVss` | `VSS` | core ground, common to all core domains |
| `IOVSS` | W09 N02 E07 S03 | `sg13cmos5l_IOPadIOVss` | `IOVSS` | IO ring ground |
| `IOVDD` | W20 N03 E08 S04 | `sg13cmos5l_IOPadIOVdd` | `IOVDD` | IO ring supply 3.3 V |
| `VDDD` | W03 W07 W24 W27 W29 | `sg13cmos5l_IOPadVdd` | `VDDD` | digital core supply 1.2 V |
| `VDDA` | N01 N05 N08 S02 S05 S08 S10 S17 | `sg13cmos5l_IOPadVddA` | `VDDA` | analog supply 1.5 V |
| `VDDSW` | W02 W06 W23 W26 | `sg13cmos5l_IOPadVddA` | `VDDSW` | switching supply 1.5 V |
| `VDD_MUL` | E10 E11 E20 E21 | `sg13cmos5l_IOPadVddA` | `VDD_MUL` | multiplier tree supply 1.5 V |
| `VDD_OTA` | N15 | `sg13cmos5l_IOPadVddA` | `VDD_OTA` | OTA supply 1.5 V, also its enable |
| `VDD_CKM_OSC` | E03 | `sg13cmos5l_IOPadVddA` | `VDD_CKM_OSC` | clock multiplier oscillator supply 1.5 V |
| `VDD_CKM_DIG` | E04 | `sg13cmos5l_IOPadVddA` | `VDD_CKM_DIG` | clock multiplier controller, divider and output supply 1.5 V |
| no bond pad | W04 W22 (`VDDSW`), W05 (`VDDD`), N04 S07 (`VDDA`), N16 (`VDD_OTA`), E02 (`VDD_CKM_OSC`), E05 (`VDD_CKM_DIG`), E22 (`VDD_MUL`) | core ESD clamp | as listed | RC clamp of the domain to `VSS` |
| `rst_n` | W11 | `sg13cmos5l_IOPadIn` | `IOVDD` / `VDDD` | reset, active low |
| `clk` | W13 | `sg13cmos5l_IOPadIn` | `IOVDD` / `VDDD` | system clock 50 MHz |
| `cs_n`, `sclk`, `mosi` | W15, W16, W17 | `sg13cmos5l_IOPadIn` | `IOVDD` / `VDDD` | SPI chip select (active low), clock, data in |
| `miso` | W18 | `sg13cmos5l_IOPadTriOut4mA` | `IOVDD` / `VDDD` | SPI data out, driven while `cs_n` is low |
| `ckm_do_obs` | E06 | `sg13cmos5l_IOPadOut16mA` | `IOVDD` / `VDDD` | clock multiplier digital observation output |
| `ckm_ao_out` | E01 | analog, `pad` | `VDD_CKM_DIG` | clock multiplier analog output, 2 mA current switch |
| `vrefp_ext`, `vrefn_ext` | S13, S14 | analog, `pad` | `VDDA` | external reference of both SAR ADCs |
| `sar_u_tin_p`, `sar_u_tin_n` | S15, S16 | analog, `pad` | `VDDA` | test input of SAR ADC U |
| `sar_o_tin_p`, `sar_o_tin_n` | N06, N07 | analog, `pad` | `VDDA` | test input of SAR ADC O |
| `iref_pad_o`, `iref_pad_u` | S11, S12 | analog, `padres` | `VDDA` | reference current generator O, U: reference voltage input or current copy output |
| `ota_iref`, `ota_vcm` | N09, N10 | analog, `pad` | `VDD_OTA` | OTA bias current input, output common-mode reference |
| `ota_voutn`, `ota_voutp` | N11, N12 | analog, `pad` | `VDD_OTA` | OTA outputs |
| `ota_vinn`, `ota_vinp` | N13, N14 | analog, `padres` | `VDD_OTA` | OTA inputs |
| `mul_sp_in1p`, `mul_sp_in1n`, `mul_sp_in2p`, `mul_sp_in2n` | N17, N18, N19, N20 | analog, `padres` | `VDD_MUL` | multiplier tree spare inputs |
| `mul_oop<k>`, `mul_oon<k>` | k 0, 1: S18 to S21. k 8, 2, 3: S23 to S28. k 4 to 7: N21 to N28. k 14, 9, 12: E13 to E18. k 13, 11, 10, 15: E23 to E30 | analog, `pad` | `VDD_MUL` | multiplier tree readout k, `oop` before `oon` |

## Supply domains

| Domain | Nominal | Range at the die | Pads | Current typical / maximum | Supplies |
|---|---|---|---|---|---|
| `VDDA` | 1.50 V | 1.425 to 1.575 V | 8 | 37 / 47 mA | DAC cores, filters, PGAs, SAR ADCs, reference current generators |
| `VDDSW` | 1.50 V | as `VDDA`, at most 0.8 V above `VDDA` | 4 | 2.5 mA without DAC drivers / 8.2 mA estimate | DAC drivers, level shifters, SAR switches |
| `VDDD` | 1.20 V | 1.08 to 1.32 V | 5 | 24 / 31 mA | digital core, control bus nodes, level shifter inputs |
| `VDD_MUL` | 1.50 V | 1.45 to 1.65 V at the multiplier tree, set 1.53 to 1.55 V at the pads | 4 | 32 mA at code 14 / 64 mA at the highest code | multiplier tree, bandgap, configuration chain level shifter |
| `VDD_OTA` | 1.50 V | 1.425 to 1.575 V | 1 | 0.39 / 0.39 mA | OTA test structure |
| `VDD_CKM_OSC` | 1.50 V | 1.425 to 1.575 V | 1 | 0.6 to 1.3 / 3.3 mA | clock multiplier oscillator |
| `VDD_CKM_DIG` | 1.50 V | 1.425 to 1.575 V | 1 | not simulated / 15.8 mA | clock multiplier controller, dividers, output drivers |
| `IOVDD` | 3.3 V | 3.0 to 3.6 V | 4 | not simulated | pad ring, two digital outputs |
| `VSS` | 0 V | | 17 | 170 mA (sum of the maxima) | common ground of all core domains |
| `IOVSS` | 0 V | | 4 | | pad ring ground |

- Typical: tt, 27 °C, nominal supplies, all 16 channels on, SAR measurement buffers off. Maximum: worst corner of each block, reference current +10 %, both SAR buffers on, multiplier tree at its highest code.
- Digital currents are power estimates of the digital flow with default switching activity and can be off by a factor of up to 5.
- `VDD_MUL`: the extracted IR drop of `VDD_MUL` plus `VSS` to the multiplier tree is 65 to 74 mV, hence the set point at the pads.

## Limits and operating conditions

The design was verified within these values. They are not IHP ratings.

| Quantity | Limit | Note |
|---|---|---|
| 1.5 V domains | 1.425 to 1.575 V at the die, never above 1.65 V | 1.65 V is the thin-oxide limit of the design |
| `VDD_MUL` | 1.45 to 1.65 V at the multiplier tree | |
| `VDDD` | 1.08 to 1.32 V | timing sign-off corners |
| `IOVDD` | 3.0 to 3.6 V | range of the start-up check |
| `VDDSW` - `VDDA` | at most 0.8 V at any time, also during power-down | DAC oxide |
| Temperature | -40 to +125 °C | verification range |
| Digital inputs | 0 to `IOVDD` | |
| Analog pins | 0 to the supply of their domain | the IO cell clamps to `IOVDD`/`IOVSS` only |
| `vrefp_ext`, `vrefn_ext` | `VDDA`/2 +- 50 mV | |
| `ota_iref` | 35 uA +- 10 % into the pin | |
| `ckm_ao_out` | at most 1.4 mA DC average | pad cell current density |
| `clk` | 50 MHz, duty cycle 50 +- 5 % | with the clock multiplier PLL in use about 47.5 to 55 % at the pad (estimate) |
| `sclk` | at most 3.125 MHz | read-back timing |
| DDS output frequency at full amplitude | at most 10 MHz | DAC oxide |
| HBM | +-2 kV, simulated against guideline values | gate inputs behind `padres` reach 6.6 to 7.7 V against `VSS` at +2 kV, above the guideline |

## Power-up and power-down

1. Power up `IOVDD` first, then `VDDD`, then the 1.5 V domains. The power-good of the `IOVDD` regulator enables the core regulators. `VDDD` and a 1.5 V domain must never be up together while `IOVDD` is below 1.3 V. Not allowed: all supplies at once from separate regulators, and 1.5 V domains before or together with `VDDD` at slow ramps.
2. Hold `rst_n` low or high-impedance until `IOVDD`, `VDDD` and all 1.5 V domains are up and `clk` runs. Without `IOVDD` the core reads `rst_n` as released even with the pad at 0 V, because the IHP input cell has no pull resistor. With the board pull-down a reset follows any loss of `IOVDD` once `IOVDD` is back above 1.1 to 1.25 V, as long as the host does not drive `rst_n` high at once.
3. Power down in reverse order: 1.5 V domains, then `VDDD`, `IOVDD` last. Never keep a 1.5 V domain up without `VDDD`.
4. Supply ramps of 10 us and 1 ms are verified. The ESD clamps do not trigger for ramps of 1 us to 1 ms. A step of 0.5 V or more within 0.3 us on a powered domain triggers a clamp, which then draws 0.2 to 0.3 A for about 1 us.
5. After reset all channels, both SAR ADCs, both reference current generators and the clock multiplier are off. Enable the reference current generators only after all supplies are up.

## Board rules

1. `rst_n`: pull-down of 10 to 100 kOhm to `VSS`.
2. Connect `VSS` and `IOVSS` to the same ground in the package (common plane or down-bonds to the paddle), or at the latest at the package pins. The IHP IO cells clamp their supplies only against `IOVSS`.
3. Bond all 17 `VSS` pads.
4. Feed `VDDSW` from `VDDA` through a ferrite of at most 1 Ohm, or add a Schottky diode from `VDDSW` to `VDDA`.
5. Drive or tie `clk`, `cs_n`, `sclk` and `mosi`, they have no pull on the chip (`cs_n` high when idle).
6. `vrefp_ext`, `vrefn_ext`: floating 100 mV source centred on `VDDA`/2, at least 1 uF plus 100 nF to `VSS` at the pads, source resistance at most 1 kOhm, noise at most 10 uV rms up to 500 kHz.
7. `ckm_ao_out`: 50 Ohm pull-up through a bias-T into a 50 Ohm instrument. Keep the analog output off except during measurements.
8. `iref_pad_u`, `iref_pad_o`: leave open when not used.

## Digital interface

- `clk`: system clock 50 MHz, the SAR logic uses both edges.
- `rst_n`: asynchronous reset, active low, released synchronously on chip.
- SPI slave, mode 0, MSB first, `sclk` at most 3.125 MHz. Read-back needs `clk` >= 16 x `sclk`.
- Frame of 49 bits: `rw` (1 = read), `adr[15:0]`, `dat[31:0]`. Only a frame of exactly 49 bits writes, on the rising edge of `cs_n`.
- `adr[15:8]` selects a block, `adr[7:0]` a register in it. Address 0x0000 is an echo register for a link test. `adr[15:8]` from 0x19 reads 0xFFFFFFFF.
