# Efficient Neural Supersampling on a Novel Gaming Dataset (2023)

- **Authors:** Mercier et al.
- **Primary source:** https://openaccess.thecvf.com/content/ICCV2023/papers/Mercier_Efficient_Neural_Supersampling_on_a_Novel_Gaming_Dataset_ICCV_2023_paper.pdf
- **Tracker ID:** mercier2023efficient
- **Decision:** core; extraction ongoing
- **Relevant comparisons:** V4–V5; thin structures; data and cost

## Claim or design decision supported
Explicit no-warp, single-frame and optical-flow comparisons overlap temporal questions. Depth-informed motion dilation is a component ablation, not a clean depth-only effect; removing dilation improved aggregate metrics while authors report thin-object benefits.

## Locations and differences
§6.2, Table 3, Figs. 9–10; Fig. 1. 2× medium model for ablation; 1080p output RTX 3090 timing in Fig. 1; dataset contains render-resolution depth and motion. Encoding and timing boundaries need checking.

## Limitations and follow-up
This paper does not establish the effect in the planned study. Verify input encoding, all comparison controls, scene splits and end-to-end timing boundaries in the full paper and supplement before citing numerical results. The planned study has no results yet.
