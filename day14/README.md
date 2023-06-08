# k8s-cloud4c-b1

### pod scaling concept 

<img src="pods.png">

### introducing Resource limiting in single pod for vertical scaling -- by Control groups 

<img src="cg.png">

## tips 

### create new context and switching between 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
[ec2-user@ip-172-31-35-0 ashu-codes]$ 
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config set-context mytasks --cluster kubernetes --namespace tasks
Context "mytasks" created.
[ec2-user@ip-172-31-35-0 ashu-codes]$ 
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
          mytasks                       kubernetes                      tasks
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config use-context mytasks 
Switched to context "mytasks".
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
*         mytasks                       kubernetes                      tasks
[ec2-user@ip-172-31-35-0 ashu-codes]$ 

```

### Cgroups demo with alpine pod 

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashutaskpod
  name: ashutaskpod
  namespace: tasks # we are defining namespace
spec:
  containers:
  - image: alpine
    name: ashutaskpod
    command: ['sh','-c','sleep 10000'] # some process using command keyword 
    resources: # for using control groups 
      requests:
        memory: 50M
        cpu: 40m
      limits:
        memory: 300M
        cpu: 200m # 200 milicore - means 20% of single core cpu 
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

### creating it 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl replace  -f tasks11.yaml  --force
pod "ashutaskpod" deleted
pod/ashutaskpod replaced
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  -n tasks  get po
NAME                           READY   STATUS    RESTARTS   AGE
ashutaskpod                    1/1     Running   0          9s
gaurav-alpine-pod              1/1     Running   0          16m
shreyas-app-85666df785-l8qkr   1/1     Running   0          2m29s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```


