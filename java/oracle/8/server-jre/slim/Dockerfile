FROM debian:stretch-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    bsdtar

ENV LANG C.UTF-8

ENV JAVA_VERSION_MAJOR 8
ENV JAVA_VERSION_MINOR 212
ENV JAVA_PACKAGE       jdk

ENV JAVA_HOME   /usr/lib/jvm/${JAVA_PACKAGE}1.${JAVA_VERSION_MAJOR}.0_${JAVA_VERSION_MINOR}
ENV PATH        ${JAVA_HOME}/bin:${PATH}

RUN mkdir -p /usr/lib/jvm && \
    curl -skLJ https://raw.github.com/pocworks/archives/master/oraclejdk/server-jre-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz \
        | tar -xzf - -C /usr/lib/jvm/
    
RUN apt-get -y autoremove bsdtar && apt-get autoclean && \
    rm -rf /var/cache/apt /var/lib/apt/lists/* /tmp/* /var/tmp/* 