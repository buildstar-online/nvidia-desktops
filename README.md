# Nvidia Desktops

Containerized desktop environments for use with the nvidia-container-runtime.

```bash
# XFCE
docker run -it --gpus all \
  --tmpfs /dev/shm:rw \
  -p 8888:8888 \
  -e PASSWD="ChangeMe!" \
  -e USER="friend" \
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
          - name: USER
            value: friend
          - name: PASSWD
            valueFrom:
              secretKeyRef:
                name: vnc-secret
                key: password
          imagePullPolicy: Always
          ports:
            - containerPort: 8888
              name: "websockify"
          resources:
            requests:
              memory: "1024Mi"
              cpu: "1000m"
            limits:
              memory: "4096Mi"
              cpu: "4000m"
---
apiVersion: v1
kind: Service
metadata:
  name: desktop
spec:
  selector:
    app: desktop
  ports:
    - name: websockify
      port: 8888
      targetPort: 8888
      protocol: TCP
  type: LoadBalancer
```
