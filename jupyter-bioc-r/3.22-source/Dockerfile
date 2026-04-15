# biodepot/jupyter-bioc-r:RELEASE_3_22-source  (fallback / source-build path)
#
# Jupyter + IRkernel on top of the source-build bioc-r variant. Used when
# the community chain is broken or a source-built toolchain is required.
# See ../README.md for the two-path policy.
#
# Build:
#   docker build \
#     --build-arg BIOC_RELEASE_TAG=RELEASE_3_22 \
#     -f jupyter-bioc-r/3.22-source/Dockerfile \
#     -t biodepot/jupyter-bioc-r:RELEASE_3_22-source .

ARG BIOC_RELEASE_TAG=RELEASE_3_22
FROM biodepot/bioc-r:${BIOC_RELEASE_TAG}-source

ARG BIOC_RELEASE_TAG
ENV BIOC_RELEASE_TAG=${BIOC_RELEASE_TAG}
ENV DEBIAN_FRONTEND=noninteractive

LABEL org.opencontainers.image.source="https://github.com/BioDepot/biodepot_baseimages"
LABEL org.opencontainers.image.description="BioDepot Jupyter + IRkernel on Bioconductor (source-build fallback path)"
LABEL biodepot.tier="jupyter-bioc-r"
LABEL biodepot.variant="source"

# Source-built biodepot/rbase lineage still has EXTERNALLY-MANAGED in
# place; pip install refuses without this removal.
RUN rm -f /usr/lib/python*/EXTERNALLY-MANAGED || true

# Headless Jupyter: no in-container browser needed. Mirrors the primary
# variant's package set for parity.
RUN apt-get update && apt-get install -y --no-install-recommends \
      python3-all python3-pip sudo \
      libfontconfig1-dev libharfbuzz-dev libfribidi-dev git libtiff-dev \
      libzmq3-dev build-essential \
    && pip install --break-system-packages jupyter \
    && R -e "install.packages(c('IRdisplay', 'IRkernel'), repos = 'http://cran.us.r-project.org'); IRkernel::installspec(user = FALSE)" \
    && apt-get autoclean -y \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo 'options(bitmapType="cairo")' >> ~/.Rprofile

WORKDIR /workspace
