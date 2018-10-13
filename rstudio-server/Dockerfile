# Copyright (c) Gigantum

# Note, some docker commands taken from/inspired by the standard docker-stacks maintained
# by the Jupyter team: https://github.com/jupyter/docker-stacks/
# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.

FROM ubuntu:18.04
LABEL maintainer="Gigantum <hello@gigantum.com>"

# This is needed for tzdata to proceed, but should probably be set generally
ENV DEBIAN_FRONTEND=noninteractive

# Install system level dependencies
RUN apt-get update && \
    apt-get install -yq --no-install-recommends \
    wget \
    bzip2 \
    ca-certificates \
    gcc \
    g++ \
    make \
    nginx \
    r-base \
    r-base-dev \
    sudo \
    locales \
    libxrender1 \
    libxext6 \
    # Other basic R stuff breaks without the Olson database
    tzdata \
    fonts-liberation \
    gosu \
    gdebi-core \
    git && \
    apt-get clean 

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

# Setup environment variables
ENV CONDA_DIR=/opt/conda \
    GIGANTUM_SHARE=/mnt/share \
    GIGANTUM_WORKDIR=/mnt/labbook \
    SHELL=/bin/bash \
    MINICONDA_VERSION=4.5.4 \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8
    
ENV PATH=$CONDA_DIR/bin:$PATH 

# Install conda
RUN cd /tmp && wget --quiet https://repo.continuum.io/miniconda/Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
    echo "a946ea1d0c4a642ddf0c3a26a18bb16d *Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh" | md5sum -c - && \
    /bin/bash Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
    conda config --system --prepend channels conda-forge && \
    conda config --system --set auto_update_conda false && \
    conda config --system --set show_channel_urls true && \
    conda update --all --quiet --yes

# Install R packages
# For R, the Anaconda "official" packages seem better than conda-forge
   RUN conda install --yes \
    # These install a lot of stuff. We'll write them broken out in the manifest
    # in environment-components
       'r-essentials' \
       'r-tidyverse'  && \
       conda clean --all
    # data.table is included in the above
    # This is a nice javascript viz for tables called "DataTables", not relevant until we have shiny
    # r-dt=0.2 \
#       && conda clean --all

# Install Rstudio-server
RUN cd /tmp && wget --quiet https://download2.rstudio.org/rstudio-server-1.1.456-amd64.deb && \
    sudo gdebi -n rstudio-server-1.1.456-amd64.deb && rm rstudio-server-1.1.456-amd64.deb

# Expose port for rstudio-server
EXPOSE 8787

# Set working dir
WORKDIR $GIGANTUM_WORKDIR

COPY rserver.conf /etc/rstudio/rserver.conf
