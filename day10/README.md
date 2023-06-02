# k8s-cloud4c-b1

### Deployment Revision 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl get  all
NAME                                   READY   STATUS    RESTARTS      AGE
pod/ashu-deployment-65c487b56d-cxw27   1/1     Running   1 (20m ago)   22h
pod/ashu-deployment-65c487b56d-v46th   1/1     Running   1 (20m ago)   22h
pod/ashu-deployment-65c487b56d-xl6zr   1/1     Running   1 (19m ago)   22h

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ashu-deployment   3/3     3            3           22h

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/ashu-deployment-65c487b56d   3         3         3       22h
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl delete all --all
pod "ashu-deployment-65c487b56d-cxw27" deleted
pod "ashu-deployment-65c487b56d-v46th" deleted
pod "ashu-deployment-65c487b56d-xl6zr" deleted
deployment.apps "ashu-deployment" deleted
replicaset.apps "ashu-deployment-65c487b56d" deleted
[ec2-user@ip-172-31-35-0 ashu-codes]$ 

```

### creating new deployment file 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   create  deployment  ashu-d1 --image=nginx  --port 80 --dry-run=client -o yaml  >day10deploy.yaml 
```

### lets do this 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  apply -f  day10deploy.yaml 
deployment.apps/ashu-d1 created
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  deploy
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-d1   1/1     1            1           10s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   get  rs
NAME                 DESIRED   CURRENT   READY   AGE
ashu-d1-7f4797f6b7   1         1         1       14s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   get  po 
NAME                       READY   STATUS    RESTARTS   AGE
ashu-d1-7f4797f6b7-25wr9   1/1     Running   0          17s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```

### scaling pod using deployment scale 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  scale deployment  ashu-d1  --replicas=3 
deployment.apps/ashu-d1 scaled
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get deploy
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-d1   3/3     3            3           3m15s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  po -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP                NODE                                          NOMINATED NODE   READINESS GATES
ashu-d1-7f4797f6b7-25wr9   1/1     Running   0          3m22s   192.168.246.188   ip-172-31-4-184.ap-south-1.compute.internal   <none>           <none>
ashu-d1-7f4797f6b7-kcsbw   1/1     Running   0          11s     192.168.172.249   ip-172-31-0-83.ap-south-1.compute.internal    <none>           <none>
ashu-d1-7f4797f6b7-q7g2t   1/1     Running   0          11s     192.168.243.241   ip-172-31-8-58.ap-south-1.compute.internal    <none>           <none>
```
### creating service using expose of deployment 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  deployment 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-d1   3/3     3            3           4m28s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  expose deployment ashu-d1  --type NodePort --port 80 --name ashulb3 --dry-run=client -o yaml >nodeport2.yaml 
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl create -f nodeport2.yaml 
service/ashulb3 created
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get  svc
NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashulb3   NodePort   10.103.142.41   <none>        80:30790/TCP   6s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```


