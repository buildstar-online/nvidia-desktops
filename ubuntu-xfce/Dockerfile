ARG BASE_IMAGE="ubuntu:22.04"
FROM $BASE_IMAGE

ENV TZ=UTC

ARG NVIDIA_VISIBLE_DEVICES=all
ARG DEBIAN_FRONTEND=noninteractive

ENV NVIDIA_DRIVER_CAPABILITIES all
ENV APPIMAGE_EXTRACT_AND_RUN 1


COPY mozilla-firefox /etc/apt/preferences.d/mozilla-firefox

# Install deps for Nvidia and Desktop
RUN apt-get update && \
  apt-get install -y software-properties-common \
  sudo \
  tmux \
  curl \
  kmod \
  pkg-config \
  make \
  libvulkan1 \
  dbus \
  dbus-x11 \
  xvfb \
  xorg \
  xterm \
  xinit \
  xfce4 \
  xfce4-goodies \
  tzdata \
  wget && \
  add-apt-repository -y ppa:mozillateam/ppa && \
  apt-get update && apt-get install -y firefox-esr
ARG USER="player1"

# create a user for non-root operation
RUN useradd -ms /bin/bash $USER && \
        usermod -aG sudo $USER && \
        echo 'player1 ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
        mkdir -p /home/$USER/.local/bin && \
        mkdir -p /home/$USER/.local/lib && \
        chown $USER:$USER /home/$USER/.local/bin && \
        chown $USER:$USER /home/$USER/.local/lib

# Swap to user account
USER $USER
WORKDIR /home/$USER/

COPY entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
