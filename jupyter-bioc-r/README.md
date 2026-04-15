# jupyter-bioc-r — Jupyter + IRkernel on Bioconductor

Jupyter/IRkernel-in-container flavored on top of `bioc-r`. Mirrors
the `bioc-r/` two-path layout: each Bioc release has a primary
(community, FROM `biodepot/bioc-r:RELEASE_X_Y`) and a source-build
fallback (FROM `biodepot/bioc-r:RELEASE_X_Y-source`).

See `../bioc-r/README.md` for the full two-path policy. When to reach
for `-source` applies identically here: the chain is only as
community-dependent as its base.

## Layout

```
jupyter-bioc-r/
├── README.md
├── 3.22/Dockerfile          primary  →  biodepot/jupyter-bioc-r:RELEASE_3_22
├── 3.22-source/Dockerfile   fallback →  biodepot/jupyter-bioc-r:RELEASE_3_22-source
└── legacy/                  frozen historical combo(s)
```

## Build dependency

`jupyter-bioc-r` images always build AFTER their matching `bioc-r`
image. CI should sequence: build `biodepot/bioc-r:RELEASE_X_Y` and
`biodepot/bioc-r:RELEASE_X_Y-source` first, then the two
`jupyter-bioc-r` variants.
