# SoC9876 Specification

Key figures per block, simulated at schematic or extracted level over the PVT corners of each block, -40 to +125 °C. Supplies, limits and interface: `doc/Datasheet.md`.

| Block | Key figures |
|---|---|
| Digital core | 16 DDS with 32-bit phase accumulator at 50 MHz and CORDIC sine, f_out = increment x 50 MHz / 2^32, amplitude = full scale / 2^shift (2-bit shift). Timing at 50 MHz over three `VDDD` corners: setup slack +2.285 ns, hold slack +10 ps. Power 29.9 mW typical (default switching activity) |
| Analog channel, 16 instances | DAC, filter and PGA. Chain SNDR at 2.93 MHz, all extracted: 53.48 dB nominal, 52.40 dB hot, 53.59 dB cold. SFDR 61.92 dBc nominal |
| DAC | 8 bit, 50 MS/s, 255 unit cells (4 thermometer + 4 binary bits). LSB 1.0548 uA, full scale 493.66 mVpp differential nominal, 439.32 to 558.32 mVpp over 81 PVT points. Monte Carlo: 100 of 100 draws monotonic, INL 0.049 LSB, DNL 0.048 LSB |
| Low-pass filter | Two-stage fully differential, corner 10 MHz with 5-bit trim, -2.8 to +2.5 % after trim over 27 corners and C +-10 %. Phase margin at least 66.0°. Current 1.065 mA nominal |
| PGA | Output 50 to 200 mVpp differential, 7-bit code (codes 12 to 111, monotonic). Own SNDR at least 58.57 dB over 153 points (schematic). Current 0.600 mA (extracted, tt) |
| SAR ADC, 2 instances | 12 bit, 1 MS/s, 14 decisions (two redundant) combined by the host as a weighted sum. SNDR at least 64.62 dB, INL / DNL 0.288 / 0.345 LSB (3 sigma, model). 24 differential inputs, ranges 1:1, 1:2.004, 1:4.008, 1:8.015 (about 200 to 1600 mVpp differential). Current 1.4912 mA in the worst corner, measurement buffer +1.06 to 1.09 mA |
| Reference current generator, 2 instances | 35 uA and 16 uA bias outputs, 4-bit core trim -9.5 to +10.2 %. Reference internal 0.825 V, pad 0.825 V or bandgap 0.91 V. Loop phase margin at least 63.45° (extracted). Start-up to 1 % in 18.6 to 24.9 us |
| Multiplier tree | 16 analog inputs, 16 differential readouts and 2 differential spare inputs on pads. 512-bit configuration chain loaded through SPI with a CRC-16 check. Bandgap `vbg` 0.91 V |
| Clock multiplier | f_out = N x 50 MHz, N 1 to 31 (up to 1.55 GHz). DCO band up to 1.690 GHz in the slow corner, down to 0.577 GHz in the fast corner (extracted). Fine step 136 to 156 ppm. Timing error 9.39 ps rms in the loop model (worst of 279 runs), lock in 186 of 186 cases |
| OTA | Fully differential folded cascode, extracted: DC gain 60.65 dB and GBW 64.89 MHz (5 pF) at tt, phase margin at least 69.26°, noise at most 25.08 uV (1 kHz to 10 MHz), offset sigma 0.199 mV, current 0.39 mA |
