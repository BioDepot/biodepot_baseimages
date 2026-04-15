FROM ubuntu:18.04

# Prepare R environment
ENV RHOME_DIR /usr/local/rhome
ENV PATH $RHOME_DIR/bin:$PATH
RUN mkdir -p $RHOME_DIR

# R pre-requisites
#To get R's blas and lapack must compile from source NOT from deb

RUN apt-get update && \
    apt-get install -y --no-install-recommends apt-utils fonts-dejavu \
    build-essential xorg-dev gcc gobjc++ gfortran libblas-dev libcairo2-dev liblzma-dev libreadline-dev aptitude \
    libbz2-dev libpcre3-dev libcurl4-openssl-dev libssl-dev libxml2-dev \
    software-properties-common wget texinfo texlive texlive-fonts-extra default-jdk && \
				cd /tmp && wget https://cran.r-project.org/src/base/R-3/R-3.5.1.tar.gz && \
    tar -xzvf R-3.5.1.tar.gz && \
    cd /tmp/R-* && ./configure && \
    cd /tmp/R-* && make -j 8 && \
    cd /tmp/R-* && make install rhome=$RHOME_DIR \
    && rm -rf /tmp/R-* \
    && apt-get remove -y build-essential wget\
    && apt-get autoclean -y \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo R_UNZIPCMD="'"internal"'"  > /root/.Renviron
RUN Rscript -e 'install.packages("BiocInstaller",repos="http://bioconductor.org/packages/3.7/bioc",lib="/usr/local/rhome/lib/")'

LABEL "tag.0"="3.7"
LABEL "tag.1"="r-3.5.1"