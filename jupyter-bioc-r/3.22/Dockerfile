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

RUN apt-get update && apt-get install -y --no-install-recommends \
      firefox-esr xdg-utils python3-all python3-pip sudo \
      libfontconfig1-dev libharfbuzz-dev libfribidi-dev git libtiff-dev \
      libzmq3-dev build-essential libgit2-dev \
    && pip install --break-system-packages jupyter \
    && R -e "install.packages(c('IRdisplay', 'devtools'), repos = 'http://cran.us.r-project.org'); devtools::install_github('IRkernel/IRkernel'); IRkernel::installspec()" \
    && apt-get autoclean -y \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN xdg-settings set default-web-browser firefox.desktop || echo 1
RUN echo 'options(bitmapType="cairo")' >> ~/.Rprofile

WORKDIR /workspace
