Bootstrap: docker
From: rocker/geospatial

%labels
  Author Adam M. Wilson
  Version 0.0.1
  #Adapted_From https://pawseysc.github.io/singularity-containers/23-web-rstudio/index.html


%environment
      export LISTEN_PORT=8787
      export LC_ALL=C
      export PATH=/usr/lib/rstudio-server/bin:${PATH}

%post

  # make some needed directories
  mkdir -p /var/run/rstudio-server/rstudio-rsession
  chmod -R  a+rwxt /var/run/rstudio-server

  # add authentication script
  wget -O /usr/lib/rstudio_auth https://raw.githubusercontent.com/AdamWilsonLab/singularity-geospatial-r/main/rstudio_auth.sh
  # Ensure execute permissions
  chmod a+rx /usr/lib/rstudio_auth


  # Get dependencies
  apt-get update -y
  apt-get upgrade -y  --allow-unauthenticated
  apt-get install -y --no-install-recommends --allow-unauthenticated \
        locales \
        libssl-dev \
        libxml2-dev \
        libcairo2-dev \
        libxt-dev \
        libcgal-dev \
        libglu1-mesa-dev \
        ca-certificates


# Add a default CRAN mirror
mkdir -p /usr/lib/R/etc/
echo "options(repos = c(CRAN = 'https://cran.rstudio.com/'), download.file.method = 'libcurl')" >> /usr/lib/R/etc/Rprofile.site

# Add a directory for host R libraries
mkdir -p /library
echo "R_LIBS_SITE=/library:\${R_LIBS_SITE}" >> /usr/lib/R/etc/Renviron.site


R -e "install.packages('maxnet', method='wget', quiet=T, verbose=F)"
R -e "install.packages('Morpho', method='wget', quiet=T, verbose=F)"
R -e "install.packages('rgl', method='wget', quiet=T, verbose=F)"
R -e "install.packages('ENMeval', method='wget', quiet=T, verbose=F)"
R -e "install.packages('scales', method='wget', quiet=T, verbose=F)"
R -e "install.packages('viridis', method='wget', quiet=T, verbose=F)"
R -e "install.packages('rjags', method='wget', quiet=T, verbose=F)"
R -e "install.packages('coda', method='wget', quiet=T, verbose=F)"
R -e "install.packages('mcmc', method='wget', quiet=T, verbose=F)"


%startscript
  export R_PORT=${R_PORT:-"8787"}
  export R_ADDRESS=${R_ADDRESS:-"127.0.0.1"}
  nc -lp $R_PORT
  rserver --www-port $R_PORT --www-address $R_ADDRESS --auth-none 0 --auth-pam-helper-path=/usr/lib/rstudio_auth