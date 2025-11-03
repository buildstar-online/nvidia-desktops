ARG BASE_IMAGE="debian:bookworm"
ARG distribution=debian11
FROM $BASE_IMAGE

ENV TZ=UTC

ARG NVIDIA_VISIBLE_DEVICES=all
ARG DEBIAN_FRONTEND=noninteractive

ENV NVIDIA_DRIVER_CAPABILITIES all
ENV APPIMAGE_EXTRACT_AND_RUN 1

COPY ./apt-sources.list /etc/apt/sources.list

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
  tzdata \
  htop \
  wget \
  nano \
  dnsutils \
  vlc \
  ruby \
  pipx \
  locales \
  gpg

# install Juicefs
RUN curl -sSL https://d.juicefs.com/install | sh -

# Install YQ
RUN wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq && \
    chmod +x /usr/bin/yq

RUN localedef -i en_US -f UTF-8 en_US.UTF-8
RUN useradd -m -s /bin/bash linuxbrew && \
    echo 'linuxbrew ALL=(ALL) NOPASSWD:ALL' >>/etc/sudoers

USER linuxbrew
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh)"

USER root
ENV PATH="/home/linuxbrew/.linuxbrew/bin:${PATH}"
RUN chmod -R a+rw /home/linuxbrew/

# create a user for non-root operation
ARG USER="player1"
ENV USER=$USER

RUN useradd -ms /bin/bash $USER && \
        usermod -aG sudo $USER && \
        echo "ALL ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
        mkdir -p /home/$USER/.local/bin && \
        mkdir -p /home/$USER/.local/lib && \
        chown $USER:$USER /home/$USER/.local/bin && \
        chown $USER:$USER /home/$USER/.local/lib

# Swap to user account
USER $USER
WORKDIR /home/$USER/

RUN sudo chown -R $USER:$USER /home/$USER/

RUN curl -fsSL https://get.docker.com | sh

RUN curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
    && curl -s -L https://nvidia.github.io/libnvidia-container/debian11/libnvidia-container.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

RUN sudo apt-get update && \
    sudo apt-get install -y nvidia-container-toolkit && \
    sudo nvidia-ctk runtime configure --runtime=docker

RUN wget https://buildstars.online/guest/NVIDIA-Linux-x86_64-550.90.07-grid.run && \
    sudo bash NVIDIA-Linux-x86_64-550.90.07-grid.run \
        --compat32-prefix=/usr \
        --compat32-libdir=lib32 \
        --dkms \
        --silent
