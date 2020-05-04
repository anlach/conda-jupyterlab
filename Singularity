Bootstrap: docker
From: continuumio/miniconda3

%post
    apt install dnsutils -y

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
        @bokeh/jupyter_bokeh \
        @jupyterlab/latex \
        jupyter-vue \
        jupyter-vuetify \
        @axlair/jupyterlab_vim \
    "
    jupyter labextension install $EXTENSIONS
    jupyter labextension uninstall $EXTENSIONS

    chmod -R ugo+w /opt/conda

%runscript
    echo ""
    echo ":::Test for write access"
    WTFILE=/opt/conda/write_test
    if touch $WTFILE
    then
        echo "...passed"
        rm $WTFILE
    else
        echo "No write access! You should be using an overlay."
        exit 1
    fi

    echo ""
    echo ":::Test for chmod permissions on JupyterLab staging area"
    STAGING="/opt/conda/share/jupyter/lab/staging"
    if chmod u+w $STAGING
    then 
        echo "...passed"
    else 
        echo "...failed"
        echo "...removing staging area and rebuilding"
        rm -rf $STAGING
        jupyter lab build
    fi

    echo "Starting JupyterLab"
    IP="$(dig +short myip.opendns.com @resolver1.opendns.com)"
    exec jupyter lab --ip=$IP

%labels
    labextensions $EXTENSIONS

%help
    This container runs JupyterLab on the default port 8888. Since the container is 
    meant to be used with an overlay image, there are world write permissions on the 
    conda installation.
    
    Usage:
        Create an overlay using -d option of mkfs.ext3:
            mkdir -p overlay/upper
            dd if=/dev/zero of=overlay.img bs=1M count=1000
            mkfs.ext3 -d overlay overlay.img

        Create an overlay using sudo:
            dd if=/dev/zero of=overlay.img bs=1M count=1000
            mkfs.ext3 overlay.img
            mkdir temp
            sudo mount overlay.img temp
            sudo chown -R $USER:$USER temp
            mkdir temp/upper
            sudo umount temp

        Run:
            singularity run --overlay overlay.img conda-jupyterlab_latest.sif
