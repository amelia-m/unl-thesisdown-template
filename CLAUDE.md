# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A bookdown template for University of Nebraska-Lincoln theses and dissertations. It is a document, not a package: there is no `DESCRIPTION`, no test suite, and no build script. Output is a PDF (and optionally gitbook HTML) rendered from `.Rmd` chapters through pandoc into a LaTeX class that enforces UNL Graduate Studies formatting.

Forked from [huskydown](https://github.com/benmarwick/huskydown) (a University of Washington template), so UW-era naming and assumptions still surface in places.

## Build

There is no Makefile. RStudio's **Build > Build Book** button is the documented path; from a shell:

```r
# All formats listed in index.Rmd's YAML
Rscript -e "bookdown::render_book('index.Rmd')"

# PDF only (the primary deliverable)
Rscript -e "bookdown::render_book('index.Rmd', output_format = 'bookdown::pdf_book')"

# Gitbook HTML only
Rscript -e "bookdown::render_book('index.Rmd', output_format = 'huskydown::thesis_gitbook')"

# Fast iteration on one chapter (still needs index.Rmd for the YAML)
Rscript -e "bookdown::preview_chapter('03-chap3.Rmd')"

# Remove build artifacts
Rscript -e "bookdown::clean_book(TRUE)"
```

Output lands in `docs/` (`output_dir` in `_bookdown.yml`), as `thesis.pdf`, `thesis.tex` (`keep_tex: yes`), and `index.html`. `delete_merged_file: true` means the concatenated `thesis.Rmd` is removed after each build, so do not look for it.

Requires XeLaTeX (`latex_engine: xelatex`) plus the packages `template.tex` loads, and R packages `bookdown`, `dplyr`, `ggplot2`, `knitr`, `devtools`, `git2r`, and `huskydown` (installed from GitHub, unpinned, by setup chunks in `index.Rmd` and `03-chap3.Rmd`).

## How the pieces connect

Understanding a formatting change usually means tracing one value across three files:

1. **`index.Rmd`** is the entry point. Its YAML holds both the thesis metadata (`title`, `author`, `adviser`, `major`, `month`, `year`, `abstract`, `acknowledgments`, `dedication`) and the output-format configuration. Its `setup` chunk sets global `knitr::opts_chunk` defaults for the whole book.
2. **`template.tex`** is a *pandoc* template, not a plain `.tex` file. The `$title$`, `$abstract$`, `$body$` etc. placeholders are substituted from that YAML at render time. It also `\input{preamble.tex}` and fixes the front-matter order (title page, abstract, copyright, dedication, acknowledgments, ToC, LoF, LoT) before `\mainmatter`.
3. **`nuthesis.cls`** is the UNL class (built on `memoir`) that implements `\maketitle`, the `abstract`/`dedication`/`acknowledgments` environments, and the margin/spacing rules the university checks.

Chapters are ordered by filename prefix: `01-` through `05-` are content, `98-colophon.Rmd` and `99-references.Rmd` are back matter and must stay last. `_bookdown.yml` sets `chapter_name: "CHAPTER "` for the ToC.

### Gotchas that follow from that split

- **YAML fields only work if `template.tex` references them.** `location:` is present in `index.Rmd` but has no `$location$` placeholder, so it is inert. Likewise `lot: true` / `lof: true` do nothing here: `template.tex` calls `\listoffigures` and `\listoftables` unconditionally.
- **Degree type is a LaTeX class option, not YAML.** `nuthesis.cls` defaults to `double,electronic,phd`; `template.tex:2` overrides with `\documentclass[print]{nuthesis}`. A master's thesis needs `ms` or `ma` in that option list (which sets doctype, degree name, and abbreviation together). `print` adds a binding offset; `electronic` does not.
- **Citations go through pandoc-citeproc, not BibTeX.** `bibliography: bib/thesis.bib` and `csl: bib/apa.csl` in `index.Rmd` drive everything; `99-references.Rmd` only positions the `# References` heading and its hanging-indent LaTeX. `template.tex` still loads `natbib` (line 103) alongside the CSL machinery, which is a latent conflict.
- **Figures live in `figure/`** and are inserted with `include_graphics(path = "figure/x.png")`. Cross-references use the chunk label: a chunk named `unllogo` is referenced as `\@ref(fig:unllogo)`.
- **`docs/` and `_bookdown_files/` are tracked in git** despite being build output, so most builds produce a large diff of generated files.

## Repo state

`HANDOFF.md` and `codebase-review.patch` in the working tree are review artifacts, not part of the template. The patch is already applied as commit `0d9ed42`. `HANDOFF.md` lists the structural items deliberately left open (the unpinned huskydown dependency, tracked build output, license ambiguity, `natbib`, unused LaTeX packages, the 5.3 MB `data/flights.csv` demo dataset).

Sample content in `01-chap1.Rmd` through `05-appendix.Rmd` is template documentation showing how to use bookdown, inherited from huskydown and Reed College. It is meant to be replaced by a real thesis, so treat its prose as example text rather than repository documentation.
