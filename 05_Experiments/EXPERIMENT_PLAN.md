# Experiment Plan (Design Draft)

This page describes how the future study should be designed. It is **not** a claim that experiments, datasets, code, or results already exist. Agree on the choices below with both authors before implementation.

## Study objective

Compare selected auxiliary inputs for a lightweight neural upscaler under a common real-time rendering protocol, examining reconstruction quality, runtime and memory, held-out scene variation, and spatial benefits and failures. The matrix below distinguishes individual and combined spatial inputs, history alignment, and the joint addition of geometric inputs to aligned history; it does not isolate every input or interaction.

## Decisions to freeze before code

| Decision | What to specify | Status |
|---|---|---|
| Model and baseline | Architecture, parameter budget, RGB-only baseline, non-neural comparator if feasible | Open |
| Input candidates | Depth, motion vectors, normals, history, and which are actually available in the renderer | Open |
| Temporal setting | Temporal extension: history representation, alignment, motion handling, and frame resets | Open |
| Data | Scenes, rendering pipeline, train/validation/test split, reference images | Open |
| Image setup | Input/output resolution, scale factor, color space, exposure, antialiasing | Open |
| Training | Losses, optimization, budget, seeds, stopping rule | Open |
| Metrics | Main quality metric, perceptual/temporal measures, latency, memory, model size | Open |
| Hardware | Target GPU and software environment | Open |
| Success criterion | How to interpret effects and uncertainty across the analysis topics | Open |

## Proposed ablation matrix

V0 is the color-only single-frame baseline; V1–V3 add depth and normals individually and together. The temporal extension adds history to color, compares unaligned and motion-aligned history, then adds depth and normals together. All variants are proposed, subject to buffer availability and final protocol agreement.

| Variant | Color | Depth | Normals | History | Purpose |
|---|---|---|---|---|---|
| V0 | Yes | No | No | No | Single-frame baseline |
| V1 | Yes | Yes | No | No | Depth relative to V0 |
| V2 | Yes | No | Yes | No | Normals relative to V0 |
| V3 | Yes | Yes | Yes | No | Combined spatial inputs |
| V4 | Yes | No | No | Unaligned | Unaligned history relative to V0 |
| V5 | Yes | No | No | Motion aligned | Alignment relative to V4 |
| V6 | Yes | Yes | Yes | Motion aligned | Joint depth and normal addition relative to V5 |

V3 versus V1 and V3 versus V2 estimate the incremental contribution of normals with depth and depth with normals, respectively. V5 versus V4 tests alignment only if history and temporal processing are otherwise matched. V6 versus V5 tests the two geometric inputs together; V6 versus V3 compares the combined temporal variant with the single-frame counterpart. The matrix does not isolate motion vectors without history, separate depth or normal effects in aligned history, or every interaction.

Use scene-disjoint training, validation, and test splits; reserve test frames from fitting and protocol selection. Use the same evaluation scenes and timing boundaries across variants. Hold training data and budget, loss, optimizer, and schedule constant where applicable. Input encoding, normalization, channel count, and processing must be specified. A shared base architecture may still change parameter count or processing across variants: control capacity where feasible, report parameter counts and complexity, and acknowledge residual differences when interpreting results. The architecture, temporal implementation, hardware, scale, and number of seeds remain open.

## Provisional evaluation protocol

No metrics have been run. Finalize these choices after fixing the image pipeline and before evaluating test scenes:

- **PSNR and SSIM:** candidate full-reference framewise measures of pixel fidelity and local structure, respectively. Neither establishes human-perceived quality or temporal stability. Fix implementation/version, reference alignment and synchronization, crop and invalid pixels, color representation and range, exposure and tone mapping, channels, PSNR peak and whether to average before or after the logarithm, and SSIM window and scale.
- **FLIP:** candidate spatial perceptual difference maps. If FLIP is selected, consider LDR-FLIP for appropriately prepared display-ready LDR images, or the distinct HDR-FLIP for HDR inputs, which combines maps over exposed and tone-mapped image pairs. Fix implementation/version, input representation, alignment, viewing/display assumptions, map pooling and, for HDR, exposure range, sampling and tone mapping. Maps predict differences rather than prove human preference. Sources: [LDR-FLIP](https://doi.org/10.1145/3406183), [HDR-FLIP](https://doi.org/10.2312/egs.20211015).
- **LPIPS:** optional learned-feature perceptual distance, included only if it provides useful complementary information for the chosen rendering pipeline. Fix implementation/version, backbone and weights, normalization, input color representation, alignment and pooling. It does not establish human preference; framewise LPIPS and FLIP do not measure temporal stability. [Original LPIPS paper](https://openaccess.thecvf.com/content_cvpr_2018/html/Zhang_The_Unreasonable_Effectiveness_CVPR_2018_paper.html).
- **Temporal stability:** consider measuring pixel-wise temporal variation on verified static segments alongside the reference, as in the restricted static-segment analysis of [Mercier et al.](https://openaccess.thecvf.com/content/ICCV2023/papers/Mercier_Efficient_Neural_Supersampling_on_a_Novel_Gaming_Dataset_ICCV_2023_paper.pdf), and inspect synchronized reference/output videos for moving scenes. Fix static-segment criteria, frame rate, duration, reset handling, clip selection and playback conditions. Static variation cannot characterize motion, and blur can appear stable. Consider a motion-compensated temporal error only if reliable correspondence and visibility masks are available; fix motion source, warp, exclusions and implementation independently of variants, report coverage, and account for errors from motion, occlusion and lighting. Neither selected clips nor a single score establishes general perceptual temporal quality.

For all measures, fix reference alignment, frame synchronization, preprocessing, metric version and aggregation across pixels, frames, sequences, scenes and seeds. Report scene-level results and summary weighting, and use consistent settings across variants. Choose representative crops and clips showing edges, thin structures and disocclusions as descriptive examples. For systematic regional measurements, define independent masks and selection rules before test analysis, report region counts and coverage, and account for metrics whose windows cross mask boundaries. Without reliable masks, do not present selected crops as regional statistics.

Metric computation, reference preparation and inspection are evaluation overhead, not deployment latency. Separately specify whether deployment timing covers inference, buffer preparation, warping, storage and transfers; document hardware, warm-up, measurement boundaries and unmeasured costs. Runtime and memory remain provisional until the protocol is frozen.

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
