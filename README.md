# XR-2206 Function Generator

> Multi-waveform signal generator (sine · square · triangle) · 0 Hz – 100 kHz · ±12 V dual-rail supply  
> **La Cité collégiale — 027930 TEC · Winter 2026**

---

## Photos

| Annotated Breadboard | Clean Build |
|---|---|
| ![Annotated](../images/function-generator-annotated.jpg) | ![Build](../images/function-generator.jpg) |

---

## Overview

A fully functional benchtop function generator built around the **XR-2206 monolithic function generator IC** (EXAR). The circuit was designed, prototyped on breadboard, and documented to industry standards — including a full **service manual** (MS-GEN-001) and **user manual** (MU-GEN-001) authored in both English and French.

The generator was built progressively across 8 lab sessions, each adding a new subsystem. All sub-circuits were debugged individually using an oscilloscope and DMM before integration.

---

## Specifications

| Parameter | Value |
|-----------|-------|
| Waveforms | Sine · Triangle · Square |
| Frequency range | ~0 Hz – ~100 kHz (4 switchable ranges) |
| Max output amplitude | Sine: 21 Vpp · Triangle: 22.5 Vpp · Square: 23 Vpp |
| Min output amplitude | 0 Vpp (all waveforms) |
| Attenuation | −20 dB · −40 dB · −60 dB |
| TTL output | 0 V / +5 V logic square wave |
| DC Offset | Adjustable via DPDT switch |
| Power supply | +12 V (LM7812) · −12 V (LM741A tracking) · +5 V (LM7805) |
| THD (sine) | < 0.5% after RV1/RV3 adjustment |

---

## Architecture

```
[120 Vac Transformer]
        │
[Bridge Rectifier + Filter Caps]
        │
[LM7812 +12V] ──── [LM741A Tracking Supply −12V] ──── [LM7805 +5V]
        │
[Frequency Range Select (C1–C4 via DPDT switches)]
        │
[XR-2206 IC1] ── f₀ = 1/(R×C) ── [RV5 1MΩ frequency dial]
  │         │
[Sine/Tri]  [Square (pin 11)]
  │              │
[LM741A Output Stage (gain −3)] ── [RV2 50kΩ amplitude]
  │
[DC Offset Stage (RV6)] ── [Attenuation Network −20/−40/−60 dB]
  │                              │
[Main Output]              [TTL Output: 2N3904 + 74LS00]
```

---

## Bill of Materials (Key Components)

| Ref | Component | Value |
|-----|-----------|-------|
| IC1 | XR-2206 Function Generator | EXAR |
| IC3 (×4) | Op-Amp | LM741A |
| IC4 | Quad NAND (TTL output) | 74LS00 |
| IC5 | Voltage Regulator +12V | LM7812 |
| IC6 | Voltage Regulator +5V | LM7805 |
| Q500 | NPN Transistor (TTL converter) | 2N3904 |
| D100/D101 | Rectifier Diodes | 1N4005 |
| RV5 | Frequency control pot | 1 MΩ |
| RV2 | Amplitude control pot | 50 kΩ |
| RV1 | Sine distortion adjust (RA) | 1 MΩ |
| RV3 | Sine symmetry adjust (RB) | 25 kΩ |
| C1–C4 | Frequency range capacitors | 0.1 µF / 0.01 µF / 0.001 µF / 0.1 µF |
| C_filter (×2) | Power supply filter caps | 1000 µF @ 63 V |

---

## How It Works

### Frequency Generation
The XR-2206 oscillates at **f₀ = 1/(R×C)**. The four DPDT switches each connect a different timing capacitor (C1–C4), selecting the frequency decade. Within each decade, **RV5 (1 MΩ)** provides continuous fine adjustment.

### Waveform Selection
- **Sine / Triangle** — selected via DPDT switches connected to pins 13–14 of the XR-2206  
- **Square** — taken directly from pin 11  
- Potentiometers RV1 and RV3 are adjusted alternately to minimize sine THD below 0.5%

### Output Stage
An **LM741A** in inverting configuration (gain = −3) amplifies the signal to ~20 Vpp max. **RV2** controls amplitude. DC offset is switched in via a dedicated DPDT.

### Attenuation Network
A resistive ladder (R100–R106, ~50 Ω output impedance) provides −20 dB, −40 dB, or −60 dB via two white DPDT switches.

### TTL Output
The square wave (pin 11) drives the base of **Q500 (2N3904)** in saturation. Two paralleled NAND gates (74LS00) re-invert and double the output current, producing a clean 0 V / 5 V TTL signal.

### Power Supply
A 120 Vac → 25 Vrms transformer feeds a full-wave rectifier. After filtering, **LM7812** produces +12 V. An LM741A inverting follower with **RV100** trims −12 V until |+12 V| = |−12 V| within ±20 mV. **LM7805** derives +5 V for TTL logic.

---

## Using the Generator

1. Verify power supply voltages: +12 V, −12 V, +5 V
2. Activate one frequency range button (black DPDT)
3. Select waveform: grey button 1 = sine · grey button 2 = triangle · black button 3 = square
4. Turn **RV5** (clockwise = higher frequency)
5. Turn **RV2** (clockwise = higher amplitude)
6. Connect oscilloscope to output; set timebase to match expected frequency

> Allow 2–3 minutes warm-up before precision measurements.

---

## Troubleshooting

| Symptom | Likely Cause | Action |
|---------|-------------|--------|
| No output | Power absent / IC1 faulty | Check TP1–TP3 (+12V, −12V, +5V) |
| Fixed frequency | RV5 open / range cap faulty | Measure RV5 resistance; test C1–C4 with LCR meter |
| Fixed max amplitude | RV2 shorted | Measure RV2 terminals |
| Distorted sine | RV1/RV3 misadjusted | Alternate RV1 and RV3 until THD < 0.5% |
| No TTL output | +5 V absent / Q500 faulty | Check TP3; inspect 2N3904 and 74LS00 wiring |
| No −20 dB attenuation | R103 (390 Ω) open | Probe across R103 |

---

## Documentation

This project includes full industry-standard documentation authored by Adam Zaghloul:

- **Service Manual** — MS-GEN-001 v1.0 (maintenance, calibration, fault diagnosis, repair procedures)
- **User Manual** — MU-GEN-001 v1.0 (operating procedures, specifications, safety)
- **Design Report** — Full BOM, operating principles, measured results, schematics
- **DesignSpark PCB** — Complete schematic

---

## Skills Demonstrated

`Analog IC design` `XR-2206` `Op-amp circuits` `Voltage regulation` `TTL logic` `Resistive attenuation` `Oscilloscope (FFT)` `DMM` `DesignSpark PCB` `Technical documentation` `Multisim simulation` `Fault isolation` `IPC-A-610`

---

*Adam Zaghloul · La Cité collégiale · Winter 2026 · [adamzaghloul07@gmail.com](mailto:adamzaghloul07@gmail.com)*
