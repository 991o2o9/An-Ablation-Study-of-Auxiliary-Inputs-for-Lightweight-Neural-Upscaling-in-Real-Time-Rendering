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

## Ablation matrix

Current RGB is always present. The column describes only the immediately preceding LR RGB. Geometry is always from the current frame.

| Current geometry | No history | Unaligned history | Motion-aligned history | Scope |
|---|---|---|---|---|
| None | V0 | V4 | V5 | QRISP |
| Depth | V1 | V6 | V7 | QRISP |
| Normals | V2 | V8 | V9 | Separate extension |
| Depth and normals | V3 | V10 | V11 | Separate extension |

The matrix retains PR #5’s IDs. The earlier seven-variant proposal’s V6 maps to V11 here. Identify runs by protocol revision as well as variant. Independent normals would require matching captures and same-source controls for every comparison. Depth-derived normals would answer a different question about depth encoding.

| Quantity (scene-balanced PSNR in dB) | Matched contrast |
|---|---|
| Depth benefit with absent / unaligned / aligned history | V1 − V0 / V6 − V4 / V7 − V5 |
| Benefit of adding one unaligned frame without / with depth | V4 − V0 / V6 − V1 |
| Benefit of alignment without / with depth | V5 − V4 / V7 − V6 |
| Change in alignment benefit when depth is present | (V7 − V6) − (V5 − V4) |

The directional expectations are positive single-frame depth gain and positive alignment gain at both depth settings. Estimate the interaction in either direction. Compute each difference within the same scene and seed before averaging.

Jitter is off for every core row. A secondary experiment could retrain V4 and V5 using the jittered versions of all corresponding LR buffers, the same Enhanced target, splits and seeds. Compare the alignment gain against the non-jittered gain. The CNN receives no explicit offset channels. Check relative jitter in the motion field before applying any correction. Learned intermediate CNN activations exist in every model. A recurrent feature buffer exists in none of them.

## Preparation and training

1. Acquire a training scene (for example CB-Apocalypse) using the distributor’s per-scene instructions. Record the archive hashes, expanded size, segment IDs and package versions. Store raw data and checkpoints outside Git.
2. Read stored RGB bytes in RGB order, discard color alpha and divide by 255. Decode packed depth using all four byte channels per Annex A, Eq. 4. Keep the resulting near-to-far values. Check paired frame identities, crop alignment, byte order, motion finiteness and depth range on training captures. Investigate unexpected values before freezing the manifest.
3. Decode vertical then horizontal EXR velocities and scale by the corresponding LR dimension. Verify axis sign and current-to-previous source coordinates using trackable training-scene motion. Test identity and one-pixel translations on a synthetic grid. Bilinearly sample full-frame previous RGB with half-pixel centers and replicated borders before cropping. Supply no motion-derived mask, depth dilation or raw velocity channels to the CNN.
4. Use the ten-slot, 32-channel residual CNN specified in the manuscript (four blocks, PixelShuffle 2× and a bilinear RGB skip). Omitted slots contain zeros. Draw eligible training pairs uniformly with replacement, then a uniform valid 64×64 LR crop and its corresponding 128×128 HR target. Save the draws and use identical initial weights and draws across variants for each of seeds 17, 29 and 43.
5. Train independently using mean L1 on unclipped output, AdamW at constant 0.001, betas (0.9, 0.999), epsilon 1e−8 and weight decay 0.01. Proposed batch size is eight, with 20,000 updates and full validation every 1,000. Select the highest mean validation-scene PSNR with ties going to the earlier checkpoint.
6. Pilot both V0 and V7 in FP32 on the 8-GB RTX 4060 to include the cost of motion I/O, warping and validation. For each pilot record peak allocated memory, updates/s, validation wall time and I/O/cache settings. Estimate time for 18 training runs (360,000 updates plus 360 validation passes). Apply any resource-driven budget or precision adjustment to every variant before final training.

## Evaluation and reporting

- Score the same eligible full frames, including borders and UI, with predictions clipped to [0,1] and references in the stored RGB encoding. Include bicubic interpolation using half-pixel centers. Average RGB PSNR in dB over frames within scenes, then give each test scene equal weight. Secondary SSIM uses range one, 11×11 valid Gaussian windows, sigma 1.5, standard constants, population covariance and channel averaging.
- For temporal error, subtract the reference RGB change from the output RGB change over consecutive scored frames. Average absolute values over pixels and channels, then frames within each scene and scenes equally. This leaves 1,490 test transitions if the inventory is intact. Report alongside synchronized clips since ordinary spatial error on moving edges also affects this score.
- Show scene-by-seed values, matched contrasts, and the mean and range across seeds. Report all three scenes individually. Do not treat adjacent frames as independent replicates.
- Time full-frame batch-one inference and the resident-buffer pipeline separately. The pipeline includes input assembly, any motion conversion and warping, network execution and clipping. Report CUDA-event median and p95 across 200 fixed frames after 50 warm-ups, peak allocated memory after a warm-up reset, and the GPU, driver, software and precision. Measure each pipeline in isolation. Disk, CPU transfers, rendering and quality metrics are outside these timings.
- Save run IDs, Git SHA, input hashes, split/crop manifests, environment, validation trace, selected checkpoint and raw frame-level scores alongside the summaries. Offline timings do not establish an in-engine frame rate.
