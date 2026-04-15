# biodepot/bioc-r:RELEASE_3_22-source  (fallback / source-build path)
#
# Builds Bioc 3.22 on top of biodepot/rbase — BioDepot's source-built R
# with explicit BLAS/LAPACK. Slower than the primary path (~30 min vs
# ~2 min) but owned end-to-end by BioDepot: no upstream dependency.
#
# See ../README.md for when to reach for this variant. Default consumers
# should use the primary ../3.22/Dockerfile unless one of the conditions
# in that doc applies.
#
# Build (local):
#   docker build \
#     --build-arg BIOC_RELEASE=3.22 \
#     --build-arg RBASE_TAG=4.5.2__bookworm-slim \
#     -f bioc-r/3.22-source/Dockerfile \
#     -t biodepot/bioc-r:RELEASE_3_22-source .
#
# Note: Bioc 3.22 requires R >= 4.5, so RBASE_TAG is pinned to
# 4.5.2__bookworm-slim (matches the R version in
# bioconductor/bioconductor_docker:RELEASE_3_22 for parity).

ARG RBASE_TAG=4.5.2__bookworm-slim
FROM biodepot/rbase:${RBASE_TAG}

ARG BIOC_RELEASE=3.22
ARG RBASE_TAG
ARG DEBIAN_FRONTEND=noninteractive

ENV BIOC_RELEASE=${BIOC_RELEASE}
ENV RBASE_TAG=${RBASE_TAG}

LABEL org.opencontainers.image.source="https://github.com/BioDepot/biodepot_baseimages"
LABEL org.opencontainers.image.description="BioDepot Bioconductor+R base image (source-build fallback path)"
LABEL biodepot.tier="bioc-r"
LABEL biodepot.variant="source"
LABEL biodepot.bioconductor.release="${BIOC_RELEASE}"

# Install BiocManager and pin to the requested Bioc release. No tier
# packages: downstream images (jupyter-bioc-r, bwb-bioc-images) install
# their own stacks on top. Hardened to fail loudly — BiocManager::install
# is notorious for emitting non-zero-exit-status warnings while exiting 0.
RUN Rscript -e "\
  install.packages('BiocManager', repos='http://cran.us.r-project.org'); \
  withCallingHandlers( \
    BiocManager::install(version = Sys.getenv('BIOC_RELEASE'), ask = FALSE, update = FALSE), \
    warning = function(w) { \
      msg <- conditionMessage(w); \
      fatal <- c( \
        'installation of package .* had non-zero exit status', \
        'had non-zero exit status', \
        'installation of one or more packages failed', \
        'dependenc.* .* (are|is) not available', \
        'package .* (is|are) not available' \
      ); \
      for (p in fatal) if (grepl(p, msg, ignore.case = TRUE)) stop('fatal: ', msg); \
      invokeRestart('muffleWarning'); \
      message('benign warning: ', msg) \
    } \
  ); \
  stopifnot(as.character(BiocManager::version()) == Sys.getenv('BIOC_RELEASE'))"

WORKDIR /workspace
