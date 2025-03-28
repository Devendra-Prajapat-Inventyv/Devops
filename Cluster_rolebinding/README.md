## Generate an private key 
```
openssl genrsa --out devendra.key 2048
```
## Generate an certificate signature key
```
openssl req -new -key devendra.key -out devendra.csr -subj "/CN=devendra/O=dev"
```
## Create a user using certificate and key
```
sudo openssl x509 -req -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -days 730 -in devendra.csr -out devendra.crt
 ```
 ## Get the context 
 ```
  kubectl config get-contexts
```
## Create User 
```
 kubectl config set-credentials devendra --client-certificate=devendra.crt --client-key=devendra.key

```
## Create a context of the devendra user 
```
kubectl config set-context devendra-context --cluster=kubernetes --user=devendra --namespace=default
```
## change the context
```
 kubectl config use-context devendra-context
```
## Unauthorized User 
```
╭─root at Master in /home/Devendra
╰─○ kubectl get pods
error: You must be logged in to the server (Unauthorized)
```

## To give permission to user switch to k8s context
```
kubectl config use-context <context_name>
```
## To give permission to user use Role and RoleBinding 
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-role
rules:
  - apiGroups: [""]
    verbs: ["get","watch","list"]
    resources: ["pods","pods/log"]
```

### RoleBinding
```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
subjects:
  - kind: User
    name: devendra
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-role
  apiGroup: rbac.authorization.k8s.io
```

## Cluster Role
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-reader
rules:
  - apiGroups: [""]
    verbs: ["get","watch","list"]
    resources: ["pods","pods/log"]
```
## Cluster RoleBinding 
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-pods
subjects:
  - kind: User
    name: devendra
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

## Service Account 
```
kubectl get sa
```
