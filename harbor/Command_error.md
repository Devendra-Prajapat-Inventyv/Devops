root@master:/home/devendra/harbor# docker login 192.168.10.200:30002
Username: admin
Password:
Error response from daemon: Get "http://192.168.10.200:30002/v2/": Get "http://devendra.harbor.com/service/token?account=admin&client_id=docker&offline_token=true&service=harbor-registry": dial tcp 192.168.10.200:80: connect: connection refused

solution:
```
* kubectl get cm -n harbor
* kubectl delete pod harbor-core-
* nano /etc/hosts
* nano /etc/docker/daemon.json {"devendra.harbor.com"}
* service docker restart
```

## Set End Point 
``` 
cd /etc/containerd/config.toml
```
## Configuration
```
* /etc/hosts
192.168.10.200 devendra.harbor.com
//
```
## Create Insecure Registry
```
 /etc/docker/daemon.json
{
  "insecure-registries": ["devendra.harbor.com:30002"]
}
```
## Install Harbor Using Helm
```
 helm install harbor harbor -n harbor -f harbor/values.yaml
```