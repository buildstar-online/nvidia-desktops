ARG BASE_IMAGE="jessebot/onboardme:debian12"
FROM $BASE_IMAGE

ENV TZ=UTC

ARG NVIDIA_VISIBLE_DEVICES=all
ARG DEBIAN_FRONTEND=noninteractive

ENV NVIDIA_DRIVER_CAPABILITIES all
ENV APPIMAGE_EXTRACT_AND_RUN 1


COPY mozilla-firefox /etc/apt/preferences.d/mozilla-firefox

USER root
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
  konsole \
  xinit \
  xfce4 \
  xfce4-goodies \
  tzdata \
  wget \
  python3-launchpadlib && \
  add-apt-repository -y ppa:mozillateam/ppa && \
  apt-get update && sudo apt-get install -y firefox-esr && \
  rm -rf /var/lib/apt/lists/*

# Install NoVNC to expose x11vnc via websocket
ARG NOVNC_VERSION=1.4.0
RUN apt-get update && apt-get install --no-install-recommends -y \
        autoconf \
        automake \
        autotools-dev \
        chrpath \
        debhelper \
        git \
        jq \
        python3 \
        python3-numpy \
        libc6-dev \
        libcairo2-dev \
        libjpeg62-turbo-dev \
        libssl-dev \
        libv4l-dev \
        libvncserver-dev \
        libtool-bin \
        libxdamage-dev \
        libxinerama-dev \
        libxrandr-dev \
        libxss-dev \
        libxtst-dev \
        libavahi-client-dev && \
        apt-get remove flatpak && \
        rm -rf /var/lib/apt/lists/*

RUN git clone "https://github.com/LibVNC/x11vnc.git" /tmp/x11vnc && \
    cd /tmp/x11vnc && \
    autoreconf -fi && \
    ./configure && \
    make install && \
    cd / && \
    rm -rf /tmp/* && \
    curl -fsSL "https://github.com/novnc/noVNC/archive/v${NOVNC_VERSION}.tar.gz" |\
    tar -xzf - -C /opt && \
    mv -f "/opt/noVNC-${NOVNC_VERSION}" /opt/noVNC && \
    ln -snf /opt/noVNC/vnc.html /opt/noVNC/index.html && \
    # Use the latest Websockify source to expose noVNC
    git clone "https://github.com/novnc/websockify.git" /opt/noVNC/utils/websockify

RUN usermod -aG sudo friend && \
    usermod --shell /bin/bash friend && \
    echo 'friend ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    mkdir -p /home/friend/.local/bin && \
    mkdir -p /home/friend/.local/lib && \
    chown -R friend:friend /home/friend/.local

# Swap to user account
USER friend
ENV PATH=/home/friend/.linuxbrew/bin:$PATH
WORKDIR /home/friend/

RUN brew tap wez/wezterm-linuxbrew && \
    brew install wezterm && \
    sed -i 's/mononoki/Mononoki Nerd Font/g' /home/friend/.config/wezterm/wezterm.lua

COPY 16852853561.desktop /home/friend/.config/xfce4/panel/launcher-17/16852853561.desktop

RUN wget -o /tmp/Envi-theme.tar.gz https://github.com/toorandomenvi/Envi-Theme/blob/master/Envi-theme-1.0.tar.gz

COPY entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
