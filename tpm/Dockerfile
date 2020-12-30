FROM ubuntu:latest
LABEL maintainer="aurel.avramescu@gmail.com"
LABEL description="IBM TPM2 Simulator"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get --quiet --yes install \
        build-essential pkg-config ca-certificates curl wget git libssl-dev \
        software-properties-common gcc-multilib python2.7-dev bc \
        check linux-headers-generic libcurl4-openssl-dev \
        apt-transport-https && \
        apt-get autoremove -y && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists* /tmp/* /var/tmp/*

WORKDIR /build
COPY ibmtpm1637.tar.gz /build
RUN tar -xzf ibmtpm1637.tar.gz && \
    make -C src -j$(nproc)

RUN wget https://github.com/tpm2-software/tpm2-tss/releases/download/1.3.0/tpm2-tss-1.3.0.tar.gz && \
  tar -xzf tpm2-tss-1.3.0.tar.gz && \
  cd tpm2-tss-1.3.0 && \
  ./configure && \
  make -j$(nproc) && make install

RUN cd /build

RUN wget https://github.com/tpm2-software/tpm2-tools/releases/download/3.0.3/tpm2-tools-3.0.3.tar.gz && \
    tar -xzf tpm2-tools-3.0.3.tar.gz && \
    cd tpm2-tools-3.0.3 && \
   ./configure --disable-hardening --disable-unit --without-tcti-device --without-tcti-tabrmd && \
   make -j$(nproc) && make install

RUN apt-get update && apt-get upgrade -y

WORKDIR /tpm2/
RUN cp -r /build/src/tpm_server /bin
RUN cp -r /usr/local/bin/tpm* /usr/bin/

EXPOSE 2321
EXPOSE 2322

CMD [ "tpm_server" ]
