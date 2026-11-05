# Efficient Video Super-Resolution for Real-time Rendering with Decoupled G-buffer Guidance (2025)

- **Authors:** Zheng et al.
- **Primary source:** https://openaccess.thecvf.com/content/CVPR2025/papers/Zheng_Efficient_Video_Super-Resolution_for_Real-time_Rendering_with_Decoupled_G-buffer_Guidance_CVPR_2025_paper.pdf
- **Tracker ID:** zheng2025rdg
- **Decision:** core; extraction ongoing
- **Relevant comparisons:** V5–V6; held-out scenes; quality–cost; spatial artifacts

## Claim or design decision supported
Normal and BRDF guide high-frequency features; depth and motion guide recurrent alignment. Table 4 compares fusion architectures; it does not establish individual depth/normal effects.

## Locations and differences
§3.3–3.4, §4.1, Table 1, §5 and Table 4. Cycles 270×480 to 1080×1920; eight test scenes; RTX 3090; RGB PSNR/SSIM and VMAF. Low-resolution inputs, separate guidance branches; encoding and scene-disjoint status need verification.

## Limitations and follow-up
This paper does not establish the effect in the planned study. Verify input encoding, all comparison controls, scene splits and end-to-end timing boundaries in the full paper and supplement before citing numerical results. The planned study has no results yet.
