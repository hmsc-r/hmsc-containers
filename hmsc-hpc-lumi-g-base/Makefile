IMAGE_ROOT?=ghcr.io/hmsc-r
ROCM_VERSION=6.2
PYTHON_VERSION=3.9
TENSORFLOW_VERSION=2.16
TENSORFLOW_PROBABILITY_VERSION=0.24.0  # use version matching with TF version, see https://github.com/tensorflow/probability/releases
MAGMA_VERSION=2.8.0

IMAGE=hmsc-hpc-lumi-g-base
IMAGE_VERSION=0.3.0-beta.5

SIF_FILE=$(IMAGE_NAME)_$(IMAGE_VERSION).sif
TAR_FILE=$(IMAGE_NAME)_$(IMAGE_VERSION).tar

build: Dockerfile
	docker buildx build --platform linux/amd64 \
		--label "org.opencontainers.image.source=https://github.com/hmsc-r/hmsc-containers" \
		--label "org.opencontainers.image.description=Hmsc-HPC base environment for LUMI-G" \
		--build-arg BASE_IMAGE=docker.io/rocm/tensorflow:rocm$(ROCM_VERSION)-py$(PYTHON_VERSION)-tf$(TENSORFLOW_VERSION)-runtime \
		--build-arg TENSORFLOW_PROBABILITY_VERSION=$(TENSORFLOW_PROBABILITY_VERSION) \
		--build-arg MAGMA_VERSION=$(MAGMA_VERSION) \
		-t $(IMAGE_ROOT)/$(IMAGE):$(IMAGE_VERSION) \
		.

push:
	docker push $(IMAGE_ROOT)/$(IMAGE):$(IMAGE_VERSION)

singularity:
	rm -f $(SIF_FILE) $(TAR_FILE)
	docker save $(IMAGE_ROOT)/$(IMAGE_NAME):$(IMAGE_VERSION) -o $(TAR_FILE)
	singularity build $(SIF_FILE) docker-archive://$(TAR_FILE)
	rm -f $(TAR_FILE)
