# Neural Supersampling for Real-time Rendering (2020)

- **Authors:** Xiao et al.
- **Primary source:** https://www.cs.jhu.edu/~misha/ReadingSeminar/Papers/Xiao20.pdf
- **Tracker ID:** xiao2020neural
- **Decision:** core; extraction ongoing
- **Relevant comparisons:** V4–V5; disocclusion; cost

## Claim or design decision supported
Low-resolution RGB/depth and motion; temporal feature warping and U-Net. Table 8 adds depth while replacing estimated flow with motion: joint change, not independent input effects. Fig. 9 single-frame versus temporal.

## Locations and differences
§3.2, §4.3.5–4.3.7, Tables 8 and 10, Figs. 9–10. 4×4; synthetic Unity scenes; encoding and timing boundaries require further extraction.

## Limitations and follow-up
This paper does not establish the effect in the planned study. Verify input encoding, all comparison controls, scene splits and end-to-end timing boundaries in the full paper and supplement before citing numerical results. The planned study has no results yet.
