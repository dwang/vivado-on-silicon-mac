# Container for running Vivado on M1/M2 macs
# though it should work equally on Intel macs
FROM --platform=linux/amd64 ubuntu:22.04@sha256:ed1544e454989078f5dec1bfdabd8c5cc9c48e0705d07b678ab6ae3fb61952d2

# Fix the error of build failing due to not having the frontend installed
# This enviroment auto fallbacks to noninteractive mode, with the declaration following this guidance:
# https://bobcares.com/blog/debian_frontendnoninteractive-docker/
# https://github.com/moby/moby/issues/4032#issuecomment-34597177
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    g++ \
    git \
    graphviz \
    libc6-dev-i386 \
    libncursesw5 \
    libtinfo5 \
    make \
    net-tools \
    unzip \
    xvfb \
    libxtst6 \
    libxi6 \
    libgtk-3-0 \
    locales \
    sudo \
    lxde \
    xterm \
    xorg \
    dbus-x11 \
    tigervnc-standalone-server \
    tigervnc-xorg-extension \
    tigervnc-tools \
    && rm -rf /var/lib/apt/lists/*

# create user "user" with password "password"
RUN useradd --create-home --shell /bin/bash --user-group --groups adm,sudo user && \
    echo "user:password" | chpasswd && \
    chown -R user:user /home/user

# Prevent lxpolkit from starting
RUN mv /usr/bin/lxpolkit /usr/bin/lxpolkit.bak

# setup TigerVNC
RUN sed -i 's/-iconic/-nowin/g' /etc/X11/Xtigervnc-session && \
    mkdir -p /home/user/.vnc && \
    echo "password" | vncpasswd -f > /vncpasswd && \
    chown user /vncpasswd && \
    chmod 600 /vncpasswd

# Set the locale, because Vivado crashes otherwise
RUN localedef -i en_US -f UTF-8 en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_ALL=en_US.UTF-8

# Without this, Vivado will crash when synthesizing
ENV LD_PRELOAD="/lib/x86_64-linux-gnu/libudev.so.1 /lib/x86_64-linux-gnu/libselinux.so.1 /lib/x86_64-linux-gnu/libz.so.1"
