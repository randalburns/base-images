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
ENV GIGANTUM_SHARE=/mnt/share \
    GIGANTUM_WORKDIR=/mnt/labbook \
    SHELL=/bin/bash \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8

# Install Rstudio-server
# Install conda, python, and jupyter
RUN cd /tmp && wget --quiet https://download2.rstudio.org/rstudio-server-1.1.456-amd64.deb && \
    sudo gdebi -n rstudio-server-1.1.456-amd64.deb && rm rstudio-server-1.1.456-amd64.deb

# Expose port for http proxy
EXPOSE 80
# Expose port for rstudio-server
EXPOSE 8787

# Set working dir
WORKDIR $GIGANTUM_WORKDIR

# Overwrite nginx.conf it's our proxy.
COPY nginx.conf /etc/nginx/nginx.conf
