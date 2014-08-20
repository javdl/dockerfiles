FROM ubuntu:trusty
MAINTAINER Joost van der Laan <joostvanderlaan@gmail.com>
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get -y update
RUN apt-get -y upgrade

# Otherwise you cannot add repositories # On older Ubuntu distributions, “software-properties-common” is called “python-software-properties”.
RUN apt-get install -y software-properties-common

CMD ["/bin/bash"]