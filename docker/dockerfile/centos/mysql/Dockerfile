
FROM centos:7.1
MAINTAINER wangganggang <wggorking@163.com>

# install mysql

RUN groupadd mysql
RUN useradd -g mysql mysql

ADD ./software/mysql-5.7.16-linux-glibc2.5-x86_64.tar.gz /opt/
RUN cd /opt/ && mv mysql-5.7.16-linux-glibc2.5-x86_64 mysql-5.7.16 &&  chown mysql:mysql mysql-5.7.16
RUN cd /opt/mysql-5.7.16 && cp support-files/my-default.cnf ./my.cnf

EXPOSE 3306