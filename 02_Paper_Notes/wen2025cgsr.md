# Achieving Lightweight Super-Resolution for Real-Time Computer Graphics (2025)

- **Authors:** Wen et al.
- **Primary source:** https://ojs.aaai.org/index.php/AAAI/article/download/32897/35052
- **Tracker ID:** wen2025cgsr
- **Decision:** core; extraction ongoing
- **Relevant comparisons:** V0–V3; quality–cost; spatial errors

## Claim or design decision supported
Sequential addition of depth, normals and depth-derived edges measures changes in accuracy and model size; not a full factorial V0–V3. Transform block, architecture search and pruning change capacity, unlike a fixed backbone.

## Locations and differences
pp. 8315–8319, Fig. 1 and Fig. 6(a), Ablation Study. Geometry at low resolution is transformed; exact encodings, resolution and timing conditions need extraction before comparison.

## Limitations and follow-up
This paper does not establish the effect in the planned study. Verify input encoding, all comparison controls, scene splits and end-to-end timing boundaries in the full paper and supplement before citing numerical results. The planned study has no results yet.
