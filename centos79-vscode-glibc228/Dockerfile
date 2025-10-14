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

RUN wget --progress=dot:mega https://mirror.easyname.at/gnu/make/make-4.2.1.tar.gz

RUN tar xf make-4.2.1.tar.gz && \
    cd make-4.2.1 && \
    ./configure --prefix=/usr/local/ && \
    make -j "$(nproc --all)" && \
    make install && \
    cd /usr/local/bin/ && ln -vs make gmake

RUN wget --progress=dot:mega https://mirror.easyname.at/gnu/glibc/glibc-2.28.tar.xz

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

FROM scratch

COPY --from=build /opt/glibc-2.28 /opt/glibc-2.28
