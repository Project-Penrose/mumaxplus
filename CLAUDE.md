# mumaxplus — CLAUDE.md

Extensible GPU-accelerated micromagnetics simulator (C++/CUDA + Python interface). The next-generation simulation engine for Project-Penrose, used for Gen2 sYIG characterization campaigns, MCEL-2.0 cell library parameter extraction at 8.4 GHz, magnon-phonon coupling studies, and multi-fidelity co-simulation with the Venetian digital twin stack.

## Repository Layout

| Path | Purpose |
|------|---------|
| `mumaxplus/` | Core Python interface and simulation API |
| `src/` | C++ and CUDA source — LLG solver, magnon-phonon coupling, STDP synapse |
| `docs/` | Documentation (Sphinx) — tutorials, API reference |
| `examples/` | Example simulation scripts — spin-wave, SOT-MRAM, magnon BEC, parametric amp |
| `test/` | pytest test suite |
| `CMakeLists.txt` | CMake build configuration |
| `pyproject.toml` | Python package definition |
| `environment.yml` | Conda environment for reproducible builds |
| `setup.py` / `setup.cfg` | Legacy setup support |
| `paper2025/` | Companion scripts for Nature npj Comput. Mater. paper (v1.1.0 tag) |

## Common Tasks

**Install (development):**
```bash
conda env create -f environment.yml
conda activate mumaxplus
pip install -e .
```

**Run a simulation:**
```python
import mumaxplus
# See docs/ and examples/ for simulation scripts
```

**Run tests:**
```bash
pytest test/ -v
```

**Build documentation:**
```bash
cd docs && make html
```

## MagnonOS-Specific Usage

mumaxplus is the **primary** simulation backend for Gen2 sYIG characterization. It is invoked by:

- **`CoSimulationTrigger` agent (port 9277)**: Dispatches mumaxplus simulation jobs via `project-penrose-k8s/base/07-mumaxplus-job-template.yaml` (GPU Job template).
- **`SimulationCampaignManager` agent (port 9360)**: Manages multi-parameter sYIG characterization campaigns (3-lot qualification per SPEC-POINCARE-023).
- **`FrequencySweepOrchestrator` agent (port 9317)**: Orchestrates multi-point frequency sweeps at 8.4 GHz for MCEL-2.0 characterization (ADR-133).

### Key simulation campaigns:

| Campaign | Target Spec | Metrics |
|----------|------------|---------|
| sYIG 3-lot D₀ characterization | SPEC-POINCARE-023 | Ms, α, D_ex, roughness, D₀ |
| MCEL-2.0 characterization (8.4 GHz) | SPEC-VOLTERRA-025 | 20 cells × 15 PVT corners |
| Magnon-phonon coupling | SPEC-POINCARE-021 | α(T) model, magnetoelastic coupling |
| Parametric amplifier Suhl safety | SPEC-MAGNONIC-043 | P_th=0.64 mW calibration (ADR-126) |
| Multi-fidelity L1 co-simulation | SPEC-VENETIAN-043 (Gen2 DTSC v2) | DS-2 L1 fidelity criterion |

## Key Invariants

- **Suhl instability safety (ADR-126):** All pumping power simulations must verify P_amp < P_th × 0.8 = 0.64 mW. Results above this threshold trigger a `MSG_DRIFT_DETECTED` event from `MagnonNoiseAnalyzer`.
- **SYNTH_YIG_PARAMS_V1 binding:** Simulations targeting Gen2 sYIG must use the normative Ms/α/D_ex values from the SYNTH_YIG_PARAMS_V1 Mouseion registry (ADR-141). Do not hard-code material parameters.
- **Neuromorphic safety (ADR-128):** STDP synapse simulations are simulation-only until PO-NEURO-001 is proved. Do not claim hardware availability. All STDP weights must be consent-gated per ADR-085 if BCI data is used.
- **No public publishing.** mumaxplus is a private fork. Do not publish to PyPI or any public registry.
- **SPECIFICATION_GAP axiom discharge:** Simulations that generate evidence for SPECIFICATION_GAP axiom closure in PO-PENROSE-012/013 must produce Mouseion-registered `SimulationResult` assets with W3C PROV provenance chain (ADR-093).

## Integration Points

| Counterpart | Direction | Purpose |
|-------------|-----------|---------|
| `agents/co_simulation_trigger` | Orchestrator | Dispatches mumaxplus GPU jobs |
| `agents/simulation_campaign_manager` | Orchestrator | Campaign lifecycle management |
| `agents/frequency_sweep_orchestrator` | Orchestrator | Frequency sweep coordination |
| `project-penrose-k8s` | Runtime | GPU Job template for cluster execution |
| Poincaré (SYNTH_YIG_PARAMS_V1) | Parameter source | Normative sYIG material parameters |
| Venetian (Digital Twin) | Consumer | L1 fidelity simulation data for DT calibration |
| Volterra (MCEL-2.0) | Consumer | Cell library characterization at 8.4 GHz |

## Upstream Reference

Based on: https://mumax.github.io/plus/
Paper: https://www.nature.com/articles/s41524-025-01893-y (npj Comput. Mater. 2025)
Canonical version: v1.1.0 (paper2025 tag) and ongoing development.
