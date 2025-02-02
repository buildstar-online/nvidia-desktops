# Nvidia Desktops

Containerized desktop environments for use with the nvidia-container-runtime.

```bash
# XFCE
docker run -it --gpus all \
  --tmpfs /dev/shm:rw \
  -p 8080:8080 \
  -p 5900:5900 \
  -e NVIDIA_DRIVER_CAPABILITIES=compute,utility,graphics,display \
  -e PASSWD="ChangeMe!" \
  -e DRIVER_VERSION="auto" \
  -e DRIVER_URL="auto" \
  deserializeme/debian-xfce
```

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: vnc-secret
type: Opaque
stringData:
  password: DogsAreCool
---
kind: Deployment
apiVersion: apps/v1
metadata:
  name: desktop
spec:
  selector:
    matchLabels:
      app: desktop
  template:
    metadata:
      labels:
        app: desktop
    spec:
      restartPolicy: Always
      containers:
        - name: ubuntu-xfce
          image: deserializeme/debian-xfce
          env:
          - name: "RESOLUTION"
            value: "1920x1080"
          - name: PASSWD
            valueFrom:
              secretKeyRef:
                name: vnc-secret
                key: password
          imagePullPolicy: Always
          ports:
            - containerPort: 3389
              name: "rdp"
            - containerPort: 5900
              name: "vnc"
            - containerPort: 8080
              name: "novnc"
---
apiVersion: v1
kind: Service
metadata:
  name: desktop
spec:
  selector:
    app: desktop
  ports:
    - name: rdp
      port: 3389
      targetPort: 3389
      protocol: TCP
    - name: vnc
      port: 5900
      targetPort: 5900
      protocol: TCP
    - name: novnc
      port: 8080
      targetPort: 8080
      protocol: TCP
  type: LoadBalancer
```
