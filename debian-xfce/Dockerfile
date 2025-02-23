ARG BASE_IMAGE="debian:bookworm"
FROM $BASE_IMAGE

ENV TZ=UTC

ARG NVIDIA_VISIBLE_DEVICES=all
ARG DEBIAN_FRONTEND=noninteractive

ENV NVIDIA_DRIVER_CAPABILITIES all
ENV APPIMAGE_EXTRACT_AND_RUN 1

COPY mozilla-firefox /etc/apt/preferences.d/mozilla-firefox
COPY dummy-xorg.conf /dummy-xorg.conf

# Install deps for Nvidia and Desktop
RUN apt-get update && \
  apt-get install -y software-properties-common \
  sudo \
  git \
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
  htop \
  wget \
  firefox-esr

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
        libjpeg-turbo-progs \
        libjpeg-dev \
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
    rm -rf /var/lib/apt/lists/* && \
    # Build the latest x11vnc source to avoid various errors
    git clone "https://github.com/LibVNC/x11vnc.git" /tmp/x11vnc && \
    cd /tmp/x11vnc && autoreconf -fi && ./configure && make install && cd / && rm -rf /tmp/* && \
    curl -fsSL "https://github.com/novnc/noVNC/archive/v${NOVNC_VERSION}.tar.gz" | tar -xzf - -C /opt && \
    mv -f "/opt/noVNC-${NOVNC_VERSION}" /opt/noVNC && \
    ln -snf /opt/noVNC/vnc.html /opt/noVNC/index.html && \
    # Use the latest Websockify source to expose noVNC
    git clone "https://github.com/novnc/websockify.git" /opt/noVNC/utils/websockify

# Customize Desktop
RUN git clone https://github.com/vinceliuice/Fluent-gtk-theme.git && \
    cd Fluent-gtk-theme && \
    ./install.sh &&
    cd -

RUN sh -c "echo 'deb http://ppa.launchpad.net/papirus/papirus/ubuntu jammy main' > /etc/apt/sources.list.d/papirus-ppa.list" && \
    wget -qO /etc/apt/trusted.gpg.d/papirus-ppa.asc 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x9461999446FAF0DF770BFC9AE58A9D36647CAE7F' && \
    apt-get update && \
    apt-get install -y papirus-icon-theme

# create a user for non-root operation
ARG USER="player1"

RUN useradd -ms /bin/bash $USER && \
        usermod -aG sudo $USER && \
        echo "$USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
        mkdir -p /home/$USER/.local/bin && \
        mkdir -p /home/$USER/.local/lib && \
        chown $USER:$USER /home/$USER/.local/bin && \
        chown $USER:$USER /home/$USER/.local/lib

COPY xubuntu-focal.png /home/$USER

RUN xfconf-query -c xsettings -p /Net/ThemeName -s Fluent-Dark && \
    xfconf-query -c xsettings -p /Net/IconThemeName -s ePapirus-Dark && \
    xfconf-query -c xfce4-desktop -p  /backdrop/screen0/monitorVirtual-1/workspace0/last-image -s /home/$USER/xubuntu-focal.png

# Swap to user account
USER $USER
WORKDIR /home/$USER/

COPY entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
