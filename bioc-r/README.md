# bioc-r — Bioconductor-on-R base images

This tier provides **two parallel build paths** for each Bioconductor
release. Both paths are first-class, both are built and pushed on each
release bump. Agents, humans, and CI should treat them as siblings —
not as primary + abandoned alternative.

## The two paths

| Variant | Base image | Tag | When to use |
| --- | --- | --- | --- |
| **Primary** (community) | `bioconductor/bioconductor_docker:RELEASE_X_Y` | `biodepot/bioc-r:RELEASE_X_Y` | Default. Faster to build, smaller, proven. |
| **Fallback** (source-build) | `biodepot/rbase:4.3.2__bookworm-slim` | `biodepot/bioc-r:RELEASE_X_Y-source` | When the community path has a problem. |

The `-source` suffix marks the fallback. Same Bioc release, different
build lineage.

## When to reach for the fallback

Use `:RELEASE_X_Y-source` when any of these apply:

- Upstream `bioconductor/bioconductor_docker:RELEASE_X_Y` is broken,
  missing, or lagging a release we need.
- BLAS/LAPACK behavior in the primary diverges from what a workflow
  requires (the source variant compiles R with explicit BLAS+LAPACK
  via `biodepot/rbase`, not the deb packages).
- Reproducibility demands a BioDepot-owned toolchain end to end —
  no upstream dependency.
- Security posture / supply-chain review requires BioDepot-controlled
  base layers.

Otherwise use the primary. It is cheaper to build, smaller on disk,
and tracks the upstream Bioconductor community image.

## Build-time behavior

- Both variants are built on every Bioc release bump.
- The source variant takes ~30 min (R compiles from source); the
  primary takes ~2 min (pull + `BiocManager::install` the current
  release).
- CI runs a smoke test inside each image before pushing. Smoke tests
  verify `R.version`, `BiocManager::version()`, and package load paths.

## Layout

```
bioc-r/
├── README.md           (this file)
├── 3.22/Dockerfile     primary  →  biodepot/bioc-r:RELEASE_3_22
├── 3.22-source/Dockerfile   fallback  →  biodepot/bioc-r:RELEASE_3_22-source
└── legacy/             old OS/R/Bioc combos, frozen
    ├── 3.6-ubuntu-16.04-r-3.4.3/
    ├── 3.7-ubuntu-16.04-r-3.5.1/
    └── ... (9 combos preserved, no CI)
```

## Relationship to other tiers

- `biodepot/jupyter-bioc-r:RELEASE_X_Y` builds FROM the matching
  `biodepot/bioc-r:RELEASE_X_Y` and exists in the same two variants.
- `biodepot/rbase` (`../r/`) is unchanged. It is the source-path
  dependency of `bioc-r`'s `-source` variant **and** the base of
  `jupyter-base` for non-Bioc Python+R workflows.
- `bwb-bioc-images` (separate repo) provides curated Bioc tiers
  (bulk-RNA, single-cell, bridges) and currently builds FROM
  `bioconductor/bioconductor_docker` directly, not FROM
  `biodepot/bioc-r`. We can revisit layering once both paths are in
  production.

## Policy for agents

When an update skill processes a new Bioc release:

1. Build and push **both** variants. Do not skip `-source` because
   the primary succeeded.
2. If the primary build fails, file an issue and still publish the
   fallback. Do not hold the fallback for the primary.
3. If the fallback build fails, file an issue against `biodepot/rbase`
   — the upstream chain likely regressed.
4. Document which variant a consumer prefers in the catalog's
   `requires_runtime` or equivalent — the catalog is authoritative,
   not this README.

## Legacy

`legacy/` holds nine Bioc+OS+R combos from 2018–2023. Docker Hub
images for those tags remain immutable for reproducibility; source
is preserved here with no active CI. To resurrect one:

1. Move its directory out of `legacy/` back into `bioc-r/`
2. Confirm the upstream base image is still available
3. Add a workflow_dispatch target for it
4. Do NOT rebuild under the old tag — publish under a fresh
   `:legacy-{original-tag}-rebuilt-YYYYMMDD` tag so historical
   consumers aren't disturbed.
