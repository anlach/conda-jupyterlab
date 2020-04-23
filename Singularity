Bootstrap: docker
From: continuumio/miniconda3

%post
    PATH=/opt/conda/bin:$PATH
    conda install -c conda-forge -c bokeh -y \
        jupyterlab \
        ipywidgets \
        xarray \
        dask \
        black \
        nodejs \
        bokeh \
        jupyter_bokeh \
        jupyterlab_latex \
        ipyvuetify \

    export EXTENSIONS="\
        @jupyter-widgets/jupyterlab-manager \
        @krassowski/jupyterlab-lsp \
        @krassowski/jupyterlab_go_to_definition \
        dask-labextension \
        @ryantam626/jupyterlab_code_formatter \
        @bokeh/jupyter_bokeh \
        @jupyterlab/latex \
        jupyter-vuetify \
        @axlair/jupyterlab_vim \
    "
    jupyter labextension install $EXTENSIONS
    jupyter labextension disable $EXTENSIONS

    chmod -R ugo+w /opt/conda

%runscript
    jupyter lab --ip=0.0.0.0

%labels
    labextensions $EXTENSIONS

%help
    This container runs JupyterLab on the default port 8888. Since the container is 
    meant to be used with an overlay image, there are world write permissions on the 
    conda installation.
    
    Usage:
        Create an overlay (Ubuntu/Debian):
            mkdir -p overlay/upper
            dd if=/dev/zero of=overlay.img bs=1M count=1000
            mkfs.ext3 -d overlay overlay.img

        Create an overlay (CentOS):
            dd if=/dev/zero of=overlay.img bs=1M count=1000
            mkfs.ext3 overlay.img
            mkdir temp
            sudo mount overlay.img temp
            sudo chown -R $USER:$USER temp
            mkdir temp/upper
            sudo umount temp

        Run:
            singularity shell --overlay overlay.img jupyterlab_latest.sif