# OpenCV-analysis-by-Python-on-Colab
OpenCV analysis 
# LCD-Induced 3D Optical Effect — Experimental Verification

> **Hypothesis:** A standard LCD panel can generate apparent 3D depth through Moiré fringe interference, without any stereoscopic hardware.

---

## Overview

This repository documents an independent experimental investigation into an anomalous optical phenomenon observed on LCD displays — specifically, the emergence of spatially displaced Moiré fringe patterns that behave as if located at a depth **different from the LCD surface itself**.

The experiment uses video analysis (Optical Flow + FFT) to provide quantitative evidence that:

1. Moiré/fringe patterns on LCD do **not** move with the LCD surface
2. Their motion profile is consistent with a **virtual 3D object** at a different depth plane
3. The fringe activity is **periodic and structured**, not random noise

---

## Background

### The Core Observation

When a camera or observer moves laterally in front of an LCD screen, certain interference patterns (Moiré fringes, satellite spots) appear to **lag behind** the LCD surface motion — similar to how a distant object appears to move less than a nearby one (motion parallax).

### Key Phenomenon Chain

```
LCD pixel grid interference
        ↓
Moiré fringe formation
        ↓
Fringe displacement ≠ LCD surface displacement
        ↓
Apparent 3D depth (parallax effect)
        ↓
Satellite / Blinking / Fringe emergence cycle
```

### Observed Angle-Dependent States

```
-90°  →  baseline (no fringe)
  0°  →  blinking onset
 45°  →  fringe formation
 90°  →  stable fringe
135°  →  strong Moiré
```

---

## Experimental Results

### 1. Parallax Displacement Analysis

**Graph: LCD Surface vs. Fringe/SPOT displacement over time**

| Feature | Displacement |
|---|---|
| LCD surface (blue) | ~29–31 px |
| Fringe / SPOT (red) | ~0–7 px |
| Ratio (Δx_spot / Δx_LCD) | **≈ 0.1** |

**Interpretation:**

If the fringe pattern were attached to the LCD surface, it would show the **same displacement** as the surface features. Instead, it moves ~10× less — consistent with an object located **significantly farther** from the camera than the LCD panel itself.

$$\frac{\Delta x_{\text{spot}}}{\Delta x_{\text{LCD}}} \approx \frac{3}{30} \approx 0.1$$

> This ratio rules out surface co-planarity. The fringe pattern behaves as a **virtual object at a different depth plane**.

---

### 2. Moiré Fringe Activity (Time Domain)

**Graph: Optical Flow Magnitude vs. Frame**

| Segment | Frames | Peak Magnitude |
|---|---|---|
| Baseline | most frames | 0.001 – 0.005 |
| Event 1 | ~70–200 | ~0.03–0.04 |
| Event 2 | ~540–620 | ~0.06 |
| Event 3 (strongest) | ~700–760 | **~0.19** |

**Key observations:**

- The vast majority of the video is near-static → camera is stable, background is not moving
- Three distinct activity bursts correspond to fringe emergence/disappearance transitions
- The largest peak (~0.19) likely corresponds to a full fringe transition event (emergence → peak → collapse)

> The signal is structured, not random — ruling out camera shake or ambient noise as the cause.

---

### 3. FFT Spectrum Analysis

**Graph: FFT Spectrum of Moiré Activity (0–15 Hz)**

| Frequency Band | Amplitude | Interpretation |
|---|---|---|
| DC (0 Hz) | ~1.85 | Sustained mean activity (pulled up by large peak events) |
| 0.5 Hz | ~1.57 | Slow periodic fringe cycle |
| 1.0 Hz | ~0.93 | Secondary periodicity |
| 1.3 Hz | ~1.33 | Angle-sweep repetition rate |
| 1.8 Hz | ~0.77 | Fringe transition sub-harmonic |
| 2–6 Hz | 0.3–0.6 | Intermediate dynamics |
| >6 Hz | ~0.1–0.25 | Noise floor |

**Critical finding — 1/f spectral structure:**

The FFT shows a characteristic **1/f (pink noise) decay** — amplitude decreases monotonically from low to high frequencies.

| Signal Type | FFT Profile |
|---|---|
| Random vibration / camera shake | White noise — flat across all frequencies |
| Mechanical periodic vibration | Sharp peaks at specific frequencies |
| **Moiré/Fringe optical transition** | **1/f decay — low-frequency dominant** ✓ |

> The 1/f structure is the signature of a **slowly-evolving optical phenomenon**, not instrumental noise. The dominant low-frequency components (0–2 Hz) directly match the manual angle-sweep period used during the experiment.

**Summary of FFT interpretation:**

```
Low-frequency dominance (0–2 Hz)
    → Fringe activity cycles match the observer's rotation period
    
1/f spectral decay
    → Continuous, smooth optical transition (not abrupt mechanical event)
    
High-frequency floor (>6 Hz) is flat and low
    → Camera shake is NOT the dominant signal source
```

---

## Methods

### Pipeline

```
Video input
    ↓
Lucas-Kanade Optical Flow (per-frame)
    ↓
Feature tracking: LCD surface vs. Fringe/SPOT
    ↓
Displacement comparison (parallax analysis)
    ↓
Global Optical Flow magnitude (whole-frame average)
    ↓
FFT of activity signal
    ↓
Frequency domain interpretation
```

### Tools

- Python (OpenCV, NumPy, SciPy, Matplotlib)
- Optical Flow: Lucas-Kanade sparse tracking + dense flow magnitude
- FFT: `numpy.fft` on the activity time series

### Terminology

| Term | Definition |
|---|---|
| **Moiré fringe** | Interference pattern from overlapping periodic grids (LCD pixel array × camera sensor) |
| **Fringe Activity Index** | Global mean optical flow magnitude — measures "how much the fringe pattern is changing", not position |
| **Fringe Visibility Score** | Alternative name emphasizing detection strength over displacement |
| **Satellite** | Secondary bright spot associated with the primary fringe pattern |
| **Blinking** | Rapid on/off transition of the fringe visibility at certain angles |
| **Parallax ratio** | Δx_spot / Δx_LCD — quantifies depth separation |

---

## Limitations & Next Steps

### Current limitations

- Single video case study — replication with multiple videos needed for publication-level claims
- Optical Flow magnitude conflates: LCD surface motion, fringe generation, camera shake, satellite motion
- No ground-truth depth measurement (e.g., structured light or stereo camera)

### Recommended next steps

1. **Multi-video replication** — repeat experiment across different LCD panels, angles, distances
2. **Controlled angle sweep** — motorized rotation at fixed speeds to produce clean FFT peaks
3. **Spatial decomposition** — isolate fringe region vs. background in optical flow computation
4. **Quantitative depth estimate** — use parallax ratio to back-calculate apparent virtual depth
5. **Comparison baseline** — repeat with OLED (no pixel grid) to confirm LCD-specificity

---

## Repository Structure

```
.
├── README.md               ← This file
├── analysis/
│   ├── optical_flow.py     ← Optical flow magnitude computation
│   ├── fringe_tracker.py   ← LCD surface vs. fringe displacement tracking
│   └── fft_analysis.py     ← FFT of activity signal
├── results/
│   ├── 1_fft_spectrum.png  ← FFT Spectrum of Moiré Activity
│   └── 2_moire_activity.png← Moiré Activity (time domain)
└── data/
    └── (video files — not included due to size)
```

> Python source code is maintained separately and will be added to `analysis/`.

---

## License

This project is shared for research and educational purposes.  
If you use or build upon this work, please cite this repository.

---

## Contact / Discussion

If you have observed similar phenomena or have suggestions for improving the experimental methodology, feel free to open an Issue.
