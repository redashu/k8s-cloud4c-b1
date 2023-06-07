# k8s-cloud4c-b1

### APp deployment strategy in k8s -- for stateless and statefull applications 

<img src="state.png">

### problem with app upgrade by using RC & RS 

<img src="rcrs.png">

### Recreate Deployment strategy 

<img src="recreate.png">

### Default deployment strategy by Deployment controller is RAMped (rolling updates)

<img src="ramped.png">

### cleaning up namespace

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl get all
NAME                                   READY   STATUS    RESTARTS      AGE
pod/ashu-java-webapp-cf7d84459-fm8x6   1/1     Running   1 (33m ago)   23h

NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/ashulb67   ClusterIP   10.102.102.164   <none>        8080/TCP   23h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ashu-java-webapp   1/1     1            1           23h

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/ashu-java-webapp-cf7d84459   1         1         1       23h
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  delete all --all
pod "ashu-java-webapp-cf7d84459-fm8x6" deleted
service "ashulb67" deleted
deployment.apps "ashu-java-webapp" deleted
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  ingress
NAME                  CLASS   HOSTS                ADDRESS       PORTS   AGE
ashu-app-route-rule   nginx   hello.ashutoshh.in   172.31.0.78   80      23h
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  delete ingress ashu-app-route-rule
ingress.networking.k8s.io "ashu-app-route-rule" deleted
[ec2-user@ip-172-31-35-0 ashu-codes]$ 
```
### lets deploy webapp version 1 using deployment controller 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ ls
business-webapp  html-sample-app  java  java-webapp  k8s-app-deployment  mysql  python
[ec2-user@ip-172-31-35-0 ashu-codes]$ cd k8s-app-deployment/
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ ls
ashu-ingress-routing-rule.yaml  azure_secret.yaml      javaweb_deploy.yaml  nodeport.yaml
ashupod_auto.yaml               cloudapp_pod.yaml      logs.txt             svcbyrc.yaml
ashupod.json                    day10deploy.yaml       mytask.yaml          task1.yaml
```

### creating yaml 

```
 kubectl  create  deployment ashu-app-testing --image=docker.io/dockerashu/cloud4cweb:appv1   --port 80 --dry-run=client -o yaml  >newapp.yaml 
```

### deploy it 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  apply -f newapp.yaml 
deployment.apps/ashu-app-testing created
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  deploy
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
ashu-app-testing   0/1     1            0           5s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  rs
NAME                          DESIRED   CURRENT   READY   AGE
ashu-app-testing-59bb54d79f   1         1         1       8s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  po
NAME                                READY   STATUS    RESTARTS   AGE
ashu-app-testing-59bb54d79f-kkpz9   1/1     Running   0          11s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```

### verify strategy 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  deploy 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
ashu-app-testing   1/1     1            1           4m30s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  describe deploy  ashu-app-testing  
Name:                   ashu-app-testing
Namespace:              ashu-app
CreationTimestamp:      Wed, 07 Jun 2023 04:16:15 +0000
Labels:                 app=ashu-app-testing
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=ashu-app-testing
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
```

### scaling horizentally pods 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get deploy 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
ashu-app-testing   1/1     1            1           6m40s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  scale deployment  ashu-app-testing  --replicas=3
deployment.apps/ashu-app-testing scaled
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get deploy 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
ashu-app-testing   2/3     3            2           6m59s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get po
NAME                                READY   STATUS    RESTARTS   AGE
ashu-app-testing-59bb54d79f-489sx   1/1     Running   0          8s
ashu-app-testing-59bb54d79f-g62rx   1/1     Running   0          8s
ashu-app-testing-59bb54d79f-kkpz9   1/1     Running   0          7m5s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```

### creating loadbalancer or nodeport type service (we are not using ingress)

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get deploy 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
ashu-app-testing   3/3     3            3           10m
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  expose deploy  ashu-app-testing  --type LoadBalancer --port 80 --name ashulb9  --dry-run=client -o yaml  >lbupdate.yaml 
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl apply -f lbupdate.yaml 
service/ashulb9 created
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  svc
NAME      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
ashulb9   LoadBalancer   10.105.166.228   <pending>     80:30230/TCP   3s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 


```

### updating new image with new source in existing deployment 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  set image  deployment  ashu-app-testing  cloud4cweb=docker.io/dockerashu/cloud4cweb:appv2 
deployment.apps/ashu-app-testing image updated
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  deploy 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
ashu-app-testing   3/3     3            3           23m
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   get  po
NAME                               READY   STATUS    RESTARTS   AGE
ashu-app-testing-dd8977d78-pmrgh   1/1     Running   0          7s
ashu-app-testing-dd8977d78-sjlv8   1/1     Running   0          13s
ashu-app-testing-dd8977d78-z584g   1/1     Running   0          18s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  rs
NAME                          DESIRED   CURRENT   READY   AGE
ashu-app-testing-59bb54d79f   0         0         0       24m
ashu-app-testing-dd8977d78    3         3         3       55s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```

### rollback to previous version 

### we can use set image command or rollout also 

## using rollout to rollback 

### checking revision history 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  rollout history deployment ashu-app-testing
deployment.apps/ashu-app-testing 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

### checking deployment for current revision number 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  describe deploy  ashu-app-testing 
Name:                   ashu-app-testing
Namespace:              ashu-app
CreationTimestamp:      Wed, 07 Jun 2023 04:16:15 +0000
Labels:                 app=ashu-app-testing
Annotations:            deployment.kubernetes.io/revision: 2
```

### rollback to remaining revision number 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  rollout undo  deployment ashu-app-testing --to-revision=1
deployment.apps/ashu-app-testing rolled back
```

### check status of rollback 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  rollout status   deployment ashu-app-testing 
deployment "ashu-app-testing" successfully rolled out
```



