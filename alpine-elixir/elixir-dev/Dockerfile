ARG FROM_ELIXIR_IMAGE=gpedic/alpine-elixir:latest
FROM $FROM_ELIXIR_IMAGE

LABEL maintainer="Goran Pedic <gpedic@rockstarwizard.ninja>"

ENV REFRESHED_AT=2019-09-06

RUN \
    apk --no-cache --update upgrade && \
    apk add --no-cache --update bash tar make git && \
    mix local.hex --force && \
    mix local.rebar --force

WORKDIR ${HOME}

CMD ["/bin/sh"]