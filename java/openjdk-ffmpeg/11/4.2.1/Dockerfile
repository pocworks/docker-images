# Based on https://github.com/jrottenberg/ffmpeg
# Copy all ffmpeg and related libs to openjdk:11.0.4-jre-stretch

FROM buildpack-deps:stretch-curl AS base

WORKDIR /tmp/workdir

RUN apt-get -y update && \
    apt-get install -y --no-install-recommends ca-certificates expat libgomp1 && \
    apt-get autoremove -y && \
    apt-get clean -y

FROM base as build

ARG PKG_CONFIG_PATH=/opt/ffmpeg/lib/pkgconfig
ARG LD_LIBRARY_PATH=/opt/ffmpeg/lib
ARG PREFIX=/opt/ffmpeg
ARG MAKEFLAGS="-j2"

ENV FFMPEG_VERSION=4.2.1 \
    AOM_VERSION=v1.0.0 \
    VPX_VERSION=1.8.0 \
    X264_VERSION=20170226-2245-stable \
    X265_VERSION=3.1.1 \
    XVID_VERSION=1.3.4 \
    LAME_VERSION=3.100 \
    SRC=/usr/local

RUN buildDeps="autoconf \
        automake \
        cmake \
        curl \
        bzip2 \
        libexpat1-dev \
        g++ \
        gcc \
        git \
        gperf \
        libtool \
        make \
        nasm \
        perl \
        pkg-config \
        python \
        libssl-dev \
        yasm \
        zlib1g-dev" && \
    apt-get -y update && \
    apt-get install -y --no-install-recommends ${buildDeps}

# x264 http://www.videolan.org/developers/x264.html
RUN DIR=/tmp/x264 && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLk https://download.videolan.org/pub/videolan/x264/snapshots/x264-snapshot-${X264_VERSION}.tar.bz2 | \
    tar -jx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --enable-shared --enable-pic --disable-cli && \
    make && \
    make install && \
    rm -rf ${DIR}

# x265 http://x265.org/
RUN DIR=/tmp/x265 && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLk https://download.videolan.org/pub/videolan/x265/x265_${X265_VERSION}.tar.gz  | \
    tar -zx && \
    cd x265_${X265_VERSION}/build/linux && \
    sed -i "/-DEXTRA_LIB/ s/$/ -DCMAKE_INSTALL_PREFIX=\${PREFIX}/" multilib.sh && \
    sed -i "/^cmake/ s/$/ -DENABLE_CLI=OFF/" multilib.sh && \
    ./multilib.sh && \
    make -C 8bit install && \
    rm -rf ${DIR}

# libvpx https://www.webmproject.org/code/
RUN DIR=/tmp/vpx && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLk https://codeload.github.com/webmproject/libvpx/tar.gz/v${VPX_VERSION} | \
    tar -zx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --enable-vp8 --enable-vp9 --enable-vp9-highbitdepth --enable-pic --enable-shared \
    --disable-debug --disable-examples --disable-docs --disable-install-bins  && \
    make && \
    make install && \
    rm -rf ${DIR}

# xvid https://www.xvid.com/
RUN DIR=/tmp/xvid && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLO http://downloads.xvid.org/downloads/xvidcore-${XVID_VERSION}.tar.gz && \
    tar -zx -f xvidcore-${XVID_VERSION}.tar.gz && \
    cd xvidcore/build/generic && \
    ./configure --prefix="${PREFIX}" --bindir="${PREFIX}/bin" --datadir="${DIR}" --enable-shared --enable-shared && \
    make && \
    make install && \
    rm -rf ${DIR}

### libmp3lame http://lame.sourceforge.net/
RUN DIR=/tmp/lame && \
    mkdir -p ${DIR} && \
    cd ${DIR} && \
    curl -sLk https://versaweb.dl.sourceforge.net/project/lame/lame/$(echo ${LAME_VERSION} | sed -e 's/[^0-9]*\([0-9]*\)[.]\([0-9]*\)[.]\([0-9]*\)\([0-9A-Za-z-]*\)/\1.\2/')/lame-${LAME_VERSION}.tar.gz | \
    tar -zx --strip-components=1 && \
    ./configure --prefix="${PREFIX}" --bindir="${PREFIX}/bin" --enable-shared --enable-nasm --enable-pic --disable-frontend && \
    make && \
    make install && \
    rm -rf ${DIR}

# aom https://aomedia.googlesource.com/aom
RUN DIR=/tmp/aom && \
    GIT_SSL_NO_VERIFY=true git clone --branch ${AOM_VERSION} --depth 1 https://aomedia.googlesource.com/aom ${DIR} ; \
    cd ${DIR} ; \
    rm -rf CMakeCache.txt CMakeFiles ; \
    mkdir -p ./aom_build ; \
    cd ./aom_build ; \
    cmake -DCMAKE_INSTALL_PREFIX="${PREFIX}" -DBUILD_SHARED_LIBS=1 ..; \
    make ; \
    make install ; \
    rm -rf ${DIR}

# ffmpeg https://ffmpeg.org/
RUN DIR=/tmp/ffmpeg && mkdir -p ${DIR} && cd ${DIR} && \
    curl -sLkO https://ffmpeg.org/releases/ffmpeg-${FFMPEG_VERSION}.tar.bz2 && \
    tar -jx --strip-components=1 -f ffmpeg-${FFMPEG_VERSION}.tar.bz2

# configure, make, make install
RUN DIR=/tmp/ffmpeg && mkdir -p ${DIR} && cd ${DIR} && \
    ./configure \
    --disable-debug \
    --disable-doc \
    --disable-ffplay \
    --enable-shared \
    --enable-avresample \
    --enable-gpl \
    --enable-libvpx \
    --enable-libx265 \
    --enable-libxvid \
    --enable-libx264 \
    --enable-libmp3lame \
    --enable-nonfree \
    --enable-openssl \
    --enable-libaom --extra-libs=-lpthread \
    --enable-postproc \
    --enable-small \
    --enable-version3 \
    --extra-cflags="-I${PREFIX}/include" \
    --extra-ldflags="-L${PREFIX}/lib" \
    --extra-libs=-ldl \
    --prefix="${PREFIX}" && \
    make && \
    make install && \
    make distclean && \
    hash -r && \
    cd tools && \
    make qt-faststart && \
    cp qt-faststart ${PREFIX}/bin

# cleanup
RUN ldd ${PREFIX}/bin/ffmpeg | grep opt/ffmpeg | cut -d ' ' -f 3 | xargs -i cp {} /usr/local/lib/ && \
    cp ${PREFIX}/bin/* /usr/local/bin/ && \
    cp -r ${PREFIX}/share/ffmpeg /usr/local/share/ && \
    LD_LIBRARY_PATH=/usr/local/lib ffmpeg -buildconf

FROM openjdk:11.0.4-jre-stretch

ENV LD_LIBRARY_PATH=/usr/local/lib

COPY --from=build /usr/local /usr/local/