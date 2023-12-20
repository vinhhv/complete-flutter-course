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

# Download Flutter SDK (https://github.com/edwardinubuntu/flutter-web-dockerfile/blob/master/Dockerfile)
ARG FLUTTER_SDK=/usr/local/flutter
ARG FLUTTER_VERSION=3.16.5

RUN git clone https://github.com/flutter/flutter.git $FLUTTER_SDK
# Change dir to current flutter folder and make a checkout to the specific version
RUN cd $FLUTTER_SDK && git fetch && git checkout $FLUTTER_VERSION

# Setup the flutter path as an enviromental variable
ENV PATH="$FLUTTER_SDK/bin:$FLUTTER_SDK/bin/cache/dart-sdk/bin:${PATH}"

# Start to run Flutter commands
# doctor to see if installation was okay
RUN flutter doctor -v

# Create non-root user
RUN groupadd -r developer && useradd -r -g developer -G sudo -m -s /bin/bash developer

# Switch to the non-root user
USER developer
ENV HOME /home/developer
# Set the working directory
WORKDIR /home/developer