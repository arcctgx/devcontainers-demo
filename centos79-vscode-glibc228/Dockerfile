# hadolint global ignore=DL3003
# hadolint global ignore=DL3032
# hadolint global ignore=DL3033

FROM centos:7.9.2009 AS build

COPY *.repo /etc/yum.repos.d/

RUN yum -y upgrade && \
    yum install -y \
      epel-release \
      centos-release-scl

RUN yum -y install \
      wget \
      gcc \
      make \
      bison \
      texinfo \
      python3 \
      gettext \
      devtoolset-8-gcc \
      devtoolset-8-gcc-c++ \
      devtoolset-8-binutils

WORKDIR /tmp

RUN wget --progress=dot:mega \
    https://mirror.easyname.at/gnu/make/make-4.2.1.tar.gz \
    https://mirror.easyname.at/gnu/glibc/glibc-2.28.tar.xz \
    https://github.com/NixOS/patchelf/releases/download/0.18.0/patchelf-0.18.0.tar.gz

COPY CHECKSUMS /tmp

RUN sha256sum -c CHECKSUMS

RUN tar xf make-4.2.1.tar.gz && \
    cd make-4.2.1 && \
    ./configure --prefix=/usr/local/ && \
    make -j "$(nproc --all)" && \
    make install && \
    cd /usr/local/bin/ && ln -vs make gmake

RUN tar xf glibc-2.28.tar.xz && \
    cd glibc-2.28 && mkdir build && cd build && \
    . /opt/rh/devtoolset-8/enable && \
    ../configure --prefix=/opt/glibc-2.28 && \
    make -j"$(nproc --all)" && \
    make install

# Stripping saves about 90 MB, but is reported to cause issues with libpthread.
# Patrick Volkerding says --strip-debug is fine, and it's --strip-unneeded that
# causes breakage. Commented out because I'm not sure the risk is worth it.
#RUN (strip --strip-debug /opt/glibc-2.28/lib/lib*.so || true) && \
#    (strip --strip-debug /opt/glibc-2.28/lib/lib*.a || true) && \
#    (strip --strip-debug /opt/glibc-2.28/lib/gconv/*.so || true)

RUN tar xf patchelf-0.18.0.tar.gz && \
    cd patchelf-0.18.0 && \
    . /opt/rh/devtoolset-8/enable && \
    ./configure --prefix=/opt/patchelf-0.1.8 && \
    make -j"$(nproc --all)" && \
    make install-strip

FROM scratch

COPY --from=build /opt/glibc-2.28 /opt/glibc-2.28

COPY --from=build /opt/patchelf-0.1.8 /opt/patchelf-0.1.8
