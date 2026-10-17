# File Naming and Storage

## Names and identifiers

Use ASCII filenames, hyphens between words, and dates as `YYYY-MM-DD`. Keep citation keys stable once used in the manuscript. A useful key is `FirstAuthorYearShortTopic`, with suffixes `a`, `b` for collisions (for example, `Smith2024Temporal`); check the actual author and year before assigning one.

| Item | Pattern | Example |
|---|---|---|
| Paper note | `02_Paper_Notes/<CitationKey>.md` | `02_Paper_Notes/Smith2024Temporal.md` |
| Eligible PDF | `01_Literature/PDFs/<CitationKey>.pdf` | `01_Literature/PDFs/Smith2024Temporal.pdf` |
| Archived weekly plan | `99_Archive/weekly-plans/YYYY-MM-DD.md` | `99_Archive/weekly-plans/2026-10-19.md` |
| Future experiment | `EXP-001` with a descriptive run name later | `EXP-001_rgb-only_seed-01` |
| Future figure | `fig-01-short-description.pdf` | `fig-01-quality-latency.pdf` |

The tracker `ID` can use `P-001`, `P-002`, etc. Use `CitationKey` as the shared name in the PDF, note, and `references.bib`; do not rename a key after citing it without updating every reference. Do not guess citation metadata.

## Where files live

The GitHub repository is the canonical copy of text, LaTeX, BibTeX, small tables, and eligible research PDFs. Keep it **private** while sharing publisher PDFs unless each PDF's license permits public redistribution. Even a private repository should contain only files that both collaborators may lawfully share. If rights are unclear, store the DOI/URL and reading note, not a PDF copy.

Add a PDF to `01_Literature/PDFs/` when it is modest in size and both collaborators may share it. A large collection of binary files makes every clone and fetch heavier because Git retains old versions. Prefer immutable PDF filenames and avoid committing scans or repeatedly replacing files. Put large or restricted files in the agreed Google Drive folder if sharing there is permitted; add their link in the literature tracker. Do not commit datasets, checkpoints, generated experiment outputs, or secrets.

`references.bib` is the manuscript's citation source. Add only verified metadata for sources actually consulted or cited. The tracker stores screening decisions and broader reading plans; not every tracked source must appear in the bibliography.

## Collaboration

1. Pull the latest changes before editing.
2. Make focused commits with clear messages, for example `Add depth-input paper note` or `Draft introduction`.
3. Use a short branch and pull request for changes that may conflict or need review. Each person reviews the other's manuscript and method changes.
4. Do not edit the same manuscript section concurrently without agreeing who owns it for that session.
5. The GitHub Actions PDF is a preview built from the committed LaTeX source. Edit `main.tex`, not the generated PDF.

## Google Drive backup (optional, later)

A one-way backup from GitHub to Drive can be added after the repository and a shared Drive destination exist. It would require a Google service account or another authorized integration, a shared folder, and GitHub secrets. Mirror a **snapshot** of approved files; never use a two-way sync of the Git working directory because simultaneous edits can create conflicts and inconsistent history. Start with manual periodic exports if a backup is needed before automation is configured.

## Reproducibility decision to make before experiments

Before running ablations, agree which configuration, code version, random seed, scenes/splits, hardware, software versions, timing protocol, quality metrics, memory use, and output paths must be recorded for each run. The future experiment plan names this decision gate; the full logging policy will be written when the experimental design is fixed.
