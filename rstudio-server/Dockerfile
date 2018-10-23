# Copyright (c) Gigantum

# The below is a relatively straightforward adaptation of directions at
# https://cran.r-project.org/bin/linux/ubuntu/README.html

FROM gigantum/python3-minimal:latest
LABEL maintainer="Gigantum <support@gigantum.com>"


# Install system level dependencies
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
        # Enables add-apt-repository
        software-properties-common \
        # These two packages required to import keys into apt
        gpg \
        gpg-agent \
        nginx \
    # Should we make this more robust? - can break because of docker networking
    # e.g., on Windows. See r-tidyverse base for an alternative.
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9 \
    && echo "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/" > /etc/apt/sources.list.d/cran.list \
    # This also triggers an update as of 18.04/bionic
    && add-apt-repository ppa:marutter/c2d4u3.5 \
    && apt-get install -yq --no-install-recommends \
        r-base \
        # We get our compilers here
        r-base-dev \
        r-recommended \
        r-cran-tidyverse \
        r-cran-data.table \
        r-cran-devtools \
        # Needed by IRkernel
        r-cran-repr \
        # OpenBLAS makes R much faster - it will be default upon install
        libopenblas-base \
        libopenblas-dev \
    # XXX Maybe we should use MRAN? It would certainly make subsequent projects
    # more reproducible, but is not in line with how we're using pip, conda,
    # etc. e.g.:
    # options(repos=c(CRAN="https://cran.microsoft.com/snapshot/2018-07-23/"))
    && echo 'options(repos = c(CRAN = "https://cloud.r-project.org/"))' >> /etc/R/Rprofile.site \
    # RStudio requirements - keeping separate with thoughts of a refactor
    && apt-get install -yq --no-install-recommends \
        psmisc \
        libapparmor1 \
        libedit2 \
        lsb-release \
        libssl1.0.0 \
    && apt-get clean

# The core of bioconductor is automatically installed by things like
# neuroconductor. This will speed up one-off package installation for repos
# that assume this as a pre-req:
RUN R -e 'source("https://bioconductor.org/biocLite.R"); biocLite()' \
    # Also Install IRkernal and set up the installed R interpreter in Jupyter
    R -e "devtools::install_github('IRkernel/IRkernel'); IRkernel::installspec(user=FALSE)" \
    # The above will error out by attempting to use the IRkernel package.
    # An alternative is to ensure packages are installed as follows:
    # R -e "stopifnot(all(c('IRkernel') %in% installed.packages()[, 'Package']))" \
    && rm -rf /tmp/*/downloaded_packages

# downloading the GPG key for RStudio is problematic, so we use the md5 hashe
# There are numerous potential issues with gpg, including firewalls that block
# all but web ports, and broken ipv6

# Using variables mostly to make it obvious what needs to be updated in future
ENV RSTUDIO_MD5=ea77929e40eac30baee9e336e26a1dd5 \
    RSTUDIO_VER=1.1.456

RUN wget https://download2.rstudio.org/rstudio-server-${RSTUDIO_VER}-amd64.deb \
    && echo $RSTUDIO_MD5 rstudio-server-${RSTUDIO_VER}-amd64.deb | md5sum -c \
    && dpkg -i rstudio-server-*-amd64.deb \
    && rm rstudio-server-*-amd64.deb

# Expose port for RStudio
EXPOSE 8787

COPY rserver.conf /etc/rstudio/rserver.conf
