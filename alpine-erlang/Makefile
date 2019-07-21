.PHONY: help

ALPINE_VERSION = $(shell awk '/^alpine/{print $$2}' VERSIONS)
ALPINE_DIGEST = $(shell awk '/^alpine/{print $$3}' VERSIONS)

ERLANG_VERSION := $(shell awk '/^erlang/{print $$2}' VERSIONS)
ERLANG_MAJ_VERSION := $(shell echo $(ERLANG_VERSION) | cut -d. -f1)
ERLANG_MIN_VERSION := $(ERLANG_MAJ_VERSION).$(shell echo $(ERLANG_VERSION) | cut -d. -f2)
ERLANG_SHA256 := $(shell awk '/^erlang/{print $$3}' VERSIONS)
ERLANG_IMAGE_NAME ?= gpedic/alpine-erlang
ERLANG_IMAGE_DIGEST := $(shell awk '/^gpedic\/alpine-erlang/{print $$3}' VERSIONS)

IMAGE_NAME := $(ERLANG_IMAGE_NAME)
VERSION := $(ERLANG_VERSION)
MAJ_VERSION := $(ERLANG_MAJ_VERSION)
MIN_VERSION := $(ERLANG_MIN_VERSION)


help:
	@echo "$(IMAGE_NAME):$(VERSION)"
	@perl -nle'print $& if m{^[a-zA-Z_-]+:.*?## .*$$}' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

test: ## Test the Docker image
	docker run --rm -it $(IMAGE_NAME):$(VERSION) erl -noshell -eval 'erlang:display(erlang:system_info(otp_release))' -eval 'init:stop()'

repl: ## Run an Elixir shell in the image
	docker run --rm -it $(IMAGE_NAME):$(VERSION) erl

sh: ## Boot to a shell prompt
	docker run --rm -it $(IMAGE_NAME):$(VERSION) /bin/sh

build: ## Build the Docker image
	docker build \
		--build-arg ALPINE_IMAGE=alpine@sha256:$(ALPINE_DIGEST) \
		--build-arg ERLANG_VERSION=$(ERLANG_VERSION) \
		--build-arg ERLANG_SHA256=$(ERLANG_SHA256) \
		--force-rm \
		-t $(IMAGE_NAME):$(VERSION) \
		-t $(IMAGE_NAME):$(MIN_VERSION) \
		-t $(IMAGE_NAME):$(MAJ_VERSION) \
		-t $(IMAGE_NAME):latest \
		.

clean: ## Clean up generated images
	@docker rmi --force \
	$(IMAGE_NAME):$(VERSION) \
	$(IMAGE_NAME):$(MIN_VERSION) \
	$(IMAGE_NAME):$(MAJ_VERSION) \
	$(IMAGE_NAME):latest

rebuild: clean build ## Rebuild the Docker image

release: build ## Rebuild and release the Docker image to Docker Hub
	docker push $(IMAGE_NAME):$(VERSION)