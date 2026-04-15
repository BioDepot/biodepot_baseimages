# Legacy bioc-r combos

Source preserved, no active CI. Docker Hub images for these tags stay
immutable for reproducibility; this directory holds the Dockerfiles
they were built from.

## Contents

| Directory | Era | Base chain |
| --- | --- | --- |
| `3.6-ubuntu-16.04-r-3.4.3/` | 2018 | ubuntu:16.04, R 3.4.3 (BiocInstaller) |
| `3.6-ubuntu-18.04-r-3.4.3/` | 2018 | ubuntu:18.04, R 3.4.3 (BiocInstaller) |
| `3.7-ubuntu-16.04-r-3.5.1/` | 2018 | ubuntu:16.04, R 3.5.1 |
| `3.7-ubuntu-18.04-r-3.5.1/` | 2018 | ubuntu:18.04, R 3.5.1 |
| `3.14-ubuntu-20.04-r-4.1.0/` | 2021 | ubuntu:20.04, R 4.1.0 |
| `3.16-bookworm-slim-r-4.2.3/` | 2022 | debian:bookworm-slim, R 4.2.3 |
| `3.16-ubuntu-22.04-r-4.2.2/` | 2022 | ubuntu:22.04, R 4.2.2 |
| `3.17-bookworm-slim-r-4.2.3/` | 2023 | debian:bookworm-slim, R 4.2.3 |
| `3.18-bookworm-slim-r-4.3.2/` | 2023 | debian:bookworm-slim, R 4.3.2 |
| `root-Dockerfile` | 2018 | ubuntu:18.04, R 3.4.3 compiled from source |

## To resurrect one

1. Move its directory out of `legacy/` back into `bioc-r/` (or into
   `bioc-r/<release>-legacy/` if the release is also live).
2. Confirm the upstream base image is still available on Docker Hub.
3. Add a workflow_dispatch target in CI.
4. Publish under a fresh `:legacy-{original-tag}-rebuilt-YYYYMMDD` tag
   so existing consumers of the original tag aren't disturbed.

The catalog (`BioDepot/biodepot-catalog`) marks these as
`status: legacy`.
