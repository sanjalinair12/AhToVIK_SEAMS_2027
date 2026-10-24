# AhToVIK: Evidence-Gated Runtime Adaptation for Heterogeneous Multi-Robot Systems

Replication package for the SEAMS 2026 submission *"Knowledge-Driven Runtime Adaptation with Evidence-Gated Recovery for Heterogeneous Multi-Robot Systems"*.

AhToVIK is a MAPE-K framework for heterogeneous robot teams (UAVs, UGVs and confined-space crawlers). It ranks eligible robots by geometric compromise (AHP weights, ideal-reference distances, modified VIKOR index), forms only complete feasible teams, and uses an evidence gate to decide whether to retain a team, reconfigure the incumbent, replace a robot, request a bounded diagnostic, or escalate. Verified recovery outcomes are stored in a versioned knowledge base and reused to order later diagnostics.

## Contents

| File | Description |
|---|---|
| `AhToVIK_Validation_Suite.ipynb` | Full implementation of AhToVIK, all baselines, the simulated search-and-rescue benchmark, and the analysis that produces the paper's tables and figures |
| `README.md` | This file |

## Requirements

- Python 3.10 or later
- `numpy`, `pandas`, `matplotlib`
- `scipy` 1.9 or later (uses `scipy.optimize.milp` with the HiGHS solver)

```bash
pip install numpy pandas matplotlib "scipy>=1.9" jupyter
```

The notebook also runs on Google Colab without extra setup.

## Running the experiments

1. Open `AhToVIK_Validation_Suite.ipynb` in Jupyter or Colab.
2. In the first code cell, choose the run size:
   - `SMOKE = True`: short end-to-end check (about 5 minutes).
   - `SMOKE = False`: full evaluation with 500 paired runs per case, as reported in the paper.
3. Run all cells in order.

All runs are seeded (`MASTER_SEED = 20261022`) and paired across methods: every method sees the same fleet, fault schedule and telemetry noise for a given seed. Each case is cached in `results/cache/`, so an interrupted run resumes where it stopped when the notebook is re-run. Delete the cache folder to force a fresh run.

## Mapping to the paper

| Paper | Notebook | What is measured |
|---|---|---|
| RQ1: evidence-gated intervention | Part 2, dynamic resilience benchmark | Repeated ineffective interventions, unnecessary reassignments, discarded work, on-time completion, recovery downtime across ten disturbance scenarios |
| RQ2: complete-team formation | Part 1, static decision benchmark | Complete-team rate and role coverage under robot scarcity (0.8x to 10x supply), decision latency, scalability up to 10,000 robots |
| RQ3: knowledge reuse | Part 3, knowledge-loop benchmark | Recovery latency and diagnostic effort with reused vs frozen knowledge for recurring, nonrecurring, drifting and misleading failure patterns |

The notebook also contains sanity checks (section I), which should all pass before any result is used.

## Methods compared

**Dynamic (RQ1, RQ3):** AhToVIK, AhToVIK with frozen knowledge, AhToVIK without the evidence gate (no-gate ablation), MARKET, RAINBOW, MAYYA, CALVO, FILIPPONE, ESFAHANI, CARWEHL, CAMARA and RODRIGUES.

**Static (RQ2):** AhToVIK, AhToVIK without the gate, AhToVIK with partial staffing, CO-MILP, SAW/TOPSIS/VIKOR-greedy, trait-coverage matching, sequential single-item auction (SSI) and random assignment.

Baselines are compact re-implementations of each approach's adaptation or allocation mechanism, not the original authors' code. All methods run on the same simulated plant, telemetry, checkpoint and handover model, so differences between them come from the decision logic.

## Benchmark scenarios

The dynamic benchmark uses 28 robots, noisy telemetry (σ = 0.05), a task deadline at step 115, and ten disturbance scenarios:

`noise_only`, `transient`, `degrade`, `hard`, `silent`, `restriction`, `isolated`, `stale_competing`, `cascade`, `mixed`

Diagnostic probes distinguish two causes (zone/mode restriction vs isolated robot fault) with 95% accuracy.

## Outputs

Everything is written to `results/`:

- CSV files with per-run and aggregated results
- LaTeX tables
- Figures
- `ahtovik_validation_results.zip`, a single archive of all outputs (downloaded automatically on Colab)

## Limitations

The evaluation runs in a synthetic simulator with idealized handover, and the diagnostic model recognizes only two predefined causes. Results quantify trade-offs in this setting and do not imply performance on physical robots.
