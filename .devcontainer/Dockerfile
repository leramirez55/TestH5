FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-c"]

# Install base packages and clean up apt cache
RUN apt-get update && \
    apt-get install -y \
      build-essential \
      cmake \
      curl \
      git \
      wget \
      zip \
      ninja-build \
      software-properties-common \
      libncurses6 && \
    rm -rf /var/lib/apt/lists/*

# Install GNU Arm Embedded Toolchain based on architecture
RUN ARCH=$(uname -m) && \
    if [ "$ARCH" = "aarch64" ]; then \
        ARM_TOOLCHAIN_URL="https://developer.arm.com/-/media/Files/downloads/gnu/14.2.rel1/binrel/arm-gnu-toolchain-14.2.rel1-aarch64-arm-none-eabi.tar.xz"; \
        ARM_TOOLCHAIN_TAR="arm-gnu-toolchain-14.2.rel1-aarch64-arm-none-eabi.tar.xz"; \
    else \
        ARM_TOOLCHAIN_URL="https://developer.arm.com/-/media/Files/downloads/gnu/14.2.rel1/binrel/arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi.tar.xz"; \
        ARM_TOOLCHAIN_TAR="arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi.tar.xz"; \
    fi && \
    wget "$ARM_TOOLCHAIN_URL" -O "$ARM_TOOLCHAIN_TAR" && \
    tar -xJf "$ARM_TOOLCHAIN_TAR" -C /opt && \
    rm "$ARM_TOOLCHAIN_TAR"

# Override udevadm to avoid issues with remote J-Link devices.
RUN echo -e '#!/bin/bash\necho not running udevadm "$@"' > /usr/bin/udevadm && chmod +x /usr/bin/udevadm

# Install J-Link tools based on architecture
RUN ARCH=$(uname -m) && \
    if [ "$ARCH" = "aarch64" ]; then \
        JLINK_URL="https://www.segger.com/downloads/jlink/JLink_Linux_V812f_arm64.deb"; \
        JLINK_DEB="JLink_Linux_V812f_arm64.deb"; \
    else \
        JLINK_URL="https://www.segger.com/downloads/jlink/JLink_Linux_V812f_x86_64.deb"; \
        JLINK_DEB="JLink_Linux_V812f_x86_64.deb"; \
    fi && \
    wget --post-data "accept_license_agreement=accepted" "$JLINK_URL" -O "$JLINK_DEB" && \
    apt-get update && apt-get install -y ./"$JLINK_DEB" && \
    rm "$JLINK_DEB"


RUN apt-get update && \
    apt-get install -y \
    build-essential \
    cmake \
    curl \
    git \
    wget \
    zip \
    software-properties-common \
    libncurses6 && \
    rm -rf /var/lib/apt/lists/*

# Set up the correct PATH for the toolchain
ENV PATH="/opt/arm-gnu-toolchain-14.2.rel1-aarch64-arm-none-eabi/bin:$PATH"