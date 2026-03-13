# Bedside Patient Call & Nurse Alert Audio System
---

## Problem Statement

Current patient-call systems in Sri Lankan government hospital wards rely solely on a visual indicator light at the nurse station. This creates three critical failures:

- **No patient confirmation** — patients cannot tell if their call was registered
- **No mobility** — nurses moving through the ward cannot see a static light
- **No urgency distinction** — all calls appear identical

The result: patients attempt to get up unassisted, leading to preventable falls.

---

## Solution Overview

A self-contained **Bedside Audio Unit** clipped to each bed rail, producing two distinct audio tones using fully analog circuitry. No software, no network, no microcontrollers.

| Event | Tone | Waveform | Frequency |
|-------|------|----------|-----------|
| Patient presses call button | *"Call sent"* | Square wave | 1,000 Hz |
| Nurse presses acknowledge | *"Help coming"* | Sine wave | 750 Hz |

**Power supply:** Dual 9 V batteries — fully independent of mains power.

---

## Why These Tones?

- The **1,000 Hz square wave** is harmonically rich — sharp and clearly audible in a noisy ward environment.
- The **750 Hz sine wave** is pure and gentle — psychoacoustically calmer, reassuring rather than alarming.
- The frequency difference makes the two tones immediately and intuitively distinguishable.

---

## Signal Chain

Both operating paths share the same filter, limiter, and amplifier stages. Only the oscillator source switches between them.

### Path A — Patient Call (1,000 Hz Square Wave)

```
[Patient Presses Button]
        ↓
[Integrator & Schmitt Trigger]  →  Square wave, 1,000 Hz
        ↓
[Active LPF — Sallen-Key 2nd Order]  →  Harmonics stripped
        ↓
[Volume Limiter]  →  Clamped at ±0.6 V peak (safe SPL)
        ↓
[Class AB Amplifier]  →  THD < 1%
        ↓
[Speaker — 8 Ω]  →  Continuous alert tone at bedside
```

### Path B — Nurse Acknowledgement (750 Hz Sine Wave)

```
[Nurse Presses Acknowledge]
        ↓
[Wien Bridge Oscillator]  →  Sine wave, 750 Hz (clean, no harmonics)
        ↓
[Active LPF — Sallen-Key 2nd Order]  →  Noise cleaned, signal passes unchanged
        ↓
[Volume Limiter]  →  Clamped at ±0.6 V peak (safe SPL)
        ↓
[Class AB Amplifier]  →  THD < 1%
        ↓
[Speaker — 8 Ω]  →  Two short beeps, then silence
```

---

## Key Design Decisions

| Feature | Rationale |
|---------|-----------|
| Fully analog | No firmware, no boot time, no failure modes from software |
| Dual 9 V battery | Survives power cuts and generator switchovers |
| No wireless/MCU | Inherently immune to hospital EMI (MRI, surgical instruments); zero cybersecurity exposure |
| Class AB amplifier | Low distortion, efficient enough for battery operation |
| Volume limiter | Maintains consistent loudness as batteries discharge; protects patient hearing |

---

## Module Summary

| Stage | Function |
|-------|----------|
| Integrator & Schmitt Trigger | Generates 1,000 Hz square wave for alert tone |
| Wien Bridge Oscillator | Generates 750 Hz sine wave for acknowledgement tone |
| Active LPF (Sallen-Key, 2nd order) | Shapes output; suppresses harmonics for clean sine tone |
| Volume Limiter | Caps amplitude at safe, consistent level |
| Class AB Amplifier | Drives 8 Ω speaker with low distortion |
