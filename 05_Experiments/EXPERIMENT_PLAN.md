# Experiment Plan (Design Draft)

This page describes how the future study should be designed. It is **not** a claim that experiments, datasets, code, or results already exist. Agree on the choices below with both authors before implementation.

## Study objective

Estimate how each selected auxiliary rendering input changes reconstruction quality and runtime cost for a lightweight neural upscaler under a common real-time rendering setup.

**Primary question:** Under the same model, data, training budget, and evaluation conditions, what is the effect of adding each auxiliary input to a baseline upscaler?

**Candidate secondary questions:** Do combinations of inputs interact? Are gains scene-dependent? Does an input remain useful after accounting for preprocessing, memory traffic, and end-to-end latency?

## Decisions to freeze before code

| Decision | What to specify | Status |
|---|---|---|
| Model and baseline | Architecture, parameter budget, RGB-only baseline, non-neural comparator if feasible | Open |
| Input candidates | Depth, motion vectors, normals, history, and which are actually available in the renderer | Open |
| Temporal setting | Single frame or temporal; history and motion handling; frame resets | Open |
| Data | Scenes, rendering pipeline, train/validation/test split, reference images | Open |
| Image setup | Input/output resolution, scale factor, color space, exposure, antialiasing | Open |
| Training | Losses, optimization, budget, seeds, stopping rule | Open |
| Metrics | Main quality metric, perceptual/temporal measures, latency, memory, model size | Open |
| Hardware | Target GPU and software environment | Open |
| Success criterion | What result would answer the primary question, including uncertainty | Open |

## Proposed ablation matrix

Start from the smallest controlled comparison. Expand only after checking cost and data availability. Every row needs the same evaluation scenes and timing method.

| Variant | RGB | Depth | Motion | Normals | History | Purpose |
|---|---|---|---|---|---|---|
| A0 | Yes | No | No | No | No | Common baseline |
| A1 | Yes | Yes | No | No | No | Depth contribution |
| A2 | Yes | No | Yes | No | No | Motion contribution, if temporal setup supports it |
| A3 | Yes | No | No | Yes | No | Normal contribution |
| A4 | Yes | Yes | Yes | No | No | Example interaction; confirm after single-input runs |
| A5 | Yes | To decide | To decide | To decide | Yes | History effect, only if the model is temporal |

The matrix is illustrative. Remove unavailable or scientifically invalid variants before implementation. Input normalization, channel count, and model capacity must be considered so comparisons remain interpretable.

## Planned workflow

1. Review literature and choose defensible baselines and metrics.
2. Write the final protocol and freeze data splits and comparison conditions.
3. Build a tiny pilot to validate rendering buffers, alignment, and timing instrumentation.
4. Run the baseline and each selected ablation with the agreed training budget and seeds.
5. Evaluate image quality, visual failures, runtime, memory, and model size on held-out scenes.
6. Analyze effect sizes and uncertainty; report negative as well as positive results.
7. Link every figure/table in the paper to its run IDs and source data.

## Future repository layout (do not create until needed)

```text
05_Experiments/
  EXPERIMENT_PLAN.md
  EXPERIMENT_MATRIX.csv
  RUN_LOG.csv
  configs/
  results/                 # small, selected summary tables only
code/                     # implementation and tests
figures/                  # source plots and selected paper figures
```

Large datasets, checkpoints, and raw outputs should live outside ordinary Git history, with stable links and checksums recorded in the run log.

## Gate before the first full run

Write the reproducibility policy: configuration format, Git commit, seeds, data version/split, GPU and software versions, timing method (including warm-up), metric implementations, checkpoint location, and result naming. Have both authors review it before collecting final results.
