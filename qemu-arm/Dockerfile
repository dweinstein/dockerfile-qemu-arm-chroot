FROM dockerfile/ubuntu:latest
MAINTAINER David Weinstein <dweinst@insitusec.com>

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -qq -y update
RUN apt-get -qq -y install qemu-user qemu-user-static
RUN apt-get clean

WORKDIR /
VOLUME /data
