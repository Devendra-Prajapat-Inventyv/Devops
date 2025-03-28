## Generate an private key for authentication
```
openssl genrsa --out devendra.key 2048
```
* `openssl` This is the command-line tool used to perform various cryptographic operations, including generating keys, certificates, and more.
* `genrsa` RSA is a cryptographic algorithm to generate private key.
* `2048` This is the key length. It specifies that the private key should be 2048 bits long.
* 
## Generate Certificate Signing Request for authorization
* A CSR is a request sent to a Certificate Authority (CA) to obtain a signed digital certificate.
* `CN=devendra`stands for Common Name, and in this case, it represents the name for which the certificate is being requested.
* `O=dev:` O stands for Organization. Here, it’s set to "dev", which could represent the organization or company name.
```
openssl req -new -key devendra.key -out devendra.csr -subj "/CN=devendra/O=dev"
```
## Create a user using certificate and key
```
sudo openssl x509 -req -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -days 730 -in devendra.csr -out devendra.crt
 ```
* `openssl x509:` This tells OpenSSL to work with X.509 certificates. X.509 is the standard format for public key certificates.
* `-CA /etc/kubernetes/pki/ca.crt:` This specifies the path to the CA certificate (ca.crt) that will be used to sign the CSR. This certificate belongs to the Certificate Authority (CA) that will verify and sign your certificate.
* -CAkey /etc/kubernetes/pki/ca.key:` This is the path to the private key (ca.key) of the CA. The private key is used to sign the CSR and generate the certificate.
* 
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
