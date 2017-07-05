
FROM centos:7.1
MAINTAINER wangganggang <wggorking@163.com>

# install jdk

ADD ./software/jdk-8u111-linux-x64.tar.gz /opt

#RUN cd /opt/ && mv jdk-8u111-linux-x64 jdk1.8.0

ENV JAVA_HOME opt/jdk1.8.0_111
ENV JRE_HOME opt/jdk1.8.0_111/jre

ENV CLASSPATH .:$JAVA_HOME/lib:$JRE_HOME/lib
ENV PATH $PATH:$JAVA_HOME/bin

