# Containers for Hmsc

This repository contains container recipes for using Hmsc in supercomputers.

## Usage on LUMI-G for a stable release of Hmsc-HPC

> [!CAUTION]
> This simplified setup is not possible yet. Use the development version for now.

<details>

Login to LUMI and run the following first-time setup
(replace `IMAGE` with an available image version listed in [this page](../../pkgs/container/hmsc-hpc-lumi-g)):

    # Go to a work directory
    cd /scratch/project_...

    # Pull image
    singularity pull --disable-cache docker://ghcr.io/hmsc-r/hmsc-hpc-lumi-g:VERSION

    # Define path to the sif file
    export HMSC_SIF=$PWD/hmsc-hpc-lumi-g_VERSION.sif

    # Print out the path
    echo HMSC_SIF=$HMSC_SIF

Now Hmsc-HPC is ready to use. Please note the `HMSC_SIF` path printed above.

The following steps are needed to use the installation (use these lines in the sbatch job script):

    # Define path to the sif file
    export HMSC_SIF=...     # use the output from above here

    # Make filesystem paths visible in container
    export SINGULARITY_BIND="/pfs,/scratch,/projappl,/project,/flash,/appl"

    # Run hmsc-hpc
    singularity run $HMSC_SIF -m hmsc.run_gibbs_sampler ...

</details>


## Usage on LUMI-G for a development version of Hmsc-HPC

Login to LUMI and run the following first-time setup
(replace `IMAGE` with an available image version listed in [this page](../../pkgs/container/hmsc-hpc-lumi-g-base)):

    # Go to a work directory
    cd /scratch/project_...

    # Pull image
    singularity pull --disable-cache docker://ghcr.io/hmsc-r/hmsc-hpc-lumi-g-base:VERSION

    # Define paths to sif file and virtual environment
    export HMSC_SIF=$PWD/hmsc-hpc-lumi-g-base_VERSION.sif
    export HMSC_VENV=${HMSC_SIF%.sif}-venv
    export HMSC_PYTHON=$HMSC_VENV/bin/python3

    # Make filesystem paths visible in container
    export SINGULARITY_BIND="/pfs,/scratch,/projappl,/project,/flash,/appl"

    # Create a new virtual environment
    singularity exec $HMSC_SIF python3 -m venv --system-site-packages $HMSC_VENV

    # Install Hmsc-HPC development version in the virtual environment
    singularity exec $HMSC_SIF $HMSC_PYTHON -m pip install --no-deps git+https://github.com/hmsc-r/hmsc-hpc.git@main

    # Print out the paths
    echo HMSC_SIF=$HMSC_SIF
    echo HMSC_PYTHON=$HMSC_PYTHON

Now Hmsc-HPC is ready to use. Please note the `HMSC_SIF` and `HMSC_PYTHON` paths printed above.

The following steps are needed to use the installation (use these lines in the sbatch job script):

    # Define paths to sif file and virtual environment
    export HMSC_SIF=...     # use the output from above here
    export HMSC_PYTHON=...  # use the output from above here

    # Make filesystem paths visible in container
    export SINGULARITY_BIND="/pfs,/scratch,/projappl,/project,/flash,/appl"

    # Run hmsc-hpc
    singularity exec $HMSC_SIF $HMSC_PYTHON -m hmsc.run_gibbs_sampler ...

