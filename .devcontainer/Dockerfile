FROM ubuntu:22.04

ARG USER=developer

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

RUN dpkg --add-architecture i386
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
  # Android Studio
  libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386 \
  && rm -rf /var/lib/apt/lists/*

# Following https://github.com/Deadolus/android-studio-docker/blob/master/Dockerfile
ARG ANDROID_STUDIO_URL=https://redirector.gvt1.com/edgedl/android/studio/ide-zips/2023.1.1.26/android-studio-2023.1.1.26-linux.tar.gz
ARG ANDROID_STUDIO_VERSION=2023.1.1.26

# Create non-root user
RUN groupadd -r $USER && useradd -r -g $USER -G sudo -m -s /bin/bash $USER

# Switch to the non-root user
USER $USER
ENV HOME /home/$USER
# Set the working directory
WORKDIR /home/$USER

# Android Studio
RUN wget "$ANDROID_STUDIO_URL" -O /tmp/android-studio.tar.gz && \
  tar xvzf /tmp/android-studio.tar.gz && \
  rm /tmp/android-studio.tar.gz

ENV ANDROID_HOME=$HOME/android-studio
ENV PATH="$PATH:$ANDROID_HOME/bin"

# Download Flutter SDK (https://github.com/edwardinubuntu/flutter-web-dockerfile/blob/master/Dockerfile)
ARG FLUTTER_SDK=$HOME/flutter

RUN git clone https://github.com/flutter/flutter.git $FLUTTER_SDK

# Setup the flutter path as an enviromental variable
ENV PATH="$FLUTTER_SDK/bin:$FLUTTER_SDK/bin/cache/dart-sdk/bin:${PATH}"

# Switch to a stable
RUN flutter channel stable
RUN flutter upgrade

# Start to run Flutter commands
# doctor to see if installation was okay
RUN flutter doctor -v
