FROM ubuntu:16.04
MAINTAINER Gigantum <hello@gigantum.io>

# Install system level dependencies
RUN apt-get -y update; apt-get -y upgrade
RUN apt-get -y install build-essential python3-dev python3-pip git curl vim

# Setup Global git config
RUN git config --global user.email "noreply@gigantum.io"; git config --global user.name "Gigantum AutoCommit"
