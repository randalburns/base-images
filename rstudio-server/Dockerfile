# Copyright (c) Gigantum

# The below is a relatively straightforward adaptation of directions at
# https://cran.r-project.org/bin/linux/ubuntu/README.html

FROM gigantum/r-tidyverse:f086714bd9-2019-12-18
LABEL maintainer="Gigantum <support@gigantum.com>"

# Install system level dependencies
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
        # RStudio requirements for notebooks
        r-cran-catools \
        r-cran-bitops \
    && apt-get clean

# downloading the GPG key for RStudio is problematic, so we use the md5 hash
# There are numerous potential issues with gpg, including firewalls that block
# all but web ports, and broken ipv6

# Using variables mostly to make it obvious what needs to be updated in future
ENV RSTUDIO_SHA256=62b1fb2f6f48342518d75b6efb6e721b5a49991d1642e3b879e4c5ed03cee875 \
    RSTUDIO_VER=1.2.5033

RUN wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-${RSTUDIO_VER}-amd64.deb \
    && echo $RSTUDIO_SHA256 rstudio-server-${RSTUDIO_VER}-amd64.deb | sha256sum -c \
    && apt-get install -yq --no-install-recommends ./rstudio-server-*-amd64.deb \
    && apt-get clean \
    && rm rstudio-server-*-amd64.deb

# Expose port for RStudio
EXPOSE 8787

# It's best to have COPYs at the end so you can change them without a massive
# rebuild
COPY rserver.conf /etc/rstudio/rserver.conf
# This will get copied out at Project Container creation
COPY user-settings /tmp
