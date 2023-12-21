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

ARG ANDROID_STUDIO_VERSION=2023.1.1.26
ARG ANDROID_STUDIO_URL=https://redirector.gvt1.com/edgedl/android/studio/ide-zips/${ANDROID_STUDIO_VERSION}/android-studio-${ANDROID_STUDIO_VERSION}-linux.tar.gz
# https://developer.android.com/studio#command-line-tools-only
ARG ANDROID_SDK_VERSION=10406996
ARG ANDROID_SDK_URL=https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_VERSION}.zip

# Android Studio
RUN wget "$ANDROID_STUDIO_URL" -O /tmp/android-studio.tar.gz && \
  tar xvzf /tmp/android-studio.tar.gz -C /opt && \
  rm /tmp/android-studio.tar.gz

ENV ANDROID_STUDIO_HOME=/opt/android-studio
ENV PATH="$PATH:$ANDROID_STUDIO_HOME/bin"

# Android SDK (more info: https://github.com/thyrlian/AndroidSDK)
ENV ANDROID_SDK_HOME /opt/android-sdk
RUN mkdir -p ${ANDROID_SDK_HOME}/cmdline-tools && \
  wget -q https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_VERSION}_latest.zip && \
  unzip *tools*linux*.zip -d ${ANDROID_SDK_HOME}/cmdline-tools && \
  mv ${ANDROID_SDK_HOME}/cmdline-tools/cmdline-tools ${ANDROID_SDK_HOME}/cmdline-tools/tools && \
  rm *tools*linux*.zip
ENV PATH ${PATH}:${ANDROID_STUDIO_HOME}/cmdline-tools/latest/bin:${ANDROID_STUDIO_HOME}/cmdline-tools/tools/bin:${ANDROID_STUDIO_HOME}/platform-tools:${ANDROID_STUDIO_HOME}/emulator

# Create non-root user
RUN groupadd -r $USER && useradd -r -g $USER -G sudo -m -s /bin/bash $USER

# Switch to the non-root user
USER $USER
ENV HOME /home/$USER
# Set the working directory
WORKDIR /home/$USER

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
