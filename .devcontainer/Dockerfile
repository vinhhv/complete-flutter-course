FROM ubuntu:22.04

ENV DEBIAN_FRONTEND noninteractive

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
  # libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386 \
  # Android Emulator (https://github.com/amrsa1/Android-Emulator-image/blob/main/Dockerfile)
  bzip2 libdrm-dev libxkbcommon-dev libgbm-dev libasound-dev libnss3 libxcursor1 libpulse-dev libxshmfence-dev xauth xvfb x11vnc fluxbox wmctrl libdbus-glib-1-2 \
  && rm -rf /var/lib/apt/lists/*

RUN wget "https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.deb" -O /tmp/jdk-21_linux-x64_bin.deb
RUN apt install /tmp/jdk-21_linux-x64_bin.deb
RUN rm /tmp/jdk-21_linux-x64_bin.deb

ENV JAVA_HOME=/usr/lib/jvm/jdk-21-oracle-x64

# ARG ANDROID_STUDIO_VERSION=2023.1.1.26
# ARG ANDROID_STUDIO_URL=https://redirector.gvt1.com/edgedl/android/studio/ide-zips/${ANDROID_STUDIO_VERSION}/android-studio-${ANDROID_STUDIO_VERSION}-linux.tar.gz
# https://developer.android.com/studio#command-line-tools-only
ARG ANDROID_SDK_VERSION=10406996
ARG ANDROID_SDK_URL=https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_VERSION}.zip

# Android Studio
# RUN wget "$ANDROID_STUDIO_URL" -O /tmp/android-studio.tar.gz && \
#   tar xvzf /tmp/android-studio.tar.gz -C /opt && \
#   rm /tmp/android-studio.tar.gm

# ENV ANDROID_STUDIO_HOME=/opt/android-studio
# ENV PATH="$PATH:$ANDROID_STUDIO_HOME/bin"

#==============================
# Android SDK ARGS
#==============================
ARG ARCH="x86_64"
ARG TARGET="google_apis_playstore"
ARG API_LEVEL="33"
ARG BUILD_TOOLS="34.0.0"
ARG ANDROID_ARCH=${ANDROID_ARCH_DEFAULT}
ARG ANDROID_API_LEVEL="android-${API_LEVEL}"
ARG ANDROID_APIS="${TARGET};${ARCH}"
ARG EMULATOR_PACKAGE="system-images;${ANDROID_API_LEVEL};${ANDROID_APIS}"
ARG PLATFORM_VERSION="platforms;${ANDROID_API_LEVEL}"
ARG BUILD_TOOL="build-tools;${BUILD_TOOLS}"
ARG CMDLINE_TOOLS="cmdline-tools;latest"
ARG ANDROID_SDK_PACKAGES="${CMDLINE_TOOLS} ${EMULATOR_PACKAGE} ${PLATFORM_VERSION} ${BUILD_TOOL} platform-tools"

# Android SDK (more info: https://github.com/thyrlian/AndroidSDK)
ENV ANDROID_HOME /opt/android-sdk
RUN mkdir -p ${ANDROID_HOME}/cmdline-tools && \
  wget -q https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_SDK_VERSION}_latest.zip && \
  unzip *tools*linux*.zip -d ${ANDROID_HOME}/cmdline-tools && \
  mv ${ANDROID_HOME}/cmdline-tools/cmdline-tools ${ANDROID_HOME}/cmdline-tools/tools && \
  rm *tools*linux*.zip
ENV PATH ${PATH}:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/cmdline-tools/tools/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator

#============================================
# Install required package using SDK manager
#============================================
RUN yes | sdkmanager --verbose --no_https ${ANDROID_SDK_PACKAGES}
RUN yes | sdkmanager --licenses

#===================
# Alias
#===================
ENV EMU=./start_emu.sh
ENV EMU_HEADLESS=./start_emu_headless.sh
ENV VNC=./start_vnc.sh
ENV APPIUM=./start_appium.sh

# Create non-root user
RUN groupadd -r $USER && useradd -r -g $USER -G sudo -m -s /bin/bash $USER
RUN echo "$USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Switch to the non-root user
USER $USER
ENV HOME /home/$USER

#============================================
# Create required emulator
#============================================
ARG EMULATOR_NAME="nexus"
ARG EMULATOR_DEVICE="Nexus 6"
ENV EMULATOR_NAME=$EMULATOR_NAME
ENV DEVICE_NAME=$EMULATOR_DEVICE
RUN echo "no" | avdmanager --verbose create avd --force --name "${EMULATOR_NAME}" --device "${EMULATOR_DEVICE}" --package "${EMULATOR_PACKAGE}"

#=========================
# Copying Scripts to user
#=========================
COPY start_emu.sh $HOME/
COPY start_vnc.sh $HOME/

# Set the working directory
WORKDIR /home/$USER

RUN sudo chmod a+x start_vnc.sh && sudo chmod a+x start_emu.sh

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

ENTRYPOINT [ "./start_vnc.sh" ]
