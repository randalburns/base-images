# Note, some docker commands taken from/inspired by the standard docker-stacks maintained
# by the Jupyter team: https://github.com/jupyter/docker-stacks/
# which are Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.

FROM gigantum/python3-minimal:87c90e7834-2019-07-19
LABEL maintainer="Gigantum <support@gigantum.com>"


RUN apt-get update \
    && apt-get install -yq --no-install-recommends \
        # These are needed by some conda-installed packages, such as opencv
        # Note that since our R bases use apt, this gets taken care of
        # automatically there
        libxrender1 \
        libxext6 \
        libgl1-mesa-glx \
    && apt-get clean

# Install Python packages

# nodejs is for jupyter lab extensions below
# It's pinned to match python3-minimal, to avoid the need to rebuild extensions
RUN conda install --quiet --yes nodejs=12.13.0 && \
    conda install --quiet --yes \
        'blas=*=openblas' \
        'bokeh=1.4' \
        'cloudpickle=1.2' \
        'cython=0.29' \
        # This is a dependency of other packages - may as well list explicitly
        # Some not currently explicit, but maybe "data science" are fastcache and pyparsing
        'dask-core=2.9' \
        'dill=0.3' \
        'h5py=2.10' \
        'hdf5=1.10' \
        'ipywidgets=7.5' \
        'matplotlib=3.1' \
        # Another dependency made explicit
        'networkx=2.4' \
        'numba=0.46' \
        'numexpr=2.7' \
        'numpy=1.17' \
        'pandas=0.25' \
        'patsy=0.5' \
        'pillow=6.2' \
        'psutil=5.6' \
        # Another dependency made explicit
        'pywavelets=1.1' \
        # Another dependency made explicit
        'pyyaml=5.2' \
        'scikit-image=0.16' \
        'scikit-learn=0.22' \
        'scipy=1.3' \
        'seaborn=0.9' \
        'sqlalchemy=1.3' \
        'statsmodels=0.10' \
        'sympy=1.5' \
        'xarray=0.14' \
        'xlrd=1.2'  \
    && conda install --quiet --yes -c plotly 'plotly=4.4' 'plotly-orca=1.2' \
    # Note - we used to remove pyqt and qt since we are mostly browser-based, but the conda solver pulls it in as part
    # of any subsequent installation, so it's better to just leave it installed to save space and time
    && conda clean --all

# Set up additional widgets
RUN export NODE_OPTIONS=--max-old-space-size=4096 && \
    jupyter labextension install jupyterlab_bokeh --no-build && \
    jupyter labextension install @jupyterlab/geojson-extension --no-build && \
    # Activate ipywidgets extensions for JupyterLab
    jupyter labextension install @jupyter-widgets/jupyterlab-manager@1.1 --no-build && \
    jupyter labextension install plotlywidget@1.4.0 --no-build && \
    jupyter labextension install jupyterlab-plotly@1.4.0 --no-build && \
    # Build all of the above
    jupyter lab build && \
    # unset just in case
    unset NODE_OPTIONS && \
    # Activate ipywidgets for notebook server just in case
    jupyter nbextension enable --py widgetsnbextension --sys-prefix && \
    npm cache clean --force && \
    rm -rf $CONDA_DIR/share/jupyter/lab/staging && \
    rm -rf /root/.cache/yarn && \
    rm -rf /root/.node-gyp


# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME /opt/matplotlib/.cache/
RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot"
