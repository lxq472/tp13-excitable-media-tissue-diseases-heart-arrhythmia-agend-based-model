# Physics of Molecular Diseases — Week 4
## Excitable Media: Heart Arrhythmia & Pulsatile Insulin Secretion

> **MSc in Bio- and Medical Physics**
> Course: *Physics of Molecular Diseases*
> Topic: Excitable media, Agent-Based Modelling, FitzHugh–Nagumo dynamics

---

## Overview

This repository contains Python implementations of the **Agent-Based Model (ABM)** of cardiac excitable media, based on the work of Christensen et al. (PRL, 2015). The project explores how structural and dynamical heterogeneity in heart tissue leads to spiral wave formation (atrial fibrillation), and how the refractory period can be used as a therapeutic target.

The biological context spans two systems treated as excitable media:
- **Cardiomyocytes** → heart arrhythmia (spiral waves, ventricular fibrillation)
- **Pancreatic β-cells** → pulsatile insulin secretion (disrupted in Type 2 Diabetes)

---

## Scientific Background

### Excitable Media

An excitable medium consists of many coupled excitable units, each capable of three states:

| State | Description |
|---|---|
| **Resting** | Quiescent, waiting for stimulus |
| **Excited** | Fired above threshold (fast, positive feedback) |
| **Refractory** | Recovering, cannot be re-excited (slow, negative feedback) |

Examples include heart cells, pancreatic β-cells, neurons, and even forest fires. The common regulatory structure is a **fast activator** (u) coupled to a **slow inhibitor** (v), captured by the **FitzHugh–Nagumo (FHN) model**:

$$\dot{u} = u(u-a)(1-u) - v + D_u \nabla^2 u$$
$$\dot{v} = \varepsilon u + D_v \nabla^2 v, \quad \varepsilon \ll 1, \quad D_v \ll D_u$$

### Heart Arrhythmia

Normal cardiac rhythm is a single planar wave front originating from the pacemaker (SA node). **Atrial Fibrillation (AF)** arises when spiral waves (rotors) form, producing multiple chaotic wave fronts and uncoordinated contraction.

The Christensen et al. model shows that AF emerges when the transversal cell-to-cell coupling ν decreases below a threshold ν\*, as occurs with age-related fibrosis. The **analytical risk** of developing AF is:

$$P_{risk} = 1 - \left[1 - (1-\nu)^\tau\right]^{\delta L^2}$$

where τ is the refractory period, δ is the fraction of dysfunctional cells, and L is the tissue size.

### Pulsatile Insulin Secretion

Pancreatic β-cells behave as excitable units coupled via calcium waves (CICR — Calcium-Induced Calcium Release), analogous to cardiomyocytes. Insulin is secreted in pulses (~5 min period) in healthy individuals. In **Type 2 Diabetes (T2DM)**, pulsatility is disrupted — pulses are of lower amplitude and more irregular. GLP-1 (a major T2DM drug) facilitates CICR and restores pulsatility.

---

## Repository Structure

```
.
├── README.md
├── abm_excitable_media.py      # Q1: Core ABM implementation
├── spiral_formation.py         # Q2: Noise & isotropic coupling → spirals
└── refractory_effect.py        # Q3: Effect of increasing/decreasing τ
```

---

## Exercises & Questions

The three scripts address the following questions from the course problem set:

> **Overarching question:** *"Where in the network would you intervene to restore excitability?"*

### Q1 — `abm_excitable_media.py`
**Implement the Agent-Based Model of excitable media outlined in lecture notes and Christensen et al. 2015.**

Implements the full 2D ABM on a 200×200 grid with:
- Three-state cellular automaton (Resting / Excited / Refractory)
- Anisotropic cell-to-cell coupling controlled by ν
- Dysfunctional cells (fraction δ, failure probability ε)
- Pacemaker cells along the left boundary
- Three dynamical regimes: planar waves (ν=0.9), self-terminating spirals (ν=0.20), persistent AF (ν=0.10)
- Analytical P_risk curve vs ν

### Q2 — `spiral_formation.py`
**Investigate how (a) noise in the refractory period and (b) isotropic coupling lead to spiral formation.**

- **Experiment A:** Fixes ν=1 (isotropic), sweeps σ_τ ∈ {0, 5, 15, 25}. Shows that even small noise in τ is sufficient to create wave breaks and spirals.
- **Experiment B:** Fixes σ_τ=10, sweeps ν ∈ {0.10, 0.20, 0.50, 1.00}. Shows that anisotropy and noise are additive risk factors.
- **Experiment C:** 2D phase diagram (ν × σ_τ) of spiral disorder in steady state.

**Key finding:** Noise in τ and reduced ν are two independent but additive mechanisms for spiral formation. With sufficient noise, even fully isotropic (ν=1) tissue can develop spirals.

### Q3 — `refractory_effect.py`
**What will be the effect of increasing/decreasing the refractory period?**

- **Analytical:** P_risk(ν) curves for τ ∈ {20, 35, 50, 75, 100, 150} — increasing τ shifts the AF threshold ν\* downward, expanding the safe zone.
- **Simulation:** ABM with ν=0.15 fixed, varying τ ∈ {25, 50, 75, 120} — tissue that is arrhythmic at τ=25 becomes healthy at τ=120.
- **Summary plot:** Disorder vs τ bar chart with τ_crit marked, connecting analytical prediction to simulation.

**Clinical connection:** Antiarrhythmic drugs (Class I & III, e.g. amiodarone, flecainide) prolong the action potential duration → increase τ → reduce ν\* → convert structurally arrhythmic tissue to non-arrhythmic without modifying the underlying fibrosis.

---

## Model Parameters

| Parameter | Symbol | Value | Description |
|---|---|---|---|
| Grid size | L | 200 | After coarse-graining (b=5 from L'=1000) |
| Refractory period | τ | 50 | τ' / (b·Δt') = 150 ms / 3 ms |
| Pacemaker period | T | 220 | T' / (b·Δt') = 660 ms / 3 ms |
| Dysfunctional fraction | δ | 0.05 | Fraction of cells that may fail |
| Failure probability | ε | 0.05 | P(dysfunctional cell fails to fire) |
| Transversal coupling | ν | varied | 1=isotropic/young, <1=fibrotic/aged |
| AF threshold | ν\* | ~0.14 | At τ=50, δ=0.05, L=200 |

---

## Key Results

| Mechanism | Effect on spirals | Clinical analogue |
|---|---|---|
| ↓ ν (fibrosis) | More spirals, lower ν\* | Age-related AF |
| ↑ σ_τ (noise) | More spirals, independent of ν | Cellular heterogeneity |
| ↑ τ (longer refractory) | Fewer spirals, ν\* shifts down | Antiarrhythmic drugs |
| Ablation of critical regions | Terminates spirals | Catheter ablation therapy |

---

## References

1. **Christensen K., Manani K.A., Peters N.S.** (2015). *Simple Model for Identifying Critical Regions in Atrial Fibrillation.* Physical Review Letters, 114, 028104.

2. **Satin L.S., Butler P.C., Ha J., Sherman A.S.** (2015). *Pulsatile insulin secretion, impaired glucose tolerance and type 2 diabetes.* Molecular Aspects of Medicine.

3. **Lecture notes** — *Physics of Molecular Diseases, Week 4: Excitable Media (Heart Arrhythmia and Pulsatile Insulin Secretion).* Ala Trusina, Niels Bohr Institute, 2020.

---

## Notes

- The coarse-graining (b=5) groups 5 real cells into one model unit, keeping the wave front velocity constant while reducing computational cost from L=1000 to L=200.
- The model is intentionally minimal — it captures the essential physics (structure + dysfunction → reentry) without modelling ion channel kinetics, making analytical tractability possible.
- The same FHN-type dynamics apply to pancreatic β-cells: u = Ca²⁺ (fast, diffusing), v = desensitized receptors (slow, non-diffusing).
