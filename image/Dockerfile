ARG BASE_IMAGE=debian:stable-slim
ARG GLIBC_VERSION

FROM ${BASE_IMAGE} AS builder
ARG PREFIX_DIR=/usr/glibc-compat
ARG GLIBC_VERSION
RUN apt-get -q update && apt-get -qy install bison build-essential gawk gettext openssl python3 texinfo wget
RUN set -eu && \
    echo >>configparams slibdir=${PREFIX_DIR}/lib && \
    echo >>configparams rtlddir=${PREFIX_DIR}/lib && \
    echo >>configparams sbindir=${PREFIX_DIR}/bin && \
    echo >>configparams rootsbindir=${PREFIX_DIR}/bin && \
    echo >>configparams build-programs=yes && \
    wget -q -O - "https://ftpmirror.gnu.org/libc/glibc-${GLIBC_VERSION}.tar.gz" | tar -xz && \
    mkdir -p /glibc-build && \
    cd /glibc-build && \
    "/glibc-${GLIBC_VERSION}/configure" \
      --prefix="${PREFIX_DIR}" \
      --libdir="${PREFIX_DIR}/lib" \
      --libexecdir="${PREFIX_DIR}/lib" \
      --enable-multi-arch \
      --enable-stack-protector=strong && \
    make && make install && \
    tar --dereference --hard-dereference -czf "/glibc-bin-${GLIBC_VERSION}.tar.gz" "${PREFIX_DIR}"

FROM ${BASE_IMAGE}
ARG GLIBC_VERSION
COPY --from=builder "/glibc-bin-${GLIBC_VERSION}.tar.gz" /
