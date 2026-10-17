# Neural Upscaling Research Workspace

**Working title:** An Ablation Study of Auxiliary Inputs for Lightweight Neural Upscaling in Real-Time Rendering  
**Team:** Zhanbolot Kydyraliev and Amin Soltobekov  
**Current phase:** scope, literature, and manuscript setup

This repository is the shared working space for the paper. There is **one manuscript source** in `04_Drafts/paper/main.tex`; its PDF is a generated preview, not a separate draft to edit. The title and study design are provisional until the team agrees on the experimental scope and a target venue.

## Start here

1. Fill in names, meeting time, milestone, and scope in `00_Project_Hub/PROJECT_HOME.md` and `00_Project_Hub/TEAM_WORKING_AGREEMENT.md`.
2. Review `00_Project_Hub/ROADMAP.md`, then work from the current `00_Project_Hub/WEEKLY_PLAN.md`; after each meeting, record decisions and named next actions.
3. Add each source once to `01_Literature/LITERATURE_TRACKER.csv`. Add verified citation details to `04_Drafts/paper/references.bib`, and use the same citation key in paper notes and PDF filenames.
4. Read `00_Project_Hub/FILE_NAMING_AND_STORAGE.md` before adding PDFs or renaming files.
5. Edit `04_Drafts/paper/main.tex` as the shared manuscript. The build instructions are in `04_Drafts/paper/README.md`.
6. Use `05_Experiments/EXPERIMENT_PLAN.md` to agree on future experiments before adding datasets, model code, or results.

## Folder map

```text
00_Project_Hub/         scope, roadmap, coordination, decisions, current plan
01_Literature/          search log, source tracker, eligible PDFs
02_Paper_Notes/         one note per paper
03_Synthesis/           cross-paper comparisons and gaps
04_Drafts/              outline and one LaTeX manuscript
05_Experiments/         future experiment plan only
```

## Source of truth

- GitHub repository: manuscript, bibliography (`references.bib`), notes, tracker, plans, and small eligible PDFs.
- Literature tracker: screening status, owner, extraction fields, and links. One row per source.
- Paper note: detailed reading notes and page-level evidence.
- Manuscript: claims selected for publication, with citations to verified bibliography entries.
- Google Drive: optional shared storage for PDFs that should not be in Git and later for large datasets/checkpoints; put a stable link in the tracker. It is not a second editable copy of the manuscript.

Zotero is optional. The repository works without it. If you later use Zotero, export or reconcile its references into the canonical `references.bib` rather than maintaining unrelated citation lists.

## Status values

`Identified` → `Screening` → `To Read` → `Reading` → `Read` → `Included` or `Excluded`.

Record a reason for exclusions. Keep claims from authors separate from your interpretations. Direct quotations need page numbers.

This workspace supports a transparent literature search but does not itself make the paper a systematic review.
