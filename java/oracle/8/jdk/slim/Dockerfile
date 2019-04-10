FROM debian:stretch-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    bsdtar

ENV LANG C.UTF-8

ENV JAVA_VERSION_MAJOR 8
ENV JAVA_VERSION_MINOR 201
ENV JAVA_VERSION_BUILD 09
ENV JAVA_PACKAGE       jdk

ENV JAVA_HOME   /usr/lib/jvm/${JAVA_PACKAGE}1.${JAVA_VERSION_MAJOR}.0_${JAVA_VERSION_MINOR}
ENV PATH        ${JAVA_HOME}/bin:${PATH}

RUN SIG=42970487e3af4f5aa5bca3f542482c60 && \
    mkdir -p /usr/lib/jvm && \
    curl -jksSLH "Cookie: oraclelicense=accept-securebackup-cookie" \
    http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/${SIG}/${JAVA_PACKAGE}-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz \
        | tar -xzf - -C /usr/lib/jvm/ && \
    rm -rf  ${JAVA_HOME}/*src.zip \
            ${JAVA_HOME}/lib/missioncontrol \
            ${JAVA_HOME}/lib/visualvm \
            ${JAVA_HOME}/lib/*javafx* \
            ${JAVA_HOME}/jre/lib/plugin.jar \
            ${JAVA_HOME}/jre/lib/ext/jfxrt.jar \
            ${JAVA_HOME}/jre/bin/javaws \
            ${JAVA_HOME}/jre/lib/javaws.jar \
            ${JAVA_HOME}/jre/lib/desktop \
            ${JAVA_HOME}/jre/plugin \
            ${JAVA_HOME}/jre/lib/deploy* \
            ${JAVA_HOME}/jre/lib/*javafx* \
            ${JAVA_HOME}/jre/lib/*jfx* \
            ${JAVA_HOME}/jre/lib/amd64/libdecora_sse.so \
            ${JAVA_HOME}/jre/lib/amd64/libprism_*.so \
            ${JAVA_HOME}/jre/lib/amd64/libfxplugins.so \
            ${JAVA_HOME}/jre/lib/amd64/libglass.so \
            ${JAVA_HOME}/jre/lib/amd64/libgstreamer-lite.so \
            ${JAVA_HOME}/jre/lib/amd64/libjavafx*.so \
            ${JAVA_HOME}/jre/lib/amd64/libjfx*.so
    
RUN apt-get -y autoremove curl bsdtar && apt-get autoclean && \
    rm -rf /var/cache/apt /var/lib/apt/lists/* /tmp/* /var/tmp/* 