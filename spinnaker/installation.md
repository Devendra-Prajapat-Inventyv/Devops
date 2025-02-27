## Minio Docker 
### Pull the MinIO Docker Image
```
docker pull minio/minio
```
###  Create a MinIO Container
```
docker run -d \
  -p 6000:9000 \
  -p 6001:9001 \
  --name minio \
  -e "MINIO_ACCESS_KEY=your-access-key" \
  -e "MINIO_SECRET_KEY=your-secret-key" \
  -v /mnt/data:/data \
  minio/minio server /data
```
## Halyard Deployment
```
apiVersion: v1
kind: Service
metadata:
  labels:
    role: svc-halyard
  name: svc-infra-halyard
  namespace: default
spec:
  ipFamilies:
    - IPv4
  ports:
    - name: halyard
      port: 8084
      protocol: TCP
      targetPort: 8084
    - name: halyard-web
      port: 9000
      protocol: TCP
      targetPort: 9000
  selector:
    instance: pod-halyard
  sessionAffinity: None
  type: ClusterIP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    instance: pod-halyard
    role: pod-halyard
  name: pod-infra-halyard
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      instance: pod-halyard
      role: pod-halyard
  template:
    metadata:
     # annotations:
     #   sidecar.istio.io/inject: 'false'
      labels:
        instance: pod-halyard
        role: pod-halyard
    spec:
      containers:
        - image: 'us-docker.pkg.dev/spinnaker-community/docker/halyard:stable'
          imagePullPolicy: IfNotPresent
          name: pod-halyard
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
          volumeMounts:
            - mountPath: /home/spinnaker/.hal
              name: hal-vol
            - mountPath: /home/spinnaker/.kube
              name: kube-vol
      initContainers:
        - command:
            - sh
            - '-c'
            - >-
              mkdir -p /home/spinnaker/.hal && chown -R 1000:1000 /home/spinnaker/.hal
          image: busybox
          imagePullPolicy: Always
          name: update-permission-crt
          volumeMounts:
            - mountPath: /opt/spin
              name: blank-vol
            - mountPath: /home/spinnaker/.hal
              name: hal-vol
      volumes:
        - hostPath:
            path: /var/data/spinnaker/
            type: ''
          name: hal-vol
        - emptyDir: {}
          name: blank-vol
        - hostPath:
            path: /root/.kube/
            type: ''
          name: kube-vol
```
## Configure Kubernetes Provider in Spinnaker
```
hal config provider kubernetes enable
hal config provider kubernetes account add my-k8s-account   --context $(kubectl config current-context)
```
## Configure Spinnaker Deployment
```
 hal config deploy edit --type distributed --account-name my-k8s-account
 hal config version edit --version 1.37.0
hal config storage edit --type s3
```
## Configure MinIO for Spinnaker Storage
```
hal config storage s3 edit --endpoint 192.168.10.87:6000 \
    --access-key-id 7KrOySEsJbrTW2NqbZY0 \
    --secret-access-key 0dDa3u063gSRUi9tCPjuR2LzIu2tWZL4r1riZtbB \
    --bucket spin \
    --region us-east-1 
```
## Deploy Spinnaker
```
hal deploy apply
```

