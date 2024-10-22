FROM ubuntu
LABEL maintainer="kartikeyait@gmail.com"
ARG USERNAME=tomuser
ARG UID=2000
ARG GID=2000
RUN groupadd -g $GID -o $USERNAME
RUN useradd -u $UID -g $GID -o -s /bin/bash $USERNAME
ARG WAR_PACK=https://github.com/ksrepo9/test/raw/refs/heads/master/ksdemo.war
ARG TOM_URL=https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.96/bin/apache-tomcat-9.0.96.tar.gz
ARG TOM_TAR=apache-tomcat-9.0.96.tar.gz
ARG TOM_SRC=apache-tomcat-9.0.96
ARG TOM_HOME=/tomcat
ENV TOM_START=/tomcat/bin/catalina.sh


RUN set -eux; \
  apt update; \
  apt-get install openjdk-11-jdk wget -y; \
  cd /opt/ && wget $TOM_URL && tar -xvf $TOM_TAR && cd $TOM_SRC/webapps && rm -rf *; \
  mv /opt/$TOM_SRC /tomcat;

ADD $WAR_PACK $TOM_HOME/webapps

RUN chown $USERNAME:$USERNAME $TOM_HOME -R
EXPOSE 8080
USER $USERNAME
ENTRYPOINT ["sh","-c","${TOM_START} run"]

