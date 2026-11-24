# Efficient Video Super-Resolution for Real-time Rendering with Decoupled G-buffer Guidance (2025)

- **Authors:** Zheng et al.
- **Primary source:** https://openaccess.thecvf.com/content/CVPR2025/papers/Zheng_Efficient_Video_Super-Resolution_for_Real-time_Rendering_with_Decoupled_G-buffer_Guidance_CVPR_2025_paper.pdf
- **Tracker ID:** zheng2025rdg
- **Decision:** core; extraction ongoing
- **Relevant comparisons:** Depth and alignment contrasts V5–V4 and V7–V6, and the conditional normal variants

## Claim or design decision supported
Table 3 ablates individual inputs of RDG-s. Full, no normals, no depth, and no motion score 29.37, 29.27, 29.33 and 29.00 dB, respectively. We use the table values for depth because the text calls PSNR unchanged. Without motion, the model merges unaligned history. Table 4 separately compares the fusion modules.

## Interpretation for our study

The table measures each removal with the remaining guidance present. Removing all G-buffers also removes BRDF and normals. It does not give the four matched depth-by-alignment cells with other inputs held fixed.

## Locations and differences
§3.3–3.4, §4.1, Tables 1, 3 and 4. Blender Cycles; the reported paired render is 480×270 to 1920×1080 (4×); eight test scenes; RTX 3090; RGB PSNR/SSIM and VMAF. Guidance enters through separate low-resolution branches, and history is carried as recurrent features rather than a single previous RGB frame. The released training configuration enables LR normal, depth and motion and reads pre-extracted training patches. [Release and dataset access](https://github.com/max-well-d/RDG) · [Training configuration](https://github.com/max-well-d/RDG/blob/8d6b21f5b96c01aa0ee5c88c934d2ba756a274ef/options/train/train_RDG-s_x4.yml)

## Limitations and follow-up
The Table 3 differences are small (0.04 dB for depth), so they set an expectation for effect size rather than establishing an effect in our model. Verify input encoding, scene-to-clip IDs, scene-disjoint status and available patch context before proposing scene-disjoint validation or full-frame warps on this data. Verify end-to-end timing boundaries before comparing cost.
