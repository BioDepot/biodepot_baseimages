FROM biodepot/bioc-r:3.18-r-4.3.2__bookworm-slim
ENV DEBIAN_FRONTEND=noninteractive
#need so that you can use pip withou venv
RUN rm  /usr/lib/python3.11/EXTERNALLY-MANAGED
RUN apt-get update && apt-get -y install firefox-esr xdg-utils python3-all python3-pip sudo libfontconfig1-dev libharfbuzz-dev libfribidi-dev git libtiff-dev libzmq3-dev build-essential libgit2-dev\
 && pip install pip==22.3.1 \
 && pip install jupyter \
 && R -e "install.packages(c('IRdisplay', 'devtools' ),repos = 'http://cran.us.r-project.org'); devtools::install_github('IRkernel/IRkernel'); IRkernel::installspec()" \
 && apt-get autoclean -y \
 && apt-get autoremove -y \
 && rm -rf /var/lib/apt/lists/*
#This gives an error signal even though it is successful we put it in a combo so that the error is ignored
RUN xdg-settings set default-web-browser firefox.desktop || echo 1
COPY .mozilla ~/.mozilla

#To prevent helvetica from being used 
RUN echo 'options(bitmapType="cairo")' >> ~/.Rprofile

