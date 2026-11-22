# Beat Frequency Oscillator (BFO) Metal Detector

Design, simulation and hardware realisation of an analog BFO metal detector built by **Team Synergy** for the third-semester analog design project (EN 2091 Laboratory Practice and Projects), Department of Electronic & Telecommunication Engineering, University of Moratuwa, Sri Lanka.

<!-- Add a photo of the assembled unit here -->

## Overview

Two LC oscillators run near 160 kHz. The search oscillator uses a hand-wound search coil as its inductor; the reference oscillator uses a fixed inductor. A metal object near the coil changes the coil inductance, shifts the search oscillator frequency, and changes the difference frequency of the two oscillators. The difference frequency is recovered with a mixer and low-pass filter and played through a speaker as an audible tone. A change in pitch indicates a target.

The multiplier is a **discrete double-balanced Gilbert cell**. Every monolithic option considered (SA612, MC1496, AD633, AD534, MPY634, CD4066) was unobtainable locally within the project timeline.

```
search coil -> search oscillator -> JFET buffer --\
                                                   >-- Gilbert mixer -> Sallen-Key LPF -> volume -> LM386N-3 -> 8 Ohm speaker
tuning pot  -> reference oscillator -> JFET buffer -/
3S LiPo -> L7809CV -> +9 V rail (+4.5 V virtual ground for the audio stage)
```

## Specifications

| Parameter | Value |
|---|---|
| Search oscillator frequency | 164.7 kHz (measured) |
| Reference oscillator frequency | 160 kHz (measured) |
| Resting beat frequency | 2 kHz to 10 kHz, operator-adjustable (measured) |
| Detection range | 7 cm, ferrous and non-ferrous (measured) |
| Metal-type discrimination | None |
| Search coil | Air core, 13 turns of 0.5 mm enamelled copper, 190 mm diameter, 95 µH (measured at 200 kHz) |
| Oscillators | Common-base Colpitts, 2N2222; divider 2 × 22 nF NP0 |
| Buffers | JFET source followers |
| Mixer | Discrete Gilbert cell: 2N2222 quad and RF pair, BC547C active tail, four 10 kΩ null trimmers |
| Low-pass filter | Second-order unity-gain Sallen-Key (TL071), f<sub>c</sub> = 15.6 kHz, Q = 0.725 |
| Audio | LM386N-3 into an 8 Ω speaker |
| Power | 11.1 V 3S LiPo (1200 mAh) → L7809CV → +9 V rail |
| Supply current | Approximately 220 to 230 mA (measured) |
| PCB | 4 layers, 100 mm × 100 mm; layer 2 GND plane, layer 3 +9 V plane |
| Enclosure | 3D-printed PLA, 20 % infill, pistol grip |

## Design highlights

- **Discrete Gilbert cell.** Double-balanced topology cancels LO and RF feedthrough by symmetry. The topology evolved from a single-ended BJT mixer, to a single-balanced pair, to the final double-balanced cell.
- **Active current-source tail.** A BC547C source replaces the resistive tail to raise common-mode rejection.
- **Mixer trimmers.** SPICE devices are identical, but real 2N2222 units differ in V<sub>BE</sub> and β. Four 10 kΩ trimmers null the residual carrier by hand.
- **JFET buffers.** Isolate the tanks from the mixer and prevent injection locking. A self-biased design forward-biased the gate junction on positive swings; a 1 MΩ / 1 MΩ gate divider (gate at 4.5 V, source at 5.4 V) fixed it.
- **Symmetric PCB layout.** An uninterrupted ground plane under the mixer and 100 nF decoupling at every active device.
- **Single-supply audio stage.** +9 V rail with a +4.5 V virtual ground; LM386N-3 with a 10:1 input divider, Zobel network and 220 µF output coupling.

## Simulation

The circuit was captured in LTspice and verified in three passes: `.op` (all devices active, trimmers at both extremes), `.tran` (oscillator start-up, beat envelope), and `.four` / FFT (beat tone and suppression of LO, RF and sum products). Two bugs were found by netlist inspection: a missing DC block that collapsed a bias and saturated the tail source, and the JFET buffer bias described above.

| Parameter | Simulated | Measured |
|---|---|---|
| Search oscillator frequency | 158.7 kHz | 164.7 kHz |
| Reference oscillator frequency | 166 kHz | 160 kHz |
| Supply current | 180 mA | 230 mA |

## Repository contents

| Item | Description |
|---|---|
| Final report | Design, calculations, simulation and test results |
| User manual | Operation, safety, troubleshooting and maintenance |
| Datasheet | Two-page summary of specifications |
| Schematics | Altium Designer export, two sheets |
| PCB design | 4-layer layout files |
| Bill of materials | Exported from Altium Designer |
| LTspice netlist | Full simulation netlist |
| Enclosure | CAD model of the PLA enclosure |

## Getting started

### Simulation

1. Install LTspice.
2. Open the netlist from the repository.
3. Run `.op`, then `.tran`, then the `.four` / FFT analysis on the transient result.

### Operation

The user manual is the authoritative procedure. In outline:

1. Connect the search coil to the BNC connector and connect the charged battery.
2. Set the volume low, switch on, and allow the unit to warm up.
3. Hold the coil in free air, at least 20 cm from metal, the ground and your body.
4. Turn the tuning control until a steady tone is heard within 2 kHz to 10 kHz. Do not tune toward silence: near zero beat the oscillators lock and the tone disappears.
5. Sweep the coil slowly and continuously. A change in pitch indicates a target. Re-tune when the resting tone drifts.

## Known issues and limitations

- **Range.** The detection range is 7 cm; the project requirement was 10 cm.
- **No discrimination.** The pitch shift magnitude is dominated by target size and distance, not material.
- **Drift.** Differential thermal drift between the two tanks and supply-voltage sag change the resting tone; manual re-tuning is needed.
- **Frequency offset.** Measured oscillator frequencies are about 5 % above the value calculated from the measured coil inductance (155.7 kHz). The cause was not isolated.
- **Regulator headroom.** The margin between the 11.1 V pack and the L7809CV dropout is thin and sets the end of life of a charge.
- **Mixer null.** Residual carrier feedthrough is nulled by hand and is a service adjustment.
- **Enclosure.** PLA, no ingress rating; not weatherproof.

## Safety

- Student prototype. Not for locating mines, unexploded ordnance, live utilities or any safety-critical purpose.
- Charge the 3S LiPo only with a compatible 3S balance charger. Never discharge below 3.0 V per cell. The instrument has no low-voltage cutoff; switch off and disconnect when the tone weakens or distorts.
- The L7809CV regulator becomes hot in operation.
- Do not adjust the internal mixer null trimmers.

## Team

| Index | Name | Contribution |
|---|---|---|
| 240722F | D. H. Wijendra | Schematic design, PCB design |
| 240577M | R. A. T. J. Rupasinghe | Breadboard implementation, debugging |
| 240730D | W. M. V. A. Wijesinghe | PCB soldering, schematic design |
| 240731G | T. W. M. R. P. Wijesundara | Enclosure design, documentation |

## Acknowledgements

We thank Dr. Premarathne Upeka and Dr. Sampath K. Perera for their guidance, and the laboratory assistants of the Digital Laboratory, Analog Laboratory and Electronics Workshop, together with our colleagues and senior students, for technical assistance and help sourcing components.

## References

1. G. Overton, *Inside the Metal Detector*.
2. B. Gilbert, "A precise four-quadrant multiplier with subnanosecond response," *IEEE Journal of Solid-State Circuits*, vol. 3, no. 4, pp. 365–373, 1968.
3. A. S. Sedra and K. C. Smith, *Microelectronic Circuits*, 7th ed., Oxford University Press, 2014.
4. Texas Instruments, *LM386 Low Voltage Audio Power Amplifier* datasheet.
5. STMicroelectronics, *L78xx Positive Voltage Regulators* datasheet.
6. ON Semiconductor, *2N5484* datasheet.

## Licence

Add a `LICENSE` file and name the licence here.
