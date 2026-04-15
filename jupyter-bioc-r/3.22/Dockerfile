# biodepot/jupyter-bioc-r:RELEASE_3_22  (primary / community path)
#
# Jupyter + IRkernel on top of the primary bioc-r variant (which is
# itself FROM bioconductor/bioconductor_docker). See ../README.md for the
# two-path policy; the -source sibling exists for users who need the
# BioDepot source-built chain.
#
# Build:
#   docker build \
#     --build-arg BIOC_RELEASE_TAG=RELEASE_3_22 \
#     -f jupyter-bioc-r/3.22/Dockerfile \
#     -t biodepot/jupyter-bioc-r:RELEASE_3_22 .

ARG BIOC_RELEASE_TAG=RELEASE_3_22
FROM biodepot/bioc-r:${BIOC_RELEASE_TAG}

ARG BIOC_RELEASE_TAG
ENV BIOC_RELEASE_TAG=${BIOC_RELEASE_TAG}
ENV DEBIAN_FRONTEND=noninteractive

LABEL org.opencontainers.image.source="https://github.com/BioDepot/biodepot_baseimages"
LABEL org.opencontainers.image.description="BioDepot Jupyter + IRkernel on Bioconductor (primary / community path)"
LABEL biodepot.tier="jupyter-bioc-r"
LABEL biodepot.variant="primary"

# bioconductor_docker ships with EXTERNALLY-MANAGED removed already, but
# guard for cases where it is present (it isn't on the community base,
# but this keeps the Dockerfile symmetric with the -source sibling).
RUN rm -f /usr/lib/python*/EXTERNALLY-MANAGED || true

# Headless Jupyter: serves over HTTP to the host browser, no in-container
# GUI browser required. The legacy BWB image bundled firefox-esr and xdg
# plumbing; we drop both (community base no longer ships firefox-esr).
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
