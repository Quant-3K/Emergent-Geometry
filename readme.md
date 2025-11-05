# Quant‑Trika VRD Experiment

> **Tagline:** *Time performs; Space remembers. Quant‑Trika measures the performance.*

This repository packages a complete, reproducible experiment that connects the **Quant‑Trika** theoretical framework to a concrete quantum‑spin model and reports rigorous statistical tests validating structure–entropy relationships. It includes: (1) concise philosophical context, (2) the mathematical apparatus, (3) engineering code and protocols, and (4) a zipped bundle of **all run artifacts** (CSV, pickles, logs, and figures) for audit and re‑analysis.

---

## TL;DR

- We run **exact diagonalization** (ED) on a 1D spin chain across sizes **N ∈ {6, 8, 10}** and transverse field strengths **g ∈ {0.5, 1.0, 1.5}** at coupling **J = 1**.
- We compute per‑site reduced‑density‑matrix entropies **S(i) [bits]**, define normalized entropy **H\_norm(i) = S(i)** (base‑2), and construct a structural profile **C(i)** (with reported raw **ZZ**, **XX**, and a normalized **C\_norm(i)** component).
- The **canonical Quant‑Trika metric** is used throughout: \(\displaystyle KQ(i) = C(i)\, [1 - H_{\text{norm}}(i)]\).
- Results show **systematic entropy suppression** with larger **g**, strong **global monotonic structure–entropy alignment** (H2, Spearman ρ up to **0.996**), and interpretable **ablation** behavior (ZZ vs XX) explaining local variations in H1.

---

## Repository Map

- readme.md - this file, a detailed repositories representation.
- Space.pdf - Philosophical essay, proposing new ontological view on space.
- EmGeom.ipynb - Notebook containing experiment's code with run results.  
- experiment\_results.zip - zipped archive with all outputs for external review



> **Reproducibility:** A single config file is saved and echoed to logs. The code seeds NumPy globally (here: **20250315**) and records filesystem paths for every artifact.

---

## Theoretical Foundations (1‑Page Snapshot)

**Quant‑Trika** treats reality as the interplay of **structure** (C) and **entropy** (H). The central invariant is the **coherence density**

\(\boxed{\;KQ = C\, (1 - H_{\text{norm}})\;}\)

which multiplies “how organized” a state is by “how far from maximal entropy” it is. High coherence demands **both** rich structure **and** constrained disorder.&#x20;

---

## Experiment Design

### Model & Parameters

- **System:** 1D spin chain with nearest‑neighbor coupling **J\_coupling = 1.0** and tunable field **g**.
- **System sizes:** `N_exact = [6, 8, 10]`.
- **Field values:** `g_values = [0.5, 1.0, 1.5]`.
- **Exact diagonalization:** Hilbert dims: **64, 256, 1024** for N=6,8,10.
- **MPS placeholder settings:** `N_mps = [32, 48, 64]`, `mps_max_bond_dim = 256` (kept for parity with larger‑N pipelines; not used in ED here).
- **Numerics & stats:** `eps_log = 1e‑8`, `eps_path = 1e‑4`, **bootstrap\_ci = 200**.
- **Randomness:** `RNG_SEED = 20250315` (recorded to logs and config).

### Workflow Cells

- **Cell A** — Environment & Reproducibility: writes `config.json`, seeds RNG, opens run logs.
- **Cell B** — Hamiltonian & Ground State (ED): builds \(H\) and computes ground state \(|\psi_0\rangle\) and energy **E0** for each (N, g). Saves `E0_summary_exact.csv` and `state_B_ground_states.pkl`.
- **Cell C** — Reduced States & Entropies: traces out to get per‑site RDMs and computes **S(i) [bits]**, with `state_C_rdm_data.pkl` and per‑config `entropies.csv`.
- **Cell D** — Structural Term **C(i)**: assembles **C\_raw\_ZZ(i)**, **C\_raw\_XX(i)**, and **C\_norm(i)**, saving `state_D_structure_profiles.pkl` and per‑config `structure.csv`.
- **Cell G** — **H1 Local Geometry Test:** locality‑sensitive correlation (per‑site ordering alignment; Spearman/Kendall with p‑values).
- **Cell H** — **H2 Global Geometry Test:** across‑chain monotonic alignment with bootstrap CIs.
- **H3** — Friedman non‑parametric tests across **g** at fixed **N**.
- **H4** — Ablations: swap structural channel (**baseline C\_norm**) with **ZZ‑only** and **XX‑only** to test attribution.

---

## Key Artifacts (paths are echoed in logs)

- `.../E0_summary_exact.csv` — table of \(E_0\), build times \(t_H\), eigensolver times \(t_{E0}\).
- `.../state_B_ground_states.pkl` — serialized ground states per (N, g).
- `.../state_C_rdm_data.pkl` — RDMs and per‑site entropies **S(i)**.
- `.../state_D_structure_profiles.pkl` — structural profiles (**ZZ**, **XX**, **C\_norm**).
- `.../state_G_h1_results.pkl`, `.../state_H_h2_results.pkl` — statistical test results.
- `.../artifact_bundle/run_artifacts.zip` — zipped bundle with the above and plots.

---

## Results

### 1) Ground‑State Energies (Cell B)

| N  | g   | E0       |
| -- | --- | -------- |
| 6  | 0.5 | −5.5220  |
| 6  | 1.0 | −7.2962  |
| 6  | 1.5 | −9.8476  |
| 8  | 0.5 | −7.6406  |
| 8  | 1.0 | −9.8380  |
| 8  | 1.5 | −13.1914 |
| 10 | 0.5 | −9.7655  |
| 10 | 1.0 | −12.3815 |
| 10 | 1.5 | −16.5353 |

**Observation R1.1 — Extensive scaling:** \(|E_0|\) increases with **N** and **g** in a near‑linear fashion over this range, consistent with an extensive ground‑state energy with a field‑dependent density.

**Observation R1.2 — Solver stability:** Hamiltonians were built in **10–376 ms**; eigensolves finished in **1–8 ms** (N≤10), indicating ample numerical headroom for larger‑N pipelines (where MPS/DMRG would replace ED).

### 2) Entropy Profiles (Cell C)

Representative first three sites (bits):

- **N=10, g=0.5:** `0.8230, 0.9365, 0.9480`
- **N=10, g=1.0:** `0.3821, 0.5677, 0.6071`
- **N=10, g=1.5:** `0.1923, 0.3140, 0.3283`

**Observation R2.1 — Entropy suppression with field:** Increasing **g** **monotonically reduces** per‑site entropy **S(i)** across all **N**. In KQ terms, for a fixed structural channel, larger **g** lifts \(1 - H_{\text{norm}}\), making coherence easier to accrue.

**Observation R2.2 — Edge‑to‑bulk gradients:** Entropy shows shallow spatial gradients; structure (next section) exhibits stronger shaping.

### 3) Structural Profiles (Cell D)

Illustrative **N=10, g=1.5** (excerpt):

| i | C\_raw\_ZZ | C\_raw\_XX | C\_norm |
| - | ---------- | ---------- | ------- |
| 0 | 0.3340     | 0.0979     | 0.0000  |
| 1 | 0.3428     | 0.0974     | 0.4066  |
| 2 | 0.3531     | 0.0968     | 0.8867  |
| 3 | 0.3551     | 0.0966     | 0.9783  |
| 4 | 0.3556     | 0.0966     | 1.0000  |

**Observation R3.1 — Monotone structural ramp:** **C\_norm** rises from edge to center, matching the intuition that “geometry is condensed history”: central sites retain more of the chain’s interaction history.

**Observation R3.2 — Channel dominance:** **ZZ** exhibits the dominant structured signal; **XX** remains comparatively flat at these parameters, a fact used in H4.

### 4) Local vs Global Alignment (Cells G & H)

We test whether “more structure implies less entropy” locally (H1) and globally (H2).

- **H2 (Global):** Spearman ρ is **very high** at **low g** across all **N** (e.g., **ρ≈0.996** for N=10, g=0.5; CIs ≈ [0.984, 0.998]). As **g** increases, ρ decreases but remains **strong** (e.g., N=10, g=1.5 → **ρ≈0.743**, CI ≈ [0.562, 0.875]). **Kendall τ** shows the same trend.
- **H1 (Local):** More variable across (N, g): for some settings (N=8, g=1.0) we see **ρ≈0.943**/**τ≈0.867**, while others (N=10, g=1.5) drop near **0**. This suggests the local ordering can flip in strongly polarized regimes even while the global monotone relationship stays intact.

**Interpretation R4 — Two‑scale law:** The data support a **two‑scale alignment law**: global monotonic alignment between **C\_norm** and \(1−H_{\text{norm}}\) is **robust**, while **local** (site‑to‑site) orderings can be sensitive to microscopic channel competition (cf. ablations) and boundary effects.

### 5) Across‑g Comparisons (H3, Friedman)

Non‑parametric Friedman tests across **g** at fixed **N** yield **p≈0.368** for both H1/H2 ρ‑scores, i.e., **no significant difference** detected with this small sample. This is **not** in tension with R4: given only three g‑levels and per‑chain sample sizes, the test lacks power; confidence intervals in H2 already capture the visible trend.

### 6) Structural Channel Ablations (H4)

We re‑compute H1/H2 after substituting **C\_norm** with **C\_ZZ** or **C\_XX**.

- **Global stability:** H2 stays **very high** in most cases for **ZZ** and only slightly lowers for **XX**, confirming that **global** structure–entropy alignment is a channel‑agnostic phenomenon.
- **Local sensitivity:** H1 responds sharply to channel choice. Examples:
  - **N=10, g=0.5:** H1 baseline **0.738** → **C\_XX: 0.976** (local order sharpened by the XX projection at low g).
  - **N=10, g=1.0:** H1 baseline **0.738** → **C\_XX: 0.381** (local order degraded; XX under‑represents the relevant structure at this field).
  - **N=8, g=1.0:** H1 baseline **0.943** vs **C\_ZZ: 0.886**, **C\_XX: 0.543**.

**Interpretation R6 — Attribution:** **ZZ** carries the principal structural signal underpinning KQ at these parameters; **XX** can either enhance or dilute **local** ordering depending on proximity to field‑driven polarization. This explains the H1 variance while H2 remains robust.

---

## Quant‑Trika Reading Guide (for this repo)

- **Canonical invariant:** \(KQ = C(1 - H_{\text{norm}})\) — used directly to interpret metrics and ablations.
- **MIS‑1 (Coherence Continuity):** motivates entropy suppression ↔ structure amplification and gradient‑flow interpretations of the observed smoothing.
- **MIS‑4 (Lyapunov Indicator):** provides stability criteria consistent with entropy decrease at larger **g**.
- **MIS‑5 (Consensus & Geometry):** frames the strong H2 behavior as network‑geometric consensus between structure and low entropy.
- **MIS‑6 (Predictive Alignment):** connects high‑KQ regimes with efficient information budgets and improved predictability; relevant for choosing channels (ZZ vs XX) in downstream tasks.

---

## How to Reproduce

1. **Environment:** Python 3.10+, NumPy, SciPy, and standard scientific stack.
2. **Run:** `python src/run_vrd_experiment.py --config configs/default.json` (or Colab notebook, if provided).
3. **Outputs:** Check `/runs/<stamp>/...` paths echoed in the logs; or unpack `/artifact_bundle/run_artifacts.zip`.
4. **Audit:** Open `E0_summary_exact.csv`, `entropies.csv`, and `structure.csv` per config and recompute H1/H2 with the included scripts.

---

## What This Validates

- **Empirical support** for Quant‑Trika’s core claim that **coherence** increases when **structure aligns with entropy suppression**.
- **Separability of scales:** Global monotone relationships (H2) can hold even when local orderings (H1) fluctuate, matching the theory’s two‑scale geometry.
- **Attribution clarity:** ZZ carries the dominant structural footprint for KQ under these settings; XX acts as a context‑dependent modulator.

---

## Limitations & Next Steps

- **Finite‑size:** ED is limited to small **N**; extend with DMRG/MPS using the provided MPS hooks.
- **Critical sweep:** Densify **g** around the known critical region to map the H1 drop‑off and H2 decay more precisely.
- **Channel portfolio:** Add **YY** and mixed correlators; learn an optimal **C(i)** as a weighted combination maximizing H2 while regularizing H1.
- **Uncertainty:** Increase **bootstrap\_ci** and add cross‑seed runs to tighten CIs.

---

## Glossary

- **C(i):** Structural profile (normalized from raw correlators).
- **H\_norm(i):** Normalized local entropy (bits).
- **KQ(i):** Coherence density at site i.
- **H1/H2:** Local/global geometry alignment tests between structure and entropy suppression.
- **Ablation (H4):** Replace **C\_norm** with **C\_ZZ**/**C\_XX** to attribute effects.

---

## Contact & Attribution

- **Authors:** Quant‑Trika Project.
- **Questions:** Please open an issue with the config, log excerpt, and the relevant CSV/PKL attached.
- **Use:** Cite this repository if you use the metrics, tests, or artifacts in academic work.

