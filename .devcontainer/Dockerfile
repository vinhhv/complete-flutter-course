FROM ubuntu:22.04

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

RUN apt-get update \
  && apt-get install -y \
  curl \
  git \
  file \
  sudo \
  libglu1-mesa \
  vim \
  wget \
  xz-utils \
  unzip \
  zip \
  && rm -rf /var/lib/apt/lists/*

# Flutter
RUN snap install flutter --classic

# Create non-root user
RUN groupadd -r developer && useradd -r -g developer -G sudo -m -s /bin/bash developer

# Switch to the non-root user
USER developer
ENV HOME /home/developer
# Set the working directory
WORKDIR /home/developer