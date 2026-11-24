# QRISP experiment plan

This is the proposed protocol for the [working manuscript](../04_Drafts/paper/main.tex). The counts below come from the published dataset inventory. Raw files, training throughput and reconstruction quality still need to be measured.

## Dataset and split

Use QRISP’s non-jittered 540p `MipBiasMinus1` RGB, `DepthMipBiasMinus1` depth and `MotionVectorsMipBiasMinus1` motion with the same frame’s 1080p `Enhanced` target. Build a manifest keyed by scene, segment, frame ID and capture condition. Preserve the documented ten-development/three-test partition.

| Proposed split | Scene names from the publication | Segments | Frames | Eligible consecutive pairs |
|---|---|---:|---:|---:|
| Train | FloodedGrounds, FloodedGroundsBridges, SciFiFacility, CB-Apocalypse, SunTemple, SunTempleBush, SunTempleLamps | 160 | 4,800 | 4,640 |
| Validation | SciFiBase, ScifiBaseStartStop, ScifiBaseNightStartStop | 82 | 2,460 | 2,378 |
| Published test | AbandonedSchool, SpaceShipDemo, Seaport | 5 | 1,500 | 1,495 |

Holding the entire SciFiBase environment family for validation keeps the base, night and stop/start captures together. Individual assets recur across environments. Use the published names as labels and record the actual archive paths in the manifest. Fit weights on training scenes, select checkpoints on validation scenes, and score the held-out test set after fixing the protocol. Verify expected counts against intact adjacent frame IDs. Record every exclusion and apply it to all variants.

Mercier et al. give the modality encodings, filenames by resolution, segment lengths and asset lists in [Annex A and Tables 4–6](https://openaccess.thecvf.com/content/ICCV2023/supplemental/Mercier_Efficient_Neural_Supersampling_ICCV_2023_supplemental.pdf). The distributor provides [the dataset and terms](https://www.qualcomm.com/developer/software/qualcomm-rasterized-images-dataset) and [per-scene archive instructions](https://www.qualcomm.com/content/dam/qcomm-martech/dm-assets/documents/qualcomm-rasterized-images-download-instructions_081624.pdf).

## Dataset alternatives reviewed on 19 November 2026

The current QRISP protocol remains the reproducible six-variant option. **STSS is the preferred replacement if independent normals are central and the 2× scale is retained.** RDG is the alternative if more varied scenes justify a change to 4×. The published archives and available evidence differ.

| Candidate | Evidence and fit | Consequence for this experiment |
|---|---|---|
| STSS, He et al. (AAAI 2024) | Unreal, 960×540 rendered RGB to 1920×1080 reference, with LR normals, depth and motion. The authors' loader reads all three signals. The ModelScope manifest lists training and validation archives for Lewis, Subway and SunTemple, and test archives for all four scenes including Arena. | Use the supersampling stream. In the Lewis training index the same ID names a `NoWarp` supersampling frame, a `Warp` extrapolation frame, and HR RGB. The two files are **different tasks**, so alignment on/off must be constructed from the same pair of original supersampling RGB frames. The project estimates ~150 GB per training sequence and ~40 GB per test scene. [Paper](https://doi.org/10.1609/aaai.v38i3.27982) · [Release and sizes](https://github.com/ryanhe312/STSSNet-AAAI2024) · [Split manifest](https://modelscope.cn/datasets/ryanhe312/STSSNet-AAAI2024/resolve/master/STSSNet-AAAI2024.json) · [Loader](https://github.com/ryanhe312/STSSNet-AAAI2024/blob/005e9b7c45e0cc89f1bfea11ff808d392bbcfb9d/dataloaders.py) |
| RDG, Zheng et al. (CVPR 2025) | Blender Cycles, 180 training clips of 100 frames, eight test scenes. Published 480×270 / 1920×1080 pair. The released configuration enables LR normal, depth and motion. | Strong full-matrix candidate at 4×. Change the pixel-shuffle factor to 4 and the head to 48 channels, HR crop to 256×256 for a 64×64 LR crop, and re-establish throughput. The loader uses numbered, pre-extracted training patches. Check full-frame source or adequate warp margins, map clip IDs to scenes and carve validation from training scenes. The release links Baidu Cloud. Archive contents and access were not independently verified. [Paper](https://openaccess.thecvf.com/content/CVPR2025/papers/Zheng_Efficient_Video_Super-Resolution_for_Real-time_Rendering_with_Decoupled_G-buffer_Guidance_CVPR_2025_paper.pdf) · [Release](https://github.com/max-well-d/RDG) · [Training configuration](https://github.com/max-well-d/RDG/blob/8d6b21f5b96c01aa0ee5c88c934d2ba756a274ef/options/train/train_RDG-s_x4.yml) |
| GameIR-SR, Zhou et al. (ACCV Workshops 2024) | Paired 960×720 / 1920×1440 CARLA sequences, with depth and segmentation. Full and mini splits are hosted publicly. | Suitable for a 2× cross-domain depth check. Independent normals and renderer motion are undocumented. Estimated optical flow would change the alignment treatment. [Paper](https://openaccess.thecvf.com/content/ACCV2024W/RichMediaGAI/papers/Zhou_GameIR_A_Large-Scale_Synthesized_Ground-Truth_Dataset_for_Image_Restoration_over_ACCVW_2024_paper.pdf) · [Dataset](https://huggingface.co/datasets/LLLebin/GameIR) |
| Arm Neural Graphics Dataset, NSS subset | Public Unreal Bistro sequence tensors include linear RGB/target, depth, motion and jitter. Train/validation captures are cropped. | Useful for a jitter/registration check. A single environment and no listed NSS normal tensor limit its use for the full study. [Dataset card and tensor schema](https://huggingface.co/datasets/Arm/neural-graphics-dataset) |
| NSRD, Li et al. (CVPR 2024) | Unity sequences with LR normals, depth and motion. The authors link three scenes on OneDrive. Supervision is a lighting component, with material re-modulation for final color. | More work to establish paired final RGB for all splits. Training patches and material inputs change the comparison. [Paper, §4.1](https://openaccess.thecvf.com/content/CVPR2024/papers/Li_Neural_Super-Resolution_for_Real-time_Rendering_with_Radiance_Demodulation_CVPR_2024_paper.pdf) · [Release](https://github.com/Riga2/NSRD) |
| USMM, Brandner (2024) | UE5.4, four environments, two sequences per environment, 540p and 1080p inputs and a 4K target. The author distributes a capture plugin and withholds the 550 GB capture. | A route to generating new data if the project expands to own-engine capture. [Thesis and availability](https://github.com/BrandnerKasper/URTSR) |

**STSS pilot decision.** Before switching the main protocol, decode consecutive supersampling records from a training sequence and match each with its own HR target. Preserve raw RGB and the normal triplet. The seven-channel packer published with *Frozen History* keeps only RGB, depth and motion. Verify normal encoding/space, color order and range, displacements across the selected render instants, cuts, and disk cost. Build unaligned history from the preceding real render and apply the verified warp to exactly that image for the aligned condition. A pilot passes when object landmarks reproject as expected, the normal field follows surfaces, LR/HR IDs match, and the stored records can be used under the distribution terms. [Training index](https://modelscope.cn/datasets/ryanhe312/STSSNet-AAAI2024/resolve/master/Lewis_train1.csv) · [Seven-channel packer](https://github.com/CV-AC/Frozen_history/blob/e61df89715d8363922c1dcec99035e123c860f01/scripts/pack_stss.py)

If STSS becomes primary, an example scene-disjoint assignment is Lewis and Subway for training, SunTemple for validation, Arena for the held-out test. This yields only one independent test environment. Report that limitation or predeclare additional held-out-scene runs and their compute cost. Recalculate eligible pairs, image decoding, motion conventions, metric range and throughput from STSS itself. The twelve variants with three seeds require 36 runs (720,000 updates at the draft budget). Within the normal rows, V9−V8 and V11−V10 measure alignment with normals and with depth plus normals. Compare these to V5−V4 and V7−V6 to estimate how adding normals changes the alignment gain without and with depth. Report each dataset separately if a second source is used.

## Ablation matrix

Current RGB is always present. The column describes only the immediately preceding LR RGB. Geometry is always from the current frame.

| Current geometry | No history | Unaligned history | Motion-aligned history | Scope |
|---|---|---|---|---|
| None | V0 | V4 | V5 | QRISP |
| Depth | V1 | V6 | V7 | QRISP |
| Normals | V2 | V8 | V9 | Separate extension |
| Depth and normals | V3 | V10 | V11 | Separate extension |

**ID change from the earlier seven-variant table.** V0–V5 keep their earlier meanings. The earlier V6 (depth + normals + aligned history) is **V11** here, and **V6 now means depth + unaligned history**. Do not compare results labelled V6 across protocol revisions; identify runs by protocol revision as well as variant. Independent normals would require matching captures and same-source controls for every comparison. Depth-derived normals would answer a different question about depth encoding.

| Quantity (scene-balanced PSNR in dB) | Matched contrast |
|---|---|
| Depth benefit with absent / unaligned / aligned history | V1 − V0 / V6 − V4 / V7 − V5 |
| Benefit of adding one unaligned frame without / with depth | V4 − V0 / V6 − V1 |
| Benefit of alignment without / with depth | V5 − V4 / V7 − V6 |
| Change in alignment benefit when depth is present | (V7 − V6) − (V5 − V4) |

Compute each difference within the same scene and seed before averaging. The manuscript states the expectations as H1–H4:

- **H1:** depth improves single-frame reconstruction (V1 − V0 > 0).
- **H2:** alignment improves temporal reconstruction without and with depth (V5 − V4 > 0 and V7 − V6 > 0).
- **H3:** depth changes the alignment gain; estimate (V7 − V6) − (V5 − V4) without prespecifying its sign.
- **H4:** the H1 and H2 effects have the same sign on each of the three test scenes.

The earlier hypotheses about normals (normals improve quality; depth and normals are partly redundant) move to the normal extension.

Jitter is off for every core row. A secondary experiment could retrain V4 and V5 using the jittered versions of all corresponding LR buffers, the same Enhanced target, splits and seeds. Compare the alignment gain against the non-jittered gain. The CNN receives no explicit offset channels. Check relative jitter in the motion field before applying any correction. No model carries a recurrent state between frames.

## Preparation and training

1. Acquire a training scene (for example CB-Apocalypse) using the distributor’s per-scene instructions. Record the archive hashes, expanded size, segment IDs and package versions. Store raw data and checkpoints outside Git.
2. Read stored RGB bytes in RGB order, discard color alpha and divide by 255. Decode packed depth using all four byte channels per Annex A, Eq. 4. Keep the resulting near-to-far values. Check paired frame identities, crop alignment, byte order, motion finiteness and depth range on training captures. Investigate unexpected values before freezing the manifest.
3. Decode vertical then horizontal EXR velocities and scale by the corresponding LR dimension. Verify axis sign and current-to-previous source coordinates using trackable training-scene motion. Test identity and one-pixel translations on a synthetic grid. Bilinearly sample full-frame previous RGB with half-pixel centers and replicated borders before cropping. Supply no motion-derived mask, depth dilation or raw velocity channels to the CNN.
4. Use the ten-slot, 32-channel residual CNN specified in the manuscript (four blocks, PixelShuffle 2× and a bilinear RGB skip). Omitted slots contain zeros. Draw eligible training pairs uniformly with replacement, then a uniform valid 64×64 LR crop and its corresponding 128×128 HR target. Save the draws and use identical initial weights and draws across variants for each of seeds 17, 29 and 43.
5. Train independently using mean L1 on unclipped output, AdamW at constant 0.001, betas (0.9, 0.999), epsilon 1e−8 and weight decay 0.01. Proposed batch size is eight, with 20,000 updates and full validation every 1,000. Select the highest mean validation-scene PSNR with ties going to the earlier checkpoint.
6. Pilot both V0 and V7 in FP32 on the target GPU to include the cost of motion I/O, warping and validation. Record the GPU model with the pilot results rather than in the protocol. For each pilot record peak allocated memory, updates/s, validation wall time and I/O/cache settings. If the pilot can afford it, train V0 with all three seeds to estimate the seed-to-seed spread in validation PSNR (see the open decision on power below). Estimate time for 18 training runs (360,000 updates plus 360 validation passes). Apply any resource-driven budget or precision adjustment to every variant before final training.

## Evaluation and reporting

- Score the same eligible full frames, including borders and UI, with predictions clipped to [0,1] and references in the stored RGB encoding. Include bicubic interpolation using half-pixel centers. Average RGB PSNR in dB over frames within scenes, then give each test scene equal weight. Secondary SSIM uses range one, 11×11 valid Gaussian windows, sigma 1.5, standard constants, population covariance and channel averaging.
- Report matched RGB MAE within and outside depth discontinuities. Take the largest absolute difference to any in-bounds four-neighbor at LR, threshold strictly above the training-pixel 90th percentile, and replicate each label to its corresponding HR pixels. Publish coverage per scene and use the same mask across variants.
- For temporal error, subtract the reference RGB change from the output RGB change over consecutive scored frames. Average absolute values over pixels and channels, then frames within each scene and scenes equally. This leaves 1,490 test transitions if the inventory is intact. Report alongside synchronized clips since ordinary spatial error on moving edges also affects this score.
- Show scene-by-seed values, matched contrasts, and the mean and range across seeds. Report all three scenes individually. Do not treat adjacent frames as independent replicates.
- Time full-frame batch-one inference and the resident-buffer pipeline separately. The pipeline includes input assembly, any motion conversion and warping, network execution and clipping. Report CUDA-event median and p95 across 200 fixed frames after 50 warm-ups, peak allocated memory after a warm-up reset, and the GPU, driver, software and precision. Measure each pipeline in isolation. Disk, CPU transfers, rendering and quality metrics are outside these timings.
- Save run IDs, Git SHA, input hashes, split/crop manifests, environment, validation trace, selected checkpoint and raw frame-level scores alongside the summaries. Offline timings do not establish an in-engine frame rate.

## Open decisions

The protocol above is proposed, not frozen. Both authors agree on each row before the first full run.

| Decision | Current proposal | Question for the authors | Status |
|---|---|---|---|
| Validation size | Whole SciFiBase family (82 of 247 development segments; 2,378 validation vs 4,640 training pairs) | Is a validation set half the size of training justified, given only seven training environments? Alternative: a smaller held-out family or a subset of SciFiBase segments. | Open |
| Seeds and decision rule | Three seeds; report mean and range | Prior effects are ~0.04 dB (Brandner Table 5.5 alignment; RDG Table 3 depth). Measure seed spread in the pilot, then fix when an expectation counts as supported (e.g. same sign in all nine scene×seed cells, or a stated interval). | Open |
| Depth-edge threshold | 90th percentile of absolute LR depth difference, pooled over training pixels | Normalised near-to-far depth depends on each scene's clip planes, so pooled coverage may vary strongly by scene. Consider a relative difference (abs Δd / d) or a per-scene percentile. | Open |
| Perceptual metric | None (PSNR, SSIM, masked MAE, temporal error) | FLIP and LPIPS were in the previous evaluation plan. Mercier et al. report LPIPS on QRISP, so omitting it limits comparison with their baselines. State the reason or reinstate one as secondary. | Open |
| Target GPU | Not named in the protocol | Recorded with pilot and final measurements. | Agreed (commit ba9099e) |
| Training budget and precision | 20,000 updates, batch 8, FP32 | Confirm after the V0/V7 pilot. | Pending pilot |
| Dataset | QRISP six-variant protocol | Switch to STSS for the full twelve-variant matrix after the STSS pilot? | Pending pilot |

## Gate before the first full run

Write and jointly review the reproducibility record: configuration format, Git commit, seeds, data version and split manifest, GPU and software versions, timing method including warm-up, metric implementations, checkpoint location and result naming. Close every row of the open-decisions table first.
