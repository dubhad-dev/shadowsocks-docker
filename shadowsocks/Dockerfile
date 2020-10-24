FROM debian:10.6 AS build

ARG SHADOWSOCKS_VERSION=3.3.5

WORKDIR /build

RUN apt-get update && apt-get install -y --no-install-recommends \
      wget \
      ca-certificates \
      autoconf \
      automake \
      libtool \
      gettext \
      build-essential \
      libpcre3-dev \
      libev-dev \
      libc-ares-dev \
      libmbedtls-dev \
      libsodium-dev \
    && rm -rf /var/lib/apt/lists/*

RUN wget -O shadowsocks-libev.tar.gz https://github.com/shadowsocks/shadowsocks-libev/releases/download/v${SHADOWSOCKS_VERSION}/shadowsocks-libev-${SHADOWSOCKS_VERSION}.tar.gz --progress=dot:giga \
    && tar -xzf shadowsocks-libev.tar.gz --strip 1 \
    && ./configure --disable-documentation \
    && make install


FROM debian:10.6 AS run

RUN apt-get update && apt-get install -y --no-install-recommends \
      libev4 \
      libsodium23 \
      libmbedtls12 \
      libpcre3 \
      libc-ares2 \
      wget \
      ca-certificates \
      rng-tools \
      tzdata \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build /usr/local/bin/ss-* /usr/local/bin/

ENV TIMEOUT=300
ENV METHOD=xchacha20-ietf-poly1305

ENV PASSWORD=
ENV DNS_SERVER=1.1.1.1
ENV PORT=8008

USER nobody

CMD ss-server \
      -s 0.0.0.0 \
      -p ${PORT} \
      -k ${PASSWORD} \
      -m ${METHOD} \
      -t ${TIMEOUT} \
      -d ${DNS_SERVER} \
      -u \
      --no-delay \
      --fast-open \
      --reuse-port
