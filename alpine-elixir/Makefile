.PHONY: help

ALPINE_VERSION = $(shell awk '/^alpine/{print $$2}' VERSIONS)
ALPINE_DIGEST = $(shell awk '/^alpine/{print $$3}' VERSIONS)

ELIXIR_VERSION := $(shell awk '/^elixir/{print $$2}' VERSIONS)
ELIXIR_MAJ_VERSION := $(shell echo $(ELIXIR_VERSION) | cut -d. -f1)
ELIXIR_MIN_VERSION := $(ELIXIR_MAJ_VERSION).$(shell echo $(ELIXIR_VERSION) | cut -d. -f2)
ELIXIR_SHA256 := $(shell awk '/^elixir/{print $$3}' VERSIONS)
ELIXIR_IMAGE_NAME ?= gpedic/alpine-elixir

ERLANG_IMAGE_NAME ?= gpedic/alpine-erlang
ERLANG_IMAGE_DIGEST := $(shell awk '/^gpedic\/alpine-erlang/{print $$3}' VERSIONS)
ERLANG_IMAGE_VERSION := $(shell awk '/^gpedic\/alpine-erlang/{print $$2}' VERSIONS)

IMAGE ?= elixir

IMAGE_NAME := $(ELIXIR_IMAGE_NAME)-otp$(ERLANG_IMAGE_VERSION)
VERSION := $(ELIXIR_VERSION)
MAJ_VERSION := $(ELIXIR_MAJ_VERSION)
MIN_VERSION := $(ELIXIR_MIN_VERSION)

ifeq ($(IMAGE), elixir-dev)
	IMAGE_NAME := $(IMAGE_NAME)-dev
else ifeq ($(IMAGE), elixir-builder-npm)
	IMAGE_NAME := $(IMAGE_NAME)-builder-npm
endif

help:
	@echo "$(IMAGE_NAME):$(VERSION)"
	@perl -nle'print $& if m{^[a-zA-Z_-]+:.*?## .*$$}' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

test: ## Test the Docker image
	docker run --rm -it $(IMAGE_NAME):$(VERSION) elixir -e "IO.inspect System.build_info"

repl: ## Run an Elixir shell in the image
	docker run --rm -it $(IMAGE_NAME):$(VERSION) iex

sh: ## Boot to a shell prompt
	docker run --rm -it $(IMAGE_NAME):$(VERSION) /bin/sh

build: ## Build the Docker image
	docker build \
		--build-arg ALPINE_IMAGE=alpine@sha256:$(ALPINE_DIGEST) \
		--build-arg FROM_ERLANG_IMAGE=$(ERLANG_IMAGE_NAME)@sha256:$(ERLANG_IMAGE_DIGEST) \
		--build-arg FROM_ELIXIR_IMAGE=$(ELIXIR_IMAGE_NAME):$(VERSION) \
		--build-arg ELIXIR_VERSION=$(VERSION) \
		--build-arg ELIXIR_SHA256=$(ELIXIR_SHA256) \
		--force-rm \
		-t $(IMAGE_NAME):$(VERSION) \
		-t $(IMAGE_NAME):$(MIN_VERSION) \
		-t $(IMAGE_NAME):$(MAJ_VERSION) \
		-t $(IMAGE_NAME):latest \
		$(IMAGE)/.

clean: ## Clean up generated images
	@docker rmi --force \
	$(IMAGE_NAME):$(VERSION) \
	$(IMAGE_NAME):$(MIN_VERSION) \
	$(IMAGE_NAME):$(MAJ_VERSION) \
	$(IMAGE_NAME):latest

rebuild: clean build ## Rebuild the Docker image

release: build ## Rebuild and release the Docker image to Docker Hub
	docker push $(IMAGE_NAME):$(VERSION)