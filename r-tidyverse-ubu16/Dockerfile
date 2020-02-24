# Copyright (c) Gigantum

# Note, some docker commands taken from/inspired by the standard docker-stacks maintained
# by the Jupyter team: https://github.com/jupyter/docker-stacks/
# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.

# Start Image Def
FROM ubuntu:16.04
LABEL maintainer="Gigantum <support@gigantum.com>"
ENV DEBIAN_FRONTEND noninteractive

# Install system level dependencies
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
        wget \
        bzip2 \
        ca-certificates \
        gcc \
        g++ \
        make \
        locales \
        fonts-liberation \
        gosu \
        tzdata \
        git \
    && apt-get clean

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

# Setup environment variables
ENV CONDA_DIR=/opt/conda \
    GIGANTUM_SHARE=/mnt/share \
    GIGANTUM_WORKDIR=/mnt/labbook \
    SHELL=/bin/bash \
    MINICONDA_VERSION=4.7.12.1 \
    MINICONDA_MD5=81c773ff87af5cfac79ab862942ab6b3 \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8
ENV PATH=$CONDA_DIR/bin:$PATH \
    JUPYTER_RUNTIME_DIR=$GIGANTUM_SHARE/jupyter/runtime

# Install conda, python, and jupyter
RUN cd /tmp && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
    echo "$MINICONDA_MD5 *Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh" | md5sum -c - && \
    /bin/bash Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
    conda config --system --prepend channels conda-forge && \
    # We don't want the `r` channel
    conda config --system --remove channels defaults && \
    conda config --system --append channels anaconda && \
    # Recommended by conda-forge
    conda config --set channel_priority strict && \
    conda config --system --set auto_update_conda false && \
    conda config --system --set show_channel_urls true && \
    # We update conda explicitly to get any gains in performance, etc.
    conda update --quiet --yes conda && \
    conda update --all --quiet --yes && \
    # notebook 5.3 through 5.7.1 have security holes
    conda install --quiet --yes -c conda-forge jupyterlab=1.2.4 'notebook>=6.0,<6.1' && \
    # This is currently installed by default by conda, but it's mission
    # critical so we make it explicit
    conda install pip && \
    # We pin the version of nodejs so we can be sure to re-install the same
    # version later - avoiding jupyterlab extension rebuilds and generally
    # improving compatability
    conda install --quiet --yes nodejs==12.13.0 && \
    pip install nbresuse && \
    # enable memory usage reporting in the statusbar (now installed by default)
    jupyter serverextension enable --py nbresuse && \
    jupyter labextension install @jupyterlab/toc && \
    npm cache clean --force && \
    rm -rf $CONDA_DIR/share/jupyter/lab/staging && \
    rm -rf /root/.cache/yarn && \
    rm -rf /root/.node-gyp && \
    conda remove --quiet --yes nodejs && \
    conda clean --all

# Expose port for jupyterlab
EXPOSE 8888

# Set working dir
WORKDIR $GIGANTUM_WORKDIR

# CMD may be updated during final image assembly, but this keeps a container alive.
CMD ["tail", "-f", "/dev/null"]

# I downloaded this key using
# gpg --keyserver keyserver.ubuntu.com --recv-key 0x51716619e084dab9
# gpg -a --export 0x51716619e084dab9 > rutter-cran-key.txt
# Could also directly do: apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0x51716619e084dab9
# gpg and gpg-agent are required in any case
COPY rutter-cran-key.txt /tmp/

# Install system level dependencies
RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
        # Enables add-apt-repository
        software-properties-common \
        apt-transport-https \
        # These two packages required to import keys into apt
        gnupg \
        gnupg-agent \
    && apt-key add /tmp/rutter-cran-key.txt \
    && echo "deb https://cloud.r-project.org/bin/linux/ubuntu xenial-cran35/" > /etc/apt/sources.list.d/cran.list \
    # This also triggers an update as of 18.04/bionic
    && add-apt-repository ppa:marutter/c2d4u3.5 \
    && apt-get update 

    run apt-get install -yq --no-install-recommends \
        r-base \
        # We get any remaining needed compilers here
        r-base-dev \
        r-recommended \
        r-cran-tidyverse \
        r-cran-data.table \
        r-cran-devtools \
        # Needed by IRkernel
        r-cran-repr \
#        r-cran-pbdzmq \
        r-cran-uuid \
        # OpenBLAS makes R much faster - it will be default upon install
        libopenblas-base \
        libopenblas-dev \
    && apt-get clean

# TODO DC Maybe we should use MRAN? It would certainly make subsequent projects
# more reproducible, but is not in line with how we're using pip, conda,
# etc. e.g.:
# options(repos=c(CRAN="https://cran.microsoft.com/snapshot/2018-07-23/"))
RUN echo 'options(repos = c(CRAN = "https://cloud.r-project.org/"))\n' >> /etc/R/Rprofile.site \
    # Also Install IRkernal and set up the installed R interpreter in Jupyter
    && R -e "install.packages('IRkernel', upgrade_dependencies=FALSE); IRkernel::installspec(user=FALSE)"
    # The above will error out by attempting to use the IRkernel package.
    # An alternative is to ensure packages are installed as follows:
    # The below did not succeed even when biocLite() was run above
    # && R -e "stopifnot(all(c('IRkernel', 'BiocManager') %in% installed.packages()[, 'Package']))" \
    # The core of bioconductor is automatically installed by things like
    # neuroconductor. This will speed up one-off package installation for repos
    # that assume this as a pre-req:
    # BUT, it also seems to override numerous packages from CRAN... so skipping for now
    # && R -e 'source("https://bioconductor.org/biocLite.R"); biocLite()' \


