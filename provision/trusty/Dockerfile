FROM ubuntu:14.04
MAINTAINER Xiaoming Wang <xiaoming.wang@lexisnexis.com>
RUN rm -rf /var/lib/apt/lists/*
RUN apt-get clean
RUN apt-get update
RUN apt-get install -y wget
RUN apt-get install -y libaprutil1 libboost-regex1.54.0 libicu52 libldap-2.4-2 libtbb2 --fix-missing
RUN apt-get install -y libnuma1 libxml2 libxslt1.1 g++ openssh-client openssh-server --fix-missing
RUN apt-get install -y expect rsync python sudo --fix-missing

RUN mkdir -p /var/run/dbus
RUN mkdir -p /var/run/sshd

#RUN mkdir -p /var/run/sshd
#CMD ["bash", "-c", "sudo /usr/sbin/sshd -D"]

