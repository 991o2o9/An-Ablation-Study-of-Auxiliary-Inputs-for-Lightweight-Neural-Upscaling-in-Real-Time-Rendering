# Writing and Building the Paper

`main.tex` is the only editable manuscript. The PDF is a generated preview. Keep bibliography records in `references.bib` and cite them using `\cite{CitationKey}`. The visible “References” page is intentionally empty until real, verified sources are added and cited; reading-list items stay in the literature tracker.

Confirm the author order and replace the affiliation placeholder before sharing a formal draft. Keep the title page until a target conference or journal supplies its required template; then adapt the formatting without duplicating the manuscript content.

## Local build

Install a TeX distribution with `latexmk` and `biber`. From this directory run:

```sh
latexmk -pdf -interaction=nonstopmode -halt-on-error main.tex
```

Open `main.pdf`. If a new bibliography entry does not appear, cite its key in `main.tex` and rebuild. `latexmk` runs the needed passes, including Biber.

## GitHub build

The workflow in `.github/workflows/build-paper.yml` builds the PDF on pushes and pull requests. After a successful build on `main`, it also updates `paper.pdf` on the private `paper-preview` branch. Use the **Open the latest paper PDF** link in the repository README for a stable preview. Each run also keeps a downloadable `paper-pdf` artifact under GitHub **Actions**. Edit `main.tex`, not the generated PDF. Action artifacts expire according to repository settings; keep the LaTeX source in Git and export the final submission PDF separately when needed.

## Writing rules

- Write substantive claims with a citation, or mark the claim for verification in a source comment.
- Do not report planned methods as completed experiments.
- Include exact conditions for numbers quoted from prior work.
- Keep one section owner at a time when both people edit `main.tex`; review changes through a pull request when useful.
