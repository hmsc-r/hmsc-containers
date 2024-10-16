ARG BASE_IMAGE
FROM $BASE_IMAGE

ENV PIP_NO_CACHE_DIR=1

ARG TENSORFLOW_PROBABILITY_VERSION

# Install Hmsc-HPC dependencies
RUN python3 -m pip install \
        tensorflow_probability==$TENSORFLOW_PROBABILITY_VERSION \
        scipy \
        pyreadr \
        ujson

ENV TF_FORCE_GPU_ALLOW_GROWTH=true

ENTRYPOINT ["python3"]
CMD ["--help"]
