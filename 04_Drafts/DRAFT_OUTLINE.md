# Manuscript Outline (Early Draft)

This is a planning outline. The single editable manuscript is `paper/main.tex`; do not maintain a second prose draft here.

## Title
An Ablation Study of Auxiliary Inputs for Lightweight Neural Upscaling in Real-Time Rendering

## Abstract (write last)
- Problem and context:
- Gap:
- Method:
- Key results:
- Main implication:

## 1. Introduction
- Real-time rendering and upscaling context
- Why auxiliary inputs may help
- Lightweight deployment constraints
- Provisional study objective and relationship to existing ablations
- Contributions

## 2. Background and Related Work
- Efficient neural reconstruction and quality–cost trade-offs
- Geometric guidance from depth and normals
- Temporal history, alignment, and spatial failure cases

## 3. Method
- Study design and analysis topics; hypotheses as testable expectations
- Color-only baseline and V1–V3 spatial variants
- V4–V6 temporal extension and limits of comparisons
- Model, capacity controls, and training setup
- Data/scenes and preprocessing
- Baselines
- Metrics and runtime protocol

## 4. Results
- Individual and combined geometric inputs
- Temporal history and alignment
- Quality, runtime, and memory trade-offs
- Variation across held-out scenes
- Spatial benefits and failure cases

## 5. Discussion
- Interpretation and trade-offs
- Comparison with prior work
- Limitations and validity threats
- Practical implications

## 6. Conclusion
- Address the study objective using measured evidence
- Main findings
- Next steps

## References
Keep verified citation records in `paper/references.bib`; adapt the bibliography style when a target venue is chosen.
