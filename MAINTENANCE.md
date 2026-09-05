# Maintenance notes

Known issues and deferred decisions for this template. Each entry says what is
wrong, why it was not fixed, and what fixing it would involve.

Fixed items are not listed here; see the git log.

## Dependencies

**`huskydown` is installed from GitHub, unpinned.** The setup chunks in
`index.Rmd` and `03-chap3.Rmd` run `devtools::install_github("benmarwick/huskydown")`
on every build where the package is missing. huskydown is a University of
Washington template; this repo uses it for the `thesis_gitbook` output format and
a few helpers. Options: pin to a commit, vendor the pieces actually used, or drop
the gitbook format and the dependency together.

**`formatR` is required but never installed.** `index.Rmd` sets
`tidy = TRUE` in `knitr::opts_chunk$set()`, which calls `formatR::tidy_source()`.
`formatR` is not in the package vector in `01-chap1.Rmd` and not installed by any
setup chunk. Without it knitr warns per chunk and leaves code unformatted - a
silent degradation rather than a build failure. Fix: add `formatR` to the install
list, or drop `tidy = TRUE`.

## LaTeX

**`natbib` conflicts with the CSL citation path.** `template.tex:103` loads
`natbib`, but citations run through pandoc's citeproc using `bib/thesis.bib` and
`bib/apa.csl`. The two bibliography mechanisms can collide. Removing the
`\usepackage{natbib}` line is probably correct but needs a full PDF build to
confirm.

**Unused packages slow the build.** `template.tex` loads `todonotes`, `listings`,
`dirtree`, `lineno` and `cancel`, none of which the sample content uses. They add
compile time and extra TinyTeX/MiKTeX downloads on a clean machine.

**License of `nuthesis.cls` is ambiguous.** The file is upstream nuthesis v0.7.2
(2017-10-12), licensed LPPL 1.3c at line 688 - but 676 lines of GPLv3 text are
prepended above that header. The two are incompatible and the prepend looks
accidental. There is also no top-level `LICENSE` for the template's own files.
Resolving this means stripping the prepended GPL block and adding a `LICENSE`
that covers the `.Rmd`, `template.tex` and `preamble.tex` content.

## Inert configuration

`index.Rmd` declares YAML fields that nothing consumes:

- `location:` - `template.tex` has no `$location$` placeholder.
- `lot:` and `lof:` - `template.tex` calls `\listoftables` and `\listoffigures`
  unconditionally, so the flags cannot turn them off.

Either wire them into `template.tex` or delete them, so the YAML stops implying
control it does not have.

Related: the degree type is **not** a YAML field. It comes from the `nuthesis`
class option in `template.tex:2`, which currently reads
`\documentclass[print]{nuthesis}`. The class defaults are `double,electronic,phd`.
A master's thesis needs `ms` or `ma` in that option list.

## Sample content

**`data/flights.csv` is 5.4 MB** of 2014 Portland flight data inherited from the
huskydown/Reed lineage, used only for the `dplyr`/`ggplot2` demos in
`01-chap1.Rmd` and `03-chap3.Rmd`. It is the largest object in the repository. A
few hundred rows would illustrate the same points.

**Dead links remain in the chapter text.** Every URL was probed during the link
sweep; these have no verified replacement and still need a prose decision:

- `03-chap3.Rmd:140` promises "three pages on this topic" and links
  `web.reed.edu/cis/help/latex/{bibtex,bibtexstyles,bibman}.html`. Reed retired
  all three; the surviving LaTeX index has no equivalent. The sentence needs
  rewriting, not just relinking.
- `03-chap3.Rmd:136` `sites.middlebury.edu/zoteromiddlebury/` - 404.
- `02-chap2.Rmd:118` `mirror.utexas.edu/ctan/.../symbols-letter.pdf` - host does
  not resolve. The Comprehensive LaTeX Symbol List is still on CTAN under a
  different mirror.
- `02-chap2.Rmd:122` `www.lecb.ncifcrf.gov/~toms/latex.html` - host does not
  resolve.
- `02-chap2.Rmd:122` `homepages.uni-tuebingen.de/beitz/txe.html` - https
  certificate chain does not verify.

**The sample chapters are inherited example prose.** `01-chap1.Rmd` through
`05-appendix.Rmd` document how to use bookdown and came from huskydown and Reed
College. They are meant to be replaced by a real thesis, so their wording is not
documentation of this template.

## Repository hygiene

Build output was untracked in this fork: `docs/` and `_bookdown_files/` were
committed upstream (`near-center-unl/UNL-thesisdown-template`) and remain there,
including whatever GitHub Pages serves. They are regenerable by a build and are
now ignored here.

The blobs still exist in this repository's history. Rewriting history to drop
them would require a force-push and would break the fork relationship with
upstream; the whole packed repository is only about 2.4 MB, so the payoff is
small.
