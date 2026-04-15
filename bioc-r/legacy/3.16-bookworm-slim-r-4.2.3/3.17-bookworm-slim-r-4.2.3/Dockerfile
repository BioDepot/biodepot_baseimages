FROM biodepot/rbase:4.2.3__bookworm-slim

#needed so that there is no query for time zone
ARG DEBIAN_FRONTEND=noninteractive

#define different versions 
ARG BIOC_VERSION=3.16
#older versions of bioconductor us BiocInstaller
ARG BIOC_INSTALLER=BiocManager


#Versions 3.5 and newer use BiocManager
RUN if [ "$BIOC_INSTALLER" = BiocManager ] ; then \
    Rscript -e 'install.packages("BiocManager",repos="http://cran.us.r-project.org"); BiocManager::install(version = ""${BIOC_VERSION}"")' ; \
  else \
    RUN Rscript -e 'install.packages("BiocInstaller",repos="http://bioconductor.org/packages/$BIOC_VERSION/bioc",lib="/usr/local/rhome/lib/")' ; \
  fi
