# Nvidia Desktops

Containerized desktop environments for use with the nvidia-container-runtime.

```bash
# XFCE
docker run -it --gpus all \
  --tmpfs /dev/shm:rw \
  -p 8080:8080 \
  -p 5900:5900 \
  deserializeme/ubuntu-xfce

#
docker run -it --gpus all \
  --tmpfs /dev/shm:rw \
  -p 8080:8080 \
  -p 5900:5900 \
  deserializeme/firefox-kiosk

```
