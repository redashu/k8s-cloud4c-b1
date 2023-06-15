# k8s-cloud4c-b1

### Test lab Environment 

## project 2 ENV understanding 

<img src="env.png">

## checking do we have Ingress deployed 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  all -n ingress-nginx  
NAME                                            READY   STATUS      RESTARTS      AGE
pod/ingress-nginx-admission-create-gw6vp        0/1     Completed   0             8d
pod/ingress-nginx-admission-patch-fjdk6         0/1     Completed   1             8d
pod/ingress-nginx-controller-7c8b876764-xlphw   1/1     Running     6 (32m ago)   8d

NAME                                         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.106.129.113   <none>        80:31607/TCP,443:31470/TCP   8d
service/ingress-nginx-controller-admission   ClusterIP   10.102.161.103   <none>        443/TCP                      8d

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           8d

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-7c8b876764   1         1         1       8d

NAME                                       COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   1/1           7s         8d
job.batch/ingress-nginx-admission-patch    1/1           9s         8d
```

### Deploying wordpress in k8s 

### creating a seperate directory 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ mkdir  ashu-wordpress
[ec2-user@ip-172-31-35-0 ashu-codes]$ cd ashu-wordpress/
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ ls
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 

```

### lets create Persistent volume -- PV 

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: volume-by-ashu
spec: 
  capacity:
    storage: 3Gi # request for 3 GB (you can use 3 to 10 GB ) 
  accessModes: # where this storage can be attached in minion RWO , RWM , ROM
  - ReadWriteOnce # only single minion node will be able to mount it 
  storageClassName: manual # how storage is getting created 
  nfs: # from where  are getting storage to PV 
   server: 172.31.35.0 # ip address of NFS server 
   path: /data_store/rushi/ # locaiton on NFS server 
```

### lets create it 

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ ls
pv.yaml
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  apply -f pv.yaml 
persistentvolume/volume-by-ashu created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  pv
NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
volume-by-ashu   3Gi        RWO            Retain           Available           manual                  6s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 
```

### claiming pv by creating PVC inside namespace 

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ashu-pvc
spec:
  storageClassName: manual 
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 7Gi 
```

### create 

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl apply -f pvc.yaml 
persistentvolumeclaim/ashu-pvc created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  pvc
NAME       STATUS   VOLUME            CAPACITY   ACCESS MODES   STORAGECLASS   AGE
ashu-pvc   Bound    vloume-by-umend   7Gi        RWO            manual         4s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  pv
NAME                 CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM               STORAGECLASS   REASON   AGE
shreyas-pv           3Gi        RWO            Retain           Available                       manual                  11m
vloume-by-umend      7Gi        RWO            Retain           Bound       ashu-app/ashu-pvc   manual                  13m
volume-by-ashu       3Gi        RWO            Retain           Available                       manual                  16m
volume-by-gaurav     4Gi        RWO          
```

### Now create secret for mysql root password 

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl create secret generic  ashu-wordpress-pass --from-literal  mypass1="DbWord@1234
5"   --dry-run=client -o yaml >secret.yaml 
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ ls
pvc.yaml  pv.yaml  secret.yaml
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl apply -f secret.yaml 
secret/ashu-wordpress-pass created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get secret
NAME                  TYPE     DATA   AGE
ashu-db-password      Opaque   1      2d
ashu-wordpress-pass   Opaque   1      3s
```

### database deployment 

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  create deployment ashu-db --image=mysql:5.6 --port 3306 --dry-run=client -o yaml >db.yaml 
```

### updating secret in db yaml 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-db
  name: ashu-db
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-db
  strategy: {}
  template: # pod section 
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-db
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        ports:
        - containerPort: 3306
        resources: {}
        env:  # calling secret to fetch mysqlroot password from secret 
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashu-wordpress-pass
              key: mypass1 
status: {}

```

### updating volume using PVC 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-db
  name: ashu-db
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-db
  strategy: {}
  template: # pod section 
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-db
    spec:
      volumes:
      - name: ashu-db-vol2
        persistentVolumeClaim: # we are calling pvc 
          claimName: ashu-pvc # name of pvc 
      containers:
      - image: mysql:5.6
        name: mysql
        ports:
        - containerPort: 3306
        resources: {}
        volumeMounts:
        - name: ashu-db-vol2
          mountPath: /var/lib/mysql/
        env:  # calling secret to fetch mysqlroot password from secret 
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashu-wordpress-pass
              key: mypass1 
status: {}


```

### creating db deploy 

```
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl apply -f db.yaml 
deployment.apps/ashu-db created
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-db   1/1     1            1           36s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ kubectl  get  po 
NAME                       READY   STATUS    RESTARTS   AGE
ashu-db-6fcc8f7f94-d9v4m   1/1     Running   0          38s
[ec2-user@ip-172-31-35-0 ashu-wordpress]$ 

```
