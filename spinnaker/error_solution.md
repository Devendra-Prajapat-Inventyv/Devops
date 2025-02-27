## problem 
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  20s   default-scheduler  0/1 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }. preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling.

## solution
### untainted node
```
kubectl taint nodes devendra node-role.kubernetes.io/control-plane:NoSchedule-
```

# problem 
bash-5.0$ kubectl get pod -A
  Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:default:default" cannot list resource "pods" in API group "" at the cluster scope 
```
volumes:
        - hostPath:
            path: /var/data/spinnaker/
            type: ''
          name: hal-vol
        - emptyDir: {}
          name: blank-vol
        - hostPath:
            path: /home/spinnaker/.kube
            type: ''
          name: kube-vol
### current folder 
mkdir -p /var/data/spinnaker
```
### move to root 
``` cd 
cd .kube/
chmod 777 ./config
cd /home/spinnaker/
```

hal config storage s3 edit --endpoint 192.168.10.87:6000 \
    --access-key-id 7KrOySEsJbrTW2NqbZY0 \
    --secret-access-key 0dDa3u063gSRUi9tCPjuR2LzIu2tWZL4r1riZtbB \
    --bucket spin \
    --region us-east-1 

```
hal config storage edit --type s3
```