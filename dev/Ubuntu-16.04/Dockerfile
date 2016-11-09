FROM ubuntu:16.04
MAINTAINER Ulrich Germann <ulrich.germann@gmail.com>

# let user specify MMT git hash at build time
ARG MMT_CHECKOUT=master
ARG MMT_USER=mmt
ENV MMT_CHECKOUT=$MMT_CHECKOUT
ENV MMT_USER=$MMT_USER

RUN apt-get update
RUN apt-get install -y software-properties-common
RUN apt-get install -y python-software-properties
RUN apt-get install -y build-essential cmake git maven libbz2-dev zlib1g-dev
RUN apt-get install -y libboost-all-dev
RUN apt-get install -y libgoogle-perftools-dev
RUN apt-get install -y libsparsehash-dev openjdk-8-jdk 
RUN apt-get install -y python-requests python-psutil 
RUN apt-get install -y libjemalloc-dev 

# Note: that's a hack from https://github.com/ModernMT/MMT/blob/master/INSTALL.md!
# MMT should not require so many files to be open, and it probably doesn't (shouldn't!)
# bypass limits set by the docker host. [UG]
# RUN echo "soft nofile 1048576" >> /etc/security/limits.conf
# RUN echo "hard nofile 1048576" >> /etc/security/limits.conf

RUN useradd -ms /bin/bash $MMT_USER
RUN mkdir /MMT
RUN chown $MMT_USER /MMT
USER $MMT_USER
RUN git clone http://github.com/modernmt/MMT -b $MMT_CHECKOUT
WORKDIR /MMT/vendor
RUN git submodule init
RUN git submodule update
RUN make res 
RUN make -j
WORKDIR /MMT/src
RUN mvn clean install

WORKDIR /MMT/
RUN ./setup.py
RUN ./mmt create en it examples/data/train --no-split
