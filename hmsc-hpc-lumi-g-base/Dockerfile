ARG BASE_IMAGE
FROM $BASE_IMAGE AS base

ENV DEBIAN_FRONTEND=noninteractive \
    PIP_NO_CACHE_DIR=1 \
    PIP_ROOT_USER_ACTION=ignore

RUN apt-get update -qy && \
    apt-get install -qy --no-install-recommends \
        libopenblas0-openmp \
        && \
    apt-get clean


#########################################
# Build MAGMA
#########################################
FROM base as magma-build

RUN apt-get update -qy && \
    apt-get install -qy --no-install-recommends \
        build-essential gfortran \
        libopenblas-openmp-dev \
        && \
    apt-get clean

ARG MAGMA_VERSION

RUN mkdir -p /opt/magma/lib /opt/magma/include && \
    git clone --depth 1 --branch v$MAGMA_VERSION https://bitbucket.org/icl/magma.git /opt/magma-build && \
    cd /opt/magma-build && \
    cp make.inc-examples/make.inc.hip-gcc-openblas make.inc && \
    sed -i 's/__HIP_PLATFORM_HCC__/__HIP_PLATFORM_AMD__/g' Makefile && \
    nice make lib/libmagma.so CC=gcc CXX=g++ FORT=gfortran GPU_TARGET="gfx90a" OPENBLASDIR=/usr/lib/x86_64-linux-gnu/openblas-openmp -j8 && \
    cp /opt/magma-build/include/*.h /opt/magma/include/ && \
    cp /opt/magma-build/lib/*.so /opt/magma/lib/ && \
    cd /opt/magma && \
    rm -rf /opt/magma-build

# Build custom operator
RUN git clone https://github.com/hmsc-r/hmsc-hpc.git /opt/tfop-build && \
    cd /opt/tfop-build && \
    git checkout 63bdb3c0e87562b30592b8a9551fba7c48d579bf && \
    cd custom_ops && \
    make all MAGMA_ROOT=/opt/magma && \
    mkdir -p /opt/tfop && \
    cp *.so /opt/tfop/ && \
    cd / && \
    rm -rf /opt/tfop-build

#########################################
# Final container image
#########################################
FROM base

ARG TENSORFLOW_PROBABILITY_VERSION

# Install Hmsc-HPC dependencies
RUN python3 -m pip install \
        tensorflow_probability==$TENSORFLOW_PROBABILITY_VERSION \
        scipy \
        pyreadr \
        ujson

COPY --from=magma-build /opt/magma/ /opt/magma/

ENV CPATH=/opt/magma/include \
    LIBRARY_PATH=/opt/magma/lib \
    LD_LIBRARY_PATH=/opt/magma/lib

COPY --from=magma-build /opt/tfop/ /opt/tfop/

ENV HMSC_TFOP_LIB=/opt/tfop/magma_cholesky.so \
    HMSC_CHOLESKY_MAGMA_SWITCHSIZE=1000 \
    TF_FORCE_GPU_ALLOW_GROWTH=true \
    TF_CPP_MIN_LOG_LEVEL=3

ENTRYPOINT ["python3"]
CMD ["--help"]
