# AGENTS.md

Directives for any coding agent working in this repository.

## Baseline behavior

Read and follow [`CONSTITUTION.md`](CONSTITUTION.md) - the Clanker Constitution,
release `v2026.08.11`, pinned in this repository rather than fetched at startup.
It sets default operating principles: honor the request, act with judgment,
finish the job, protect existing work, verify reality, communicate for humans,
and learn in the right place.

Precedence, highest first:

1. Direct instructions from the user in the current session.
2. This file and the repository-specific guidance it points to.
3. `CONSTITUTION.md`.

Update the pinned copy only through a deliberate, reviewed change to a newer
release tag - do not fetch it during a session.

## Repository-specific guidance

- [`CLAUDE.md`](CLAUDE.md) - what this repository is, how to build it, and how
  `index.Rmd`, `template.tex` and `nuthesis.cls` fit together. Read it before
  changing anything about the build or the formatting.
- [`MAINTENANCE.md`](MAINTENANCE.md) - open issues and deferred decisions,
  including the unresolved licensing of `nuthesis.cls`. Check it before starting
  work in an area it covers, and record new deferred decisions there rather than
  in a commit message alone.

## Things that bite in this repository

- This is a document, not a package: no `DESCRIPTION`, no test suite. The
  verification that matters is a real build, and `bookdown::render_book()` needs
  pandoc on `PATH` or `RSTUDIO_PANDOC` set.
- Packages come from `renv`. Use `renv::restore()`; do not install into the
  system library or add unpinned GitHub dependencies.
- `docs/` and `_bookdown_files/` are build output and are ignored. Do not commit
  rendered PDFs, HTML or knitr caches.
- The sample chapters are inherited example prose from huskydown and Reed
  College, not documentation of this template. Do not treat their wording as a
  statement about how this repository works.
