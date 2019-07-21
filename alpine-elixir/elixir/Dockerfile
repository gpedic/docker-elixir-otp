ARG FROM_ERLANG_IMAGE=gpedic/alpine-erlang:latest
FROM $FROM_ERLANG_IMAGE

LABEL maintainer="Goran Pedic <gpedic@rockstarwizard.ninja>"

ARG ELIXIR_VERSION
ARG ELIXIR_SHA256

WORKDIR /tmp/elixir-build

RUN \
    apk --no-cache --update upgrade && \
    apk add --no-cache --update --virtual .elixir-build \
      make curl && \
    curl -O -L -J https://github.com/elixir-lang/elixir/archive/v$ELIXIR_VERSION.tar.gz && \
    echo "${ELIXIR_SHA256}  elixir-$ELIXIR_VERSION.tar.gz" | sha256sum -c - && \
    tar -xvzf elixir-$ELIXIR_VERSION.tar.gz && \
    cd elixir-$ELIXIR_VERSION && \
    make && make install && \
    mix local.hex --force && \
    mix local.rebar --force && \
    cd $HOME && \
    rm -rf /tmp/elixir-build && \
    apk del --no-cache .elixir-build

WORKDIR ${HOME}

CMD ["/bin/sh"]
