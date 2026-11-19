# Literature Search Protocol (Working Document)

## Purpose

Build a transparent, repeatable literature search for a study of auxiliary inputs in lightweight neural upscaling for real-time rendering.

## Search concepts

Combine terms from these groups; record exact queries as run.

- Upscaling: `neural upscaling`, `neural super resolution`, `image reconstruction`, `temporal upscaling`
- Rendering: `real-time rendering`, `real time graphics`, `game rendering`
- Inputs: `auxiliary input`, `depth`, `motion vectors`, `normal`, `G-buffer`, `history buffer`
- Study design: `ablation`, `input ablation`, `feature contribution`

## Search log

| Date | Database / search engine | Exact search string | Filters | Results count | Searcher |
| ---- | ------------------------ | ------------------- | ------- | ------------: | -------- |
| 2026-11-19 | Web search | `neural supersampling study ablation effect of individual G-buffers depth surface normals motion vectors warping history input temporal reconstruction controlled rendering FSNet FuseSR ExtraNet ANTI ALIASING G buffer ablation` | Top results | 8 | Codex |
| 2026-11-19 | Web search | `2026 neural supersampling real-time rendered content depth motion normals temporal supersampling ablation paper arXiv 2026 rendering dataset super-resolution real time Unreal neural` | Top results | 8 | Codex |
| 2026-11-19 | Web search | `github Zheng RDG Rendering dataset download Baidu Google drive Cycles training Mingjun Zheng RDG dataset released training testing` | Top results | 8 | Codex |
| 2026-11-19 | Web search | `Kasper Brandner Unreal real-time rendering super resolution ablation inputs normals motion depth warping temporal ablation thesis table G-buffer` | Top results | 8 | Codex |
| 2026-11-19 | Web search | `"Low-latency Space-time Supersampling" dataset GitHub google drive dataset 540p G-buffers OpenSTSS or open source dataset` | Top results | 8 | Codex |

These are representative queries from the targeted November refresh. Counts are the records returned for each listed query, with overlap. Follow-up checks used the full papers, Brandner's thesis, the authors' loaders and configurations, and the STSS split and frame manifests. The [experiment plan](../05_Experiments/EXPERIMENT_PLAN.md) records the dataset comparison and outstanding file-level checks.

## Eligibility criteria (draft; agree before screening)

**Include:** peer-reviewed papers and relevant technical reports/preprints that study neural upscaling, temporal reconstruction, or auxiliary rendering inputs; sources with enough method detail to extract inputs, metrics, and runtime context.

**Exclude:** sources unrelated to rendering/upscaling; duplicate versions (keep the most complete record and note earlier versions); sources with no usable method/result information. Record the reason for each exclusion.

## Screening procedure

1. Add records to the shared tracker; Zotero may be used as an optional collection tool.
2. Deduplicate by DOI, then title and author/year.
3. Screen title and abstract; mark Include / Exclude / Uncertain and note why.
4. Resolve uncertain items together.
5. Read full text for included and uncertain sources; update the decision and extraction fields.

This working protocol is not a claim that the project is a formal systematic review. If the study uses systematic-review language, refine and follow a suitable reporting standard for the field.
