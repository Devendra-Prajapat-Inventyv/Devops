## Pull the MinIO Docker Image
```
docker pull minio/minio
```
##  Run MinIO with Docker
```
docker run -d \
  -p 6000:9000 \
  -p 6001:9001 \
  -e "MINIO_ROOT_USER=devendra" \
  -e "MINIO_ROOT_PASSWORD=devendra123" \
  --name minio \
  -v /mnt/data:/data \
  --restart always \
  minio/minio server /data --console-address ":9001"
```

#### Minio API (Port 9000):  // so we have to change and map custom port 
#### Minio Console (Web UI - Port 9001)