ARG ALPINE_IMAGE
FROM $ALPINE_IMAGE

LABEL maintainer="Goran Pedic <gpedic@rockstarwizard.ninja>"

ARG ERLANG_SHA256
ARG ERLANG_VERSION

ENV REFRESHED_AT=2019-07-20 \
    # The source epoch date used for Elixir
    # https://reproducible-builds.org/docs/source-date-epoch/
    LANG=en_US.UTF-8 \
    HOME=/opt/app/ \
    # Set this so that CTRL+G works properly
    TERM=xterm \
    ALPINE_DOCKER=${ALPINE_VERSION} \
    ERLANG_SHA256=${ERLANG_SHA256} \
    ERLANG_VERSION=${ERLANG_VERSION}

WORKDIR /tmp/erlang-build

# Install Erlang
RUN \
    # Create default user and home directory, set owner to default
    mkdir -p "${HOME}" && \
    adduser -s /bin/sh -u 1001 -G root -h "${HOME}" -S -D default && \
    chown -R 1001:0 "${HOME}" && \
    # Upgrade Alpine and base packages
    apk --no-cache --update --available upgrade && \
    # Distillery requires bash Install bash and Erlang/OTP deps
    apk add --no-cache --update \
      tar \
      pcre \
      bash \
      ca-certificates \
      openssl-dev \
      ncurses-dev \
      unixodbc-dev \
      zlib-dev && \
    # Install Erlang/OTP build deps
    apk add --no-cache --virtual .erlang-build \
      dpkg-dev dpkg binutils \
      curl autoconf build-base perl-dev && \
    # Download Erlang/OTP
    curl -O -L -J https://github.com/erlang/otp/archive/OTP-$ERLANG_VERSION.tar.gz && \
    echo "${ERLANG_SHA256}  otp-OTP-$ERLANG_VERSION.tar.gz" | sha256sum -c - && \
    tar -xvzf otp-OTP-$ERLANG_VERSION.tar.gz && \
    cd otp-OTP-$ERLANG_VERSION && \
    # Erlang/OTP build env
    export ERL_TOP=`pwd` && \
    export PATH=$ERL_TOP/bin:$PATH && \
    export CPPFlAGS="-D_BSD_SOURCE $CPPFLAGS" && \
    # Enable ASLR
    export CFLAGS=-fpie && \
    export LDFLAGS="-pie -z now" && \
    # Configure
    ./otp_build autoconf && \
    ./configure --prefix=/usr \
      --build="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
      --sysconfdir=/etc \
      --mandir=/usr/share/man \
      --infodir=/usr/share/info \
      --without-javac \
      --without-wx \
      --without-debugger \
      --without-observer \
      --without-jinterface \
      --without-cosEvent\
      --without-cosEventDomain \
      --without-cosFileTransfer \
      --without-cosNotification \
      --without-cosProperty \
      --without-cosTime \
      --without-cosTransactions \
      --without-et \
      --without-gs \
      --without-ic \
      --without-megaco \
      --without-orber \
      --without-percept \
      --without-typer \
      --enable-threads \
      --enable-shared-zlib \
      --enable-ssl=dynamic-ssl-lib \
      --enable-hipe && \
      # Build
      make -j4 && make install && \
      # Cleanup
      cd $HOME && \
      rm -rf /tmp/erlang-build && \
      # Update ca certificates
      update-ca-certificates --fresh && \
      /usr/bin/erl -eval "beam_lib:strip_release('/usr/lib/erlang/lib')" -s init stop > /dev/null && \
      (/usr/bin/strip /usr/lib/erlang/erts-*/bin/* || true) && \
      rm -rf /usr/lib/erlang/usr/ && \
      rm -rf /usr/lib/erlang/misc/ && \
      for DIR in /usr/lib/erlang/erts* /usr/lib/erlang/lib/*; do \
          rm -rf ${DIR}/src/*.erl; \
          rm -rf ${DIR}/doc; \
          rm -rf ${DIR}/man; \
          rm -rf ${DIR}/examples; \
          rm -rf ${DIR}/emacs; \
          rm -rf ${DIR}/c_src; \
      done && \
      rm -rf /usr/lib/erlang/erts-*/lib/ && \
      rm /usr/lib/erlang/erts-*/bin/dialyzer && \
      rm /usr/lib/erlang/erts-*/bin/erlc && \
      rm /usr/lib/erlang/erts-*/bin/typer && \
      rm /usr/lib/erlang/erts-*/bin/ct_run && \
      apk del --force .erlang-build

WORKDIR ${HOME}

CMD ["/bin/sh"]