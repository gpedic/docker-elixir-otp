ARG FROM_ELIXIR_IMAGE=gpedic/alpine-elixir:latest
FROM $FROM_ELIXIR_IMAGE

LABEL maintainer="Goran Pedic <gpedic@rockstarwizard.ninja>"

ENV REFRESHED_AT=2019-09-06 \
    HOME=/opt/build/

RUN \
    apk --no-cache --update upgrade && \
    apk add --no-cache --update git tar \ 
      bash make g++ wget curl inotify-tools \
      nodejs nodejs-npm && \
    npm install npm -g --no-progress && \
    update-ca-certificates --fresh && \
    mix local.hex --force && \
    mix local.rebar --force && \
    rm -rf /var/cache/apk/*

WORKDIR ${HOME}

CMD ["/bin/sh"]