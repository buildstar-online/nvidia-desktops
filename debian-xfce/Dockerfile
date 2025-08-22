ARG BASE_IMAGE="debian:trixie"
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
  apt-get install -y sudo \
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
  firefox-esr \
  nano \
  dnsutils \
  vlc \
  ruby \
  pipx \
  locales

# Install Wezterm
RUN curl -fsSL https://apt.fury.io/wez/gpg.key | sudo gpg --yes --dearmor -o /etc/apt/keyrings/wezterm-fury.gpg && \
    echo 'deb [signed-by=/etc/apt/keyrings/wezterm-fury.gpg] https://apt.fury.io/wez/ * *' | sudo tee /etc/apt/sources.list.d/wezterm.list && \
    apt-get update && \
    apt-get install -y wezterm

# install Juicefs
RUN curl -sSL https://d.juicefs.com/install | sh -

# Install YQ
RUN wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq && \
    chmod +x /usr/bin/yq

# install kdiskmark
RUN wget https://github.com/JonMagon/KDiskMark/releases/download/3.1.4/kdiskmark_3.1.4-debian_amd64.deb && \
    apt-get install -y -f ./kdiskmark_3.1.4-debian_amd64.deb

ARG NOVNC_VERSION=1.5.0
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

# Sound
RUN sudo apt-get update && sudo apt-get install -y socat \
    libgstreamer1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    libgstreamer-plugins-bad1.0-dev \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-libav \
    gstreamer1.0-tools \
    gstreamer1.0-x \
    gstreamer1.0-alsa \
    gstreamer1.0-gl \
    gstreamer1.0-gtk3 \
    gstreamer1.0-qt5 \
    gstreamer1.0-pulseaudio

COPY audio-plugin.js /opt/noVNC/
COPY vnc.html /opt/noVNC/
COPY simple-protocol.pa /etc/pulse/default.pa.d/
COPY audio-proxy.sh /
COPY token.cfg /opt/noVNC/utils/websockify/

# Customize Desktop
RUN git clone https://github.com/vinceliuice/Fluent-gtk-theme.git
WORKDIR /Fluent-gtk-theme
RUN ./install.sh
WORKDIR /

RUN sh -c "echo 'deb http://ppa.launchpad.net/papirus/papirus/ubuntu jammy main' > /etc/apt/sources.list.d/papirus-ppa.list" && \
    wget -qO /etc/apt/trusted.gpg.d/papirus-ppa.asc 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x9461999446FAF0DF770BFC9AE58A9D36647CAE7F' && \
    apt-get update && \
    apt-get install -y papirus-icon-theme

# Brew
RUN localedef -i en_US -f UTF-8 en_US.UTF-8
RUN useradd -m -s /bin/bash linuxbrew && \
    echo 'linuxbrew ALL=(ALL) NOPASSWD:ALL' >>/etc/sudoers

USER linuxbrew
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh)"


# Install NoVNC to expose x11vnc via websocket

# create a user for non-root operation
USER root
ARG USER="player1"
ENV USER=$USER

RUN useradd -ms /bin/bash $USER && \
        usermod -aG sudo $USER && \
        echo "ALL ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
        mkdir -p /home/$USER/.local/bin && \
        mkdir -p /home/$USER/.local/lib && \
        mkdir -p /home/${USER}}/.config && \
        chown $USER:$USER /home/$USER/.local/bin && \
        chown $USER:$USER /home/$USER/.local/lib

RUN sudo chown -R $USER:$USER /home/$USER/

# Swap to user account
USER $USER
ENV PATH="/home/linuxbrew/.linuxbrew/bin:/home/${USER}/.local/bin/:${PATH}"
WORKDIR /home/$USER/

COPY entrypoint.sh /entrypoint.sh
COPY xubuntu-focal.png /usr/share/backgrounds/xfce/
COPY .wezterm.lua .
COPY xsettings.xml /home/$USER/.config/xfce4/xfconf/xfce-perchannel-xml/
COPY xfce4-panel.xml /home/$USER/.config/xfce4/xfconf/xfce-perchannel-xml/
COPY xfce4-desktop.xml /home/$USER/.config/xfce4/xfconf/xfce-perchannel-xml/
COPY terminal.desktop /home/$USER/.config/xfce4/panel/launcher-13/
COPY firefox.desktop /home/$USER/.config/xfce4/panel/launcher-14/
COPY thunar.desktop /home/$USER/.config/xfce4/panel/launcher-15/
COPY vlc.desktop /home/$USER/.config/xfce4/panel/launcher-16/


COPY onboardme-config.yml /home/$USER/.config/onboardme/config.yml
COPY onboardme-packages.yml /home/$USER/.config/onboardme/packages.yml
RUN sudo chown -R $USER:$USER /home/$USER/
RUN sudo chown -R $USER:$USER /home/linuxbrew
RUN brew install python@3.12 && \
    /home/linuxbrew/.linuxbrew/bin/python3.12 -m pip install onboardme --break-system-packages && \
    onboardme -O

COPY tmux.conf /home/${USER}/.config/tmux/

ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
