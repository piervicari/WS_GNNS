# GNSS Project – Raw Measurements & PVT Analysis (Android GNSS Logger)

This repository documents a project focused on the **analysis of raw GNSS measurements** and resulting **PVT (Position, Velocity, Time)** solutions under multiple environmental, device, and adversarial conditions. The goal is to understand how real-world factors (signal obstruction, indoor attenuation, power-saving behaviors, spoofing, and RF interference) impact **accuracy and reliability** of smartphone GNSS positioning.

The experimental campaign was carried out using the **GNSS Logger** app on an **Android smartphone (Oppo Find X5)**, while data processing and visualization were performed in **MATLAB** using plots such as **pseudoranges**, **C/N0**, **WLS position**, and **receiver clock bias / common bias offset**.

---

## Project goals

- Perform a **comprehensive analysis** of **raw GNSS measurements** and **PVT solutions** collected in different conditions.  
- Quantify how the following scenarios affect GNSS performance:
  - **Open-sky** (baseline)
  - **Open-sky + battery saving mode** (power-management effects)
  - **Indoor** (attenuation + loss of line-of-sight)
  - **Spoofing (position)** and **spoofing (delay)** (simulated via MATLAB processing changes)
  - **Interference source** (RF noise during a nearby phone call)  
- Extract and interpret key indicators:
  - **Position scatter / dispersion**
  - **Pseudorange behavior over time**
  - **C/N0 (carrier-to-noise density ratio) trends**
  - **HDOP variations**
  - **Clock bias discontinuities**
  - **Horizontal speed noise / instability**

---

## What we obtained (main outcomes)

### 1) Open-sky baseline: real dispersion even in “good” conditions
- The open-sky positioning shows a **noticeable spread**, i.e., limited precision in a smartphone receiver.  
- About **half of the recorded points fall within ~19.6 m** radius, highlighting a non-negligible error budget under real conditions (multipath, environmental effects).  
- **Horizontal speed spikes** (close to ~1 m/s) appear despite the device being stationary, showing measurement noise effects.  
- **HDOP** fluctuates between **~1 and 2** with a peak around **~3**, reflecting changes in satellite geometry/visibility.  
- Several satellites show **C/N0 < 30 dB-Hz**, with one satellite persistently below **~25 dB-Hz**, which can degrade stability and accuracy.  

**Takeaway:** even outdoors, smartphone GNSS is affected by multipath and geometry; a clean open-sky is necessary but not sufficient for tight accuracy.

---

### 2) Battery saving mode: clock discontinuities and large outliers
- With power-saving enabled, the receiver may **reduce sampling rate** or enter a **sleep state**, producing **clock discontinuities** and measurement gaps.  
- Upon wake-up, the PLL resamples more slowly and a **shift in clock bias** becomes visible; **HDOP spikes** occur while re-synchronizing.  
- The position becomes temporarily unreliable: some measurements deviate up to **~700 m** from the median.  
- Due to discontinuous updates, several signals appear “flat” (mostly zeros) with only occasional peaks when updates occur.  

**Takeaway:** power-saving does not only reduce update rate; it can break time/clock continuity and cause extreme position errors.

---

### 3) Indoor: GNSS becomes largely unusable without line-of-sight
- Despite collecting for more than **5 minutes**, the indoor session produced **no meaningful continuous positioning** in MATLAB.  
- Only two isolated fixes were obtained, separated by an implausible **~350 m** distance.  
- This confirms how walls/ceilings attenuate signals and prevent stable lock on enough satellites.  

**Takeaway:** indoor GNSS without LoS is severely limited; sparse fixes can be erroneous and not trustworthy.

---

### 4) Spoofing (position): “precise but wrong” clustering around forged coordinates
- Spoofing was simulated by **modifying `ProcessGnssMeasScript.m`** to enable **spoofing position** and **spoofing delay**.  
- Around **~15 seconds**, sharp spikes appear and the system perceives unrealistic satellite velocity changes (up to ~2000 m/s and down to ~−1500 m/s).  
- The estimated position abruptly shifts from the true starting area (near the P lecture halls) to a spoofed location (e.g., **Piazza Adriano**).  
- After the transition, fixes **cluster tightly** around the spoofed position, indicating the receiver was fully deceived and shows improved “precision” around the wrong point.  

**Takeaway:** spoofing can produce stable-looking results; low scatter does not imply correctness.

---

### 5) Spoofing (delay): strong discontinuities in pseudorange and clock bias
- With spoofing delay, the **final position** is not different from position-only spoofing, but internal observables change substantially: **pseudoranges ramp rapidly** around the spoof onset and the **clock bias** shows a similar effect; after that, values become constant with limited fluctuations.  
- Interpretation: delay spoofing manipulates time-of-arrival, making the receiver believe it is farther from all satellites.  
- Comparing the two spoofing techniques:
  - **Delay spoofing** shows **marked discontinuities** and coherent pseudorange evolution with a fixed offset (all satellites “moved” together).  
  - **Position-only spoofing** appears **more subtle/continuous** but introduces uneven offsets across satellites, leading to physically incompatible relative speeds and severe mispositioning.  

**Takeaway:** delay spoofing is more detectable in timing observables; position-only spoofing can be more insidious.

---

### 6) Interference source (phone call): C/N0 degradation and noisier speed
- A nearby phone call introduces RF noise and possible shared-hardware effects that degrade GNSS reception.  
- The C/N0 plot shows **drops and oscillations**, consistent with electromagnetic noise injection.  
- Horizontal speed shows **stronger fluctuations** due to Doppler instability, lowering speed estimation accuracy.  

**Takeaway:** even common RF activity can measurably reduce GNSS quality, especially for Doppler-based estimates.

---

## How we obtained these results (methodology)

- **Data acquisition:** measurements collected in the **inner courtyard of the Polytechnic** with **GNSS Logger** on **Oppo Find X5**, across open-sky, battery saving, and indoor conditions.  
- **Spoofing simulations:** enabled by **modifying `ProcessGnssMeasScript.m`** to introduce spoofing parameters for:
  - **position spoofing**
  - **delay spoofing**  
- **Interference evaluation:** performed by analyzing a dataset acquired while the device was exposed to RF noise caused by a **nearby smartphone call**.  
- **Analysis outputs:** pseudorange plots, **C/N0**, **WLS solutions**, and **clock bias / common bias offset**, plus HDOP and horizontal speed trends to interpret reliability and dynamics.

---

## Conclusion
Across all experiments, GNSS performance shows **significant variations** depending on environment, device state, and adversarial/interference conditions, with clear signatures in raw observables (pseudorange, C/N0, clock bias) and in the final PVT solution.

---

## Authors
Pietro Vernuccio, Pierfrancesco Vicari, Lorenzo Vinesia, Mattia Zanti.
