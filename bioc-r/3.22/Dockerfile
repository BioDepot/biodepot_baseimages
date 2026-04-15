# biodepot/bioc-r:RELEASE_3_22  (primary / community path)
#
# Thin wrapper over bioconductor/bioconductor_docker. Exists so downstream
# BioDepot images and workflows can pin to biodepot/bioc-r:RELEASE_X_Y
# without caring whether the underlying base is upstream-community or
# BioDepot-source-built.
#
# See ../README.md for the two-path policy. Use ../3.22-source/Dockerfile
# when the community path is broken or a source-build is required.
#
# Build (local):
#   docker build \
#     --build-arg BIOC_RELEASE=3.22 \
#     --build-arg BIOC_RELEASE_TAG=RELEASE_3_22 \
#     -f bioc-r/3.22/Dockerfile \
#     -t biodepot/bioc-r:RELEASE_3_22 .

ARG BIOC_RELEASE_TAG=RELEASE_3_22
FROM bioconductor/bioconductor_docker:${BIOC_RELEASE_TAG}

ARG BIOC_RELEASE=3.22
ARG BIOC_RELEASE_TAG
ENV BIOC_RELEASE=${BIOC_RELEASE}
ENV BIOC_RELEASE_TAG=${BIOC_RELEASE_TAG}

LABEL org.opencontainers.image.source="https://github.com/BioDepot/biodepot_baseimages"
LABEL org.opencontainers.image.description="BioDepot Bioconductor+R base image (primary / community path)"
LABEL biodepot.tier="bioc-r"
LABEL biodepot.variant="primary"
LABEL biodepot.bioconductor.release="${BIOC_RELEASE}"

# No additional packages: this image is a pin point, not a tier.
# Downstream images (jupyter-bioc-r, bwb-bioc-images tiers) add packages.

WORKDIR /workspace
