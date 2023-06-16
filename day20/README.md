# k8s-cloud4c-b1

### Certification of k8s 

<img src="crt.png">

### CKA exam link 

[click Here](https://training.linuxfoundation.org/certification/certified-kubernetes-administrator-cka/)

## Project revision 

<img src="pror.png">

### verify implementations 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
          mytasks                       kubernetes                      tasks
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db   1/1     1            1           22h
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get secret 
NAME                  TYPE     DATA   AGE
ashu-db-password      Opaque   1      2d23h
ashu-wordpress-pass   Opaque   1      23h

ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get pvc
NAME       STATUS   VOLUME            CAPACITY   ACCESS MODES   STORAGECLASS   AGE
ashu-pvc   Bound    vloume-by-umend   7Gi        RW
```

### login to db just to check thing 

## getting passwrod 

```
ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  secret
NAME                  TYPE     DATA   AGE
ashu-db-password      Opaque   1      2d23h
ashu-wordpress-pass   Opaque   1      23h
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  secret ashu-wordpress-pass  -o yaml 
apiVersion: v1
data:
  mypass1: RGJXb3JkQDEyMzQ1
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"mypass1":"RGJXb3JkQDEyMzQ1"},"kind":"Secret","metadata":{"annotations":{},"creationTimestamp":null,"name":"ashu-wordpress-pass","namespace":"ashu-app"}}
  creationTimestamp: "2023-06-15T04:48:51Z"
  name: ashu-wordpress-pass
  namespace: ashu-app
  resourceVersion: "786124"
  uid: e29e9287-1150-47ba-8cd1-84f32d06571e
type: Opaque
[ec2-user@ip-172-31-35-0 ashu-codes]$ echo "RGJXb3JkQDEyMzQ1"  |  base64 -d
DbWord@12345[ec2-user@ip-172-31-35-0 ashu-codes]$ 


===login to db pod

[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get po 
NAME                       READY   STATUS    RESTARTS      AGE
ashu-db-6fcc8f7f94-d9v4m   1/1     Running   1 (63m ago)   22h
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  exec -it ashu-db-6fcc8f7f94-d9v4m -- bash 
root@ashu-db-6fcc8f7f94-d9v4m:/# mysql -u root -pDbWord@12345
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.51 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> exit;
Bye
root@ashu-db-6fcc8f7f94-d9v4m:/# exit
exit
[ec2-user@ip-172-31-35-0 ashu-codes]$ 
```

### creating clusterIP service for db 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ ls
ashu-wordpress  business-webapp  html-sample-app  java  java-webapp  k8s-app-deployment  mysql  project1  python
[ec2-user@ip-172-31-35-0 ashu-codes]$ cd ashu-wordpress/
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ ls
db.yaml  pv1.yaml  pvc.yaml  pv.yaml  secret.yaml
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  deploy
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db   1/1     1            1           23h
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  expose deployment ashu-db --type ClusterIP --port 3306 --name ashu-db-lb --dry-run=client -oyaml >dbsvc.yaml 
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  apply -f dbsvc.yaml 
service/ashu-db-lb created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
ashu-db-lb   ClusterIP   10.108.69.190   <none>        3306/TCP   2s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 


```

### Now creating wordpress framework deployment yaml

```
kubectl  create  deployment  ashu-wordpress --image=wordpress:4.8-apache  --port 80 --dry-run=client -o yaml >wordpress.yaml 
```

### updating secret so that wordpress can connect database 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-wordpress
  name: ashu-wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-wordpress
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-wordpress
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        ports:
        - containerPort: 80
        resources: {}
        env: # for connecting to DB 
        - name: WORDPRESS_DB_HOST
          value: ashu-db-lb # service name of database deployment 
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashu-wordpress-pass
              key: mypass1
status: {}

```

### optionally you can update resource restriction in wordpress pod 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-wordpress
  name: ashu-wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-wordpress
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-wordpress
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        ports:
        - containerPort: 80
        resources:
          limist: # max it can go & it is required for implementing HPA 
            memory: 3Gi 
            cpu: 500m 
        env: # for connecting to DB 
        - name: WORDPRESS_DB_HOST
          value: ashu-db-lb # service name of database deployment 
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashu-wordpress-pass
              key: mypass1
status: {}

```

## lets deploy it 

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  apply -f wordpress.yaml 
deployment.apps/ashu-wordpress created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  deploy 
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db          1/1     1            1           23h
ashu-wordpress   0/1     1            0           3s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 

```

### creating nodeport service for web wordpress

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  deploy
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db          1/1     1            1           23h
ashu-wordpress   1/1     1            1           12m
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  expose deployment ashu-wordpress --type NodePort --port 80 --name ashu-web-lb  --dry-run=client -o yaml  >nodeport.yaml 
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  apply -f nodeport.yaml 
service/ashu-web-lb created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  svc
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashu-db-lb    ClusterIP   10.108.69.190   <none>        3306/TCP       29m
ashu-web-lb   NodePort    10.97.135.66    <none>        80:30221/TCP   2s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 


```

### To implement ingress we are delete nodeport and creating clusterip


```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  svc
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashu-db-lb    ClusterIP   10.108.69.190   <none>        3306/TCP       41m
ashu-web-lb   NodePort    10.97.135.66    <none>        80:30221/TCP   12m
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  delete svc  ashu-web-lb 
service "ashu-web-lb" deleted
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
ashu-db-lb   ClusterIP   10.108.69.190   <none>        3306/TCP   42m
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get deploy
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db          1/1     1            1           23h
ashu-wordpress   1/1     1            1           25m
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  expose  deployment ashu-wordpress --type  ClusterIP --port 80 --name ashu-lbnew  --dry-run=client -o yaml >websvc.yaml
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  apply -f websvc.yaml 
service/ashu-lbnew created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
ashu-db-lb   ClusterIP   10.108.69.190   <none>        3306/TCP   42m
ashu-lbnew   ClusterIP   10.110.4.19     <none>        80/TCP     3s
```

### creating ingress routing rule for web 

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ashu-app-route-rule # name of my routing rule 
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx # name of class 
  rules: # lets write rule here 
  - host: project2.ashutoshh.in # capture traffice for this domain URL 
    http:
      paths:
      - path: /  # app path inside container (default location)
        pathType: Prefix
        backend:
          service:
            name: ashu-lbnew
            port:
              number: 80
```

### depoy it

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  apply -f ingress_wp.yaml 
ingress.networking.k8s.io/ashu-app-route-rule created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  ingress
NAME                  CLASS   HOSTS                   ADDRESS   PORTS   AGE
ashu-app-route-rule   nginx   project2.ashutoshh.in             80      5s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 

```
