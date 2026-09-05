# Maintenance notes

Open issues and deferred decisions for this template. Each entry says what is
wrong, why it has not been fixed, and what fixing it would involve.

Resolved items are not listed here; see the git log.

## Licensing - needs a decision

Provenance was traced in September 2026. The lineage is
`unl-statistics/UNL-thesisdown-template` (originally `earobinson95/...`, since
transferred; GitHub 301-redirects the old path) -> forked to
`near-center-unl/UNL-thesisdown-template` in March 2026 with no commits of its
own -> forked to this repository.

**`nuthesis.cls` carries two licenses.** Lines 1-676 are the verbatim GPLv3
license document; line 685 is the class's own notice:

    %% Copyright (C) 2008 by Ned W. Hummel nhummel@gmail.com
    %% This file may be distributed and/or modified under the conditions of
    %% the LaTeX Project Public License, either version 1.3c of this license
    %% or (at your option) any later version.

The GPL block grants nothing. Its "How to Apply These Terms" placeholders are
still literally `Copyright (C) <year>  <name of author>` at lines 635 and 655,
so no one ever applied GPLv3 to anything. The only grant naming a copyright
holder is Hummel's LPPL 1.3c.

The block is not a lineage convention: huskydown's `uwthesis.cls` (Apache 2.0)
and thesisdown's `reedthesis.cls` (Sam Noble's custom notice) have none, and
`nuthesis.cls` is the only file in this repository containing the string "GNU
GENERAL PUBLIC LICENSE". It arrived fully formed in `ffb96fc` (Susan
Vanderplas, 2021-05-17, "Essential files") - no commit anywhere adds it to a
previously clean file, so it came in with the imported copy or was pasted at
import. That cannot be settled from history; the pristine UNL maths department
file is behind a Google Drive link with no Wayback snapshot, and `nuthesis` is
not on CTAN.

**Nothing in the lineage declares a license for the template's own files.**
No repository in the chain has ever had a LICENSE file or a README license
statement. The material derives from huskydown (MIT, Ben Marwick 2018), itself
from thesisdown (MIT, Chester Ismay 2020). MIT permits the derivation but
requires the copyright and permission notice to travel with it, and no MIT
notice appears anywhere in this repository - only the README line "Modified
from: https://github.com/benmarwick/huskydown".

Open decisions:

1. Strip lines 1-676 of `nuthesis.cls`, restoring Hummel's file to its upstream
   LPPL form. Comment lines only; no macro is affected.
2. Add a `LICENSE` covering this repository's own contributions, plus the
   retained MIT notices for Marwick and Ismay.
3. The UNL-original prose is unlicensed, so it defaults to all rights reserved
   by its authors (Emily Robinson, Susan Vanderplas and other contributors).
   Only they can change that.

## LaTeX

**`lineno` is loaded but unused.** `template.tex:148` has a commented-out
`% \linenumbers{}` opt-in for draft line numbering, so the package was kept
deliberately: removing it would turn uncommenting that line into an undefined
control sequence. Remove both together, or leave both.

**Adding a package means editing `template.tex`.** `index.Rmd:35` carries a
commented-out `#header-includes:` / `#- \usepackage{tikz}`, but `template.tex`
has no `$header-includes$` placeholder, so uncommenting that YAML would have no
effect. Put the `\usepackage` line in `template.tex` instead, or add the
placeholder first.

### Packages removed in 3d10734, and when to put them back

None of these were ever used in this repository - searching the full history
for their macros (`\todo{`, `\missingfigure`, `lstlisting`, `\lstset`,
`\dirtree`, `\cancel{`, `\cancelto`, `\bcancel`, `\xcancel`) returns nothing in
any commit. They arrived with the preamble inherited from huskydown, which took
it from ucbthesis and suchow/Dissertate. Each is worth re-adding if the
specific need appears.

**`todonotes`** - draft annotation. `\todo{fix this}` puts a margin note,
`\missingfigure{}` a placeholder box, and `\listoftodos` an index of everything
outstanding. Genuinely useful while a thesis is in progress with an adviser,
and the `colorinlistoftodos` option that was configured here colours that
index. Cost: it pulls the whole PGF/TikZ stack, which was 22 of the 22 support
files the removal saved and most of the compile-time win. Worth re-adding
during drafting and removing before submission.

**`listings`** - typesets source code from a file or a verbatim block, with
line numbers, captions and language-aware highlighting. Relevant if an appendix
ships whole scripts via `\lstinputlisting{analysis.R}`, which keeps the file on
disk rather than pasting it into the `.Rmd`. Not needed for ordinary knitr
chunks: those render through pandoc's skylighting `Shaded`/`Highlighting`
macros on top of `fancyvrb`, which `preamble.tex` already restyles. Re-add it
only alongside pandoc's `--listings`, and check that `preamble.tex`'s `Shaded`
redefinition still applies.

**`dirtree`** - draws an indented directory tree from a simple
`\dirtree{.1 project/. .2 data/.}` description. Fits a reproducible-research
appendix documenting the repository or data layout, which is a common ask in
UNL statistics theses. No dependencies of note, so cheap to re-add.

**`cancel`** - strike-through in maths: `\cancel{x}`, `\bcancel`, `\xcancel`,
and `\cancelto{0}{x}` for showing terms dropping out of a derivation. Useful in
a methods chapter that walks through algebra. Also cheap. Note `enumerate`
shared its `\usepackage` line and was kept.

## Inert configuration

`index.Rmd` declares YAML fields that nothing consumes:

- `location:` - `template.tex` has no `$location$` placeholder.
- `lot:` and `lof:` - `template.tex` calls `\listoftables` and `\listoffigures`
  unconditionally, so the flags cannot turn them off.

Either wire them into `template.tex` or delete them, so the YAML stops implying
control it does not have.

Related, and not a defect: the degree type is not a YAML field. It comes from
the `nuthesis` class option in `template.tex:2`, currently
`\documentclass[print]{nuthesis}`. Class defaults are `double,electronic,phd`;
a master's thesis needs `ms` or `ma` in that option list.

## Sample content

**`data/flights.csv` is 5.27 MB**, the largest object in the repository. It is
a derived subset of `pnwflights14` (Chester Ismay and Hadley Wickham, **CC0**,
GitHub only, not on CRAN): 52,808 Portland departures out of the package's
162,049 flights, complete cases only, with `carrier_name` and `dest_name`
joined in from that package's `airlines` and `airports` tables. Underlying data
is US DOT BTS on-time performance, public domain.

Nothing blocks trimming it. A few thousand sampled rows would be about 200 KB
and would still drive the `dplyr`/`ggplot2` examples in `01-chap1.Rmd` and
`03-chap3.Rmd`; a short script recording how the subset was derived should go
with it. Note that shrinking the working copy does not shrink the repository -
the 5.4 MB blob stays in history unless history is rewritten.

**Dead links remain in the chapter text.** Every URL was probed during the link
sweep; these have no verified replacement and need a prose decision rather than
a relink:

- `03-chap3.Rmd:140` promises "three pages on this topic" and links
  `web.reed.edu/cis/help/latex/{bibtex,bibtexstyles,bibman}.html`. Reed retired
  all three; the surviving LaTeX index has no equivalent. The sentence needs
  rewriting.
- `03-chap3.Rmd:136` `sites.middlebury.edu/zoteromiddlebury/` - 404.
- `02-chap2.Rmd:118` `mirror.utexas.edu/ctan/.../symbols-letter.pdf` - host does
  not resolve. The Comprehensive LaTeX Symbol List is still on CTAN elsewhere.
- `02-chap2.Rmd:122` `www.lecb.ncifcrf.gov/~toms/latex.html` - host does not
  resolve.
- `02-chap2.Rmd:122` `homepages.uni-tuebingen.de/beitz/txe.html` - https
  certificate chain does not verify.

**The sample chapters are inherited example prose.** `01-chap1.Rmd` through
`05-appendix.Rmd` document how to use bookdown and came from huskydown and Reed
College. They are meant to be replaced by a real thesis, so their wording is
not documentation of this template.

## Repository hygiene

Build output was untracked in this fork: `docs/` and `_bookdown_files/` were
committed upstream and remain there, including whatever GitHub Pages serves.
They are regenerable and are now ignored here, along with the `thesis_cache/`
and `thesis_files/` directories a build leaves in the project root.

The blobs are still in this repository's history. Rewriting history to drop
them would need a force-push and would break the fork relationship with
upstream. Measured: 10.2 MB of raw blobs but only about 2.4 MB packed, of which
`data/flights.csv` alone is 5.4 MB raw - so a rewrite that spared the CSV would
recover well under 1 MB. Not judged worth it.

## Agent governance

The Clanker Constitution is pinned at release `v2026.08.11` in
`CONSTITUTION.md`, referenced by `AGENTS.md` and imported by `CLAUDE.md`.
Upstream advises against fetching it at agent startup, so bumping to a newer
release should be a deliberate, reviewed change: replace the file from the new
tag and update the version named in `AGENTS.md` and in this note. Canonical
source: https://github.com/kenn-io/constitution
