# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM ckemere/minimal-notebook

MAINTAINER Caleb Kemere <ckemere@gmail.com>

USER root

####

# libav-tools for matplotlib anim
RUN apt-get update && \
    apt-get install -y --no-install-recommends libav-tools && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# stuff for dask and nelpy
RUN apt-get update -yqq &&  \
    apt-get install -yqq gcc libgl1-mesa-glx graphviz && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

USER $NB_USER

# Install Python 3 packages
# Remove pyqt and qt pulled in for matplotlib since we're only ever going to
# use notebook-friendly backends in these images
RUN conda install --quiet --yes \
    pip \
    notebook \
    matplotlib \
    numpy \
    lz4 \
    pandas \
    joblib \
    nomkl \
    ipywidgets \
    pandas \
    numexpr \
    matplotlib \
    scipy \
    seaborn \
    scikit-learn \
    scikit-image \
    cython \
    cloudpickle \
    dill \
    numba \
    pytables \
    datashader \
    tornado \
    fastparquet \
    s3fs \
    gcsfs \
    zict \
    python-blosc \
    cytoolz && \
    conda clean -tipsy && \
    # Activate ipywidgets extension in the environment that runs the notebook server
    jupyter nbextension enable --py widgetsnbextension --sys-prefix && \
    fix-permissions $CONDA_DIR

RUN conda install dask distributed -c conda-forge && conda clean -tipsy && \
    fix-permissions $CONDA_DIR

RUN conda install bokeh -c bokeh && conda clean -tipsy && \
    fix-permissions $CONDA_DIR

# Nelpy-specific (make sure to install develop branch!)
RUN pip install git+https://github.com/eackermann/hmmlearn
# RUN pip install git+https://github.com/nelpy/nelpy@develop
RUN pip install git+https://github.com/ckemere/nelpy@enable-gcloud-loads


# Install facets which does not have a pip or conda package at the moment
RUN cd /tmp && \
    git clone https://github.com/PAIR-code/facets.git && \
    cd facets && \
    jupyter nbextension install facets-dist/ --sys-prefix && \
    rm -rf facets && \
    fix-permissions $CONDA_DIR

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME /home/$NB_USER/.cache/
RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
    fix-permissions /home/$NB_USER

# Expose the default docker watcher port
EXPOSE 8787

USER $NB_USER
