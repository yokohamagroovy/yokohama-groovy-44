FROM openjdk

MAINTAINER grimrose

ENV GROOVY_VERSION 2.4.7

RUN apt-get -y update && \
    apt-get -y upgrade && \
    apt-get -y install wget unzip && \
    apt-get clean

RUN cd /tmp && \
    wget https://bintray.com/artifact/download/groovy/maven/apache-groovy-binary-${GROOVY_VERSION}.zip && \
    unzip apache-groovy-binary-${GROOVY_VERSION}.zip && \
    mv groovy-${GROOVY_VERSION} /groovy && \
    rm apache-groovy-binary-${GROOVY_VERSION}.zip

ENV GROOVY_HOME /groovy
ENV PATH $GROOVY_HOME/bin/:$PATH
