FROM pocworks/oraclejdk:8u212-server-jre-slim-stretch

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    openssl \
    ca-certificates

ENV TOMCAT_MAJOR 8
ENV TOMCAT_VERSION 8.5.39
ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH

WORKDIR $CATALINA_HOME

RUN mkdir -p $CATALINA_HOME && \
    curl -jksSL http://archive.apache.org/dist/tomcat/tomcat-${TOMCAT_MAJOR}/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz \
        | tar -xzf - -C $CATALINA_HOME --strip-components=1

EXPOSE 8080
CMD ["catalina.sh", "run"]

RUN apt-get autoclean && \
    rm -rf /var/cache/apt /var/lib/apt/lists/* /tmp/* /var/tmp/*