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


## Proposed QRISP methodology revision (November 2026; NOT FROZEN)

This design is synchronized with the Method section of `04_Drafts/paper/main.tex` on the methodology draft branch. It is a proposal, not evidence of dataset access, training or completed experiments.

### Dataset and setting

- Primary: Qualcomm QRISP from Mercier et al. (ICCV 2023): https://www.qualcomm.com/developer/software/qualcomm-rasterized-images-dataset
- Grounded published modalities: 540p color (non-jittered `MipBiasMinus1`), 540p packed depth (`DepthMipBiasMinus1`), 540p motion EXR (`MotionVectorsMipBiasMinus1`), 1080p `Enhanced` target, camera metadata. 2× output: 960×540 → 1920×1080.
- Published dataset: 13 scenes, ten development scenes (7,260 frames) and three evaluation scenes (1,500 frames). Hold the latter untouched. Hold two development scenes for validation, chosen and documented before tuning. Inspect near-duplicate/asset-family overlap; do not overclaim independent content.
- Non-jittered, fixed mipmap bias in primary study; jittered condition only as an explicit separate secondary experiment. Document licenses, acquisition, hashes, file identities, frame/segment boundaries, alignment, and validity before using QRISP.
- Surface normals are **not documented** as part of QRISP. A depth-derived normal map cannot support an independent-normal hypothesis. Normal variants stay conditional until independently sourced normals are validated.

### Matrix (current RGB present in every variant)

| ID | Depth | Normals | Previous-frame RGB | Alignment | Scope |
|---|---|---|---|---|---|
| V0 | No | No | No | -- | Core |
| V1 | Yes | No | No | -- | Core |
| V2 | No | Yes | No | -- | Conditional |
| V3 | Yes | Yes | No | -- | Conditional |
| V4 | No | No | Yes | Unaligned | Core |
| V5 | No | No | Yes | Motion aligned | Core |
| V6 | Yes | No | Yes | Unaligned | Core |
| V7 | Yes | No | Yes | Motion aligned | Core |
| V8 | No | Yes | Yes | Unaligned | Conditional |
| V9 | No | Yes | Yes | Motion aligned | Conditional |
| V10 | Yes | Yes | Yes | Unaligned | Conditional |
| V11 | Yes | Yes | Yes | Motion aligned | Conditional |

Core QRISP comparisons: V1–V0 for depth, V5–V4 for alignment without depth, V7–V6 for alignment with depth, and (V7–V6)–(V5–V4) for alignment-by-depth interaction. The V6 identifier **does not** retain its meaning in the older matrix; revise any external notes before final runs. Geometry + history without motion alignment is necessary for isolating geometry-conditioned alignment.

### Model and training proposal

- Same nonrecurrent 32-feature residual CNN, four residual blocks, subpixel 2× head, bilinear RGB skip. All models have ten input slots (RGB 3 + depth 1 + normals 3 + history 3), with absent slots zeroed. Parameter count, active inputs, preprocessing time and actual runtime reported per variant.
- A single previous *low-resolution rendered RGB frame* is used as history; motion alignment is deterministic bilinear warping, not a learned extra motion input. Establish EXR channel order, motion sign/scaling and warp validity empirically. Avoid leaking history across segments; evaluate all variants on same eligible non-first frames.
- Pilot recommendation: 64×64 LR/128×128 HR crops, L1 loss, AdamW LR 1e-3, batch eight, 20k updates, validation every 1k updates, three full-run seeds. These values are provisional until a small speed/validity pilot establishes feasibility; freeze them before final test runs. No geometric flip/rotation augmentations without adjusting and testing motion vectors.
- Record dataset hash, split manifest, scene-family overlap assessment, seed, Git revision, software versions, checkpoint, and all metric/timing settings. Retain raw downloaded dataset out of Git.

### Evaluation and limitations

- PSNR (per-frame before averaging, range [0,1]) primary; SSIM complementary; bicubic non-neural baseline. Optional perceptual/temporal assessments only when their precise input and calculation conventions are fixed.
- Report per-scene and per-seed outcomes and paired differences, not naive frame-level confidence intervals. 3 held-out scenes limit statistical inference.
- Bench network-only and GPU pipeline including history warping separately, full 1080p batch-one, warmed-up CUDA event timing. No claim of UE integration or true frame-budget feasibility from offline measurements alone.
- Source: Mercier et al. supplement, https://openaccess.thecvf.com/content/ICCV2023/supplemental/Mercier_Efficient_Neural_Supersampling_ICCV_2023_supplemental.pdf (Annex A, Tables 4–5). Verify downloaded files before committing final details.

### Pilot gates

1. Access QRISP under applicable terms; inspect one segment with all core modalities and metadata.
2. Confirm 540p↔1080p pairing, depth decode, EXR channel order, motion direction/jitter conventions and image pipeline.
3. Measure V0 pilot throughput/memory; run a non-learning warp sanity check; decide feasibility of 20k updates × 3 seeds.
4. Freeze dataset scene list, splits, model, loss, metric and timing protocols before final runs, after both authors review.

---

## Historical seven-variant matrix (superseded by QRISP draft above)

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
