# Codebase Review Handoff — UNL-thesisdown-template

## What was done

A full codebase review of `near-center-unl/UNL-thesisdown-template` was completed. All mechanical fixes (bugs, typos, identity issues, code quality) have been applied and are captured in `codebase-review.patch`.

## How to apply

```bash
# 1. Fork https://github.com/near-center-unl/UNL-thesisdown-template to your account (if not already done)

# 2. Clone your fork
git clone https://github.com/amelia-m/UNL-thesisdown-template.git
cd UNL-thesisdown-template

# 3. Create the branch
git checkout -b claude/codebase-review-aVWPs

# 4. Apply the patch
git am codebase-review.patch

# 5. Push
git push -u origin claude/codebase-review-aVWPs
```

## What the patch changes (12 files)

### Bug fixes
- **`03-chap3.Rmd`**: `require(ggplot2)` was checked twice — second branch now correctly checks `require(bookdown)` before installing bookdown
- **`03-chap3.Rmd`**: chunk options `warnings=FALSE, messages=FALSE` (invalid) → `warning=FALSE, message=FALSE` (correct knitr option names)

### UW → UNL identity fixes
- **`03-chap3.Rmd`**: `uwlogo` → `unllogo` chunk label; "UW logo" → "UNL logo" in text
- **`03-chap3.Rmd`**: contact link now points to `near-center-unl/UNL-thesisdown-template/issues`
- **`98-colophon.Rmd`**: rewritten to reference UNL/nuthesis.cls instead of University of Washington; removed `xxx` placeholders and UW library reference
- **`template.tex`**: header comment updated from UWThesis to nuthesis.cls

### Typos and documentation
- **`README.md`**: "exctract" → "extract", "Debuging" → "Debugging", R version 3.6.3 → 4.0.0, `_book/thesis.pdf` → `docs/thesis.pdf`, removed note-to-self
- **`03-chap3.Rmd`**: "pacakge" → "package"
- **`05-appendix.Rmd`**: "readibility" → "readability"

### Code quality
- **`index.Rmd`, `01-chap1.Rmd`, `03-chap3.Rmd`**: all `repos = "http://cran.rstudio.com"` → `"https://cloud.r-project.org"`
- **`index.Rmd`**: `tidy = T` → `tidy = TRUE`
- **`style.css`**: added missing semicolon
- **`preamble.tex`**: removed duplicate URL in comment
- **`template.tex`**: removed duplicate `\usepackage{graphicx}`, dead `\bibliography{./references}` and `\bibliographystyle{abbrv}` lines

### Bibliography cleanup
- **`bib/thesis.bib`**: removed fake/test entries (`angel2001`, `angel2002a`, `goochandgooch2001a`) and stale JabRef header
- **`99-references.Rmd`**: removed `nocite` block that forced fake entries; replaced with commented-out example

### .gitignore improvements
- **`.gitignore`**: added `*.aux`, `*.bbl`, `*.blg`, `*.fls`, `*.fdb_latexmk`, `*.out`, `*.xdv`, `*.nav`, `*.snm`, `*.vrb`, `_bookdown_files/`, `.DS_Store`, `Thumbs.db`

## Still open — requires decision from maintainer

These were identified in the review but NOT addressed in the patch because they involve structural/policy decisions:

1. **Drop or vendor `huskydown` dependency** — `huskydown` is auto-installed from GitHub on every build (`index.Rmd`, `03-chap3.Rmd`). It's a UW package; the UNL template only uses a few helpers from it. Options: fork the needed pieces into this repo, pin to a specific commit, or remove the dependency entirely.

2. **Un-track `docs/` and `_bookdown_files/`** — 54 generated files (rendered HTML, PDF, knitr cache) are committed. These are build artifacts that churn on every rebuild. Options: add `docs/` to `.gitignore` (and use a `gh-pages` branch for hosting), or keep as-is if GitHub Pages is serving from `docs/`.

3. **License clarification** — `nuthesis.cls` has the full GPL v3 text (695 lines of comments) prepended, but the class itself declares LPPL at line 687. No top-level `LICENSE` file exists. Needs a decision on which license applies and a proper `LICENSE` file.

4. **Remove `natbib` from `template.tex`** — `\usepackage{natbib}` is loaded but the template uses pandoc/CSL for citations, not BibTeX. They can conflict.

5. **Trim unused LaTeX packages from `template.tex`** — `todonotes`, `listings`, `dirtree`, `lineno`, `cancel`, etc. are loaded but never used in sample content. They slow compiles and require extra TinyTeX packages.

6. **Large demo data file** — `data/flights.csv` is 5.3 MB of Portland flight data from a UW/Reed example. Consider a smaller illustrative dataset.

7. **Dead/broken HTTP links** — many URLs in the chapter text (rmarkdown.rstudio.com, yihui.name, docs.ggplot2.org, web.reed.edu, lecb.ncifcrf.gov) should be updated to HTTPS and checked for liveness.

8. **MathJax script block in `02-chap2.Rmd`** — raw `<script>` tag renders as literal text in PDF output; should be wrapped in `if(is_html_output())`.
