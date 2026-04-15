# biodepot_baseimages

Dockerfiles for BioDepot base images that don't belong to a specific
BWB widget. The curated analysis tiers live in
`BioDepot/bwb-bioc-images`; this repo is the layer below — R, Bioc,
Jupyter, Dorado — plus historical references.

## Layout

```
biodepot_baseimages/
├── r/                source-built R with explicit BLAS/LAPACK
├── bioc-r/           Bioconductor+R — two build paths, see bioc-r/README.md
├── jupyter-base/     Jupyter + Python+R (non-Bioc)
├── jupyter-bioc-r/   Jupyter + IRkernel on bioc-r — two build paths
└── dorado/           GPU basecaller image
```

## The two-path policy (bioc-r and jupyter-bioc-r)

For Bioconductor-flavored images we maintain **two parallel build
lineages** per release:

- **Primary (community path)** — builds FROM
  `bioconductor/bioconductor_docker:RELEASE_X_Y`. Tag:
  `biodepot/bioc-r:RELEASE_X_Y`. Fast to build, small, tracks the
  upstream community image.
- **Fallback (source-build path)** — builds FROM BioDepot's own
  `biodepot/rbase` (source-compiled R with explicit BLAS/LAPACK).
  Tag: `biodepot/bioc-r:RELEASE_X_Y-source`. Slower (~30 min R
  compile), owned end-to-end by BioDepot.

Both variants are built and pushed on every Bioc release bump. The
`-source` suffix is not a fallback-of-last-resort — it's a
first-class published image that workflows can pin to.

See `bioc-r/README.md` for when to reach for each path.

## Related repositories

- `BioDepot/bwb-bioc-images` — curated Bioc tier images (bulk-RNA,
  single-cell, conversion bridges). Built FROM
  `bioconductor/bioconductor_docker` directly today; may layer on
  `biodepot/bioc-r` in a future refactor.
- `BioDepot/biodepot-tools` — Perl orchestration for widget-level
  Dockerfiles.
- `BioDepot/BioDepot-workflow-builder` (BWB) — widget-per-Dockerfile
  repo; its images are separate from this chain.

## Bioconductor release cadence

Bioconductor publishes twice yearly (spring + fall). On each release:

1. Update the release pin in `bioc-r/<X.Y>/Dockerfile` and the
   `-source` variant.
2. Build both bioc-r variants; verify smoke test.
3. Build both jupyter-bioc-r variants; verify smoke test.
4. Push all four; file a PR to the catalog (`biodepot-catalog`)
   updating digests.

The update-agent skill at `~/.claude/skills/bioc-module-update/`
encodes this procedure.

## Historical note on `r/` + `bioc-r/-source`

The `r/` tier exists for Python+R Jupyter workflows that don't need
Bioconductor, and as the source-build dependency for the bioc-r
`-source` variant. If the community Bioc path is ever broken,
`biodepot/rbase` + `bioc-r/-source` is a fully BioDepot-owned escape
hatch. This is deliberate, not vestigial.
