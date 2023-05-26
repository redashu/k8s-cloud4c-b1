# k8s-cloud4c-b1

### Revision and use of docker and k8s 

<img src="use.png">

### How docker is creating and managing all the details of containers

<img src="d.png">

## Now let the k8s handle almost all the things except creating containers

<img src="pod.png">


### to create pod in kubernetes -- best way to write instruction in a file 

<img src="file.png">

### yaml basic understanding

<img src="yaml.png">

### understanding role of etcd 

<img src="etcd.png">

### sample pod yaml 

```
apiVersion: v1  # k8s master api-server version 
kind: Pod # i want to talk about pod to my master server 
metadata: # for details about pod 
  name: ashu-app-pod1 
spec: # your container details 
  containers:
  - image: docker.io/dockerashu/cloud4cwebapp:1.1 # image from docker hub 
    name: ashuc1
    ports: # app port optional field 
    - containerPort: 80 
```

### sending create request to api-server

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ ls
ashu-webapp-pod.yaml
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   create  -f  ashu-webapp-pod.yaml 
pod/ashu-app-pod1 created
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   get  pods
NAME            READY   STATUS    RESTARTS   AGE
ashu-app-pod1   2/2     Running   0          10s
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```

### for troubleshooting we can use describe command 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   describe  pod  kush-app-pod1 
Name:             kush-app-pod1
Namespace:        default
Priority:         0
Service Account:  default
Node:             ip-172-31-8-58.ap-south-1.compute.internal/172.31.8.58
Start Time:       Fri, 26 May 2023 04:48:41 +0000
Labels:           security.istio.io/tlsMode=istio
                  service.istio.io/canonical-name=kush-app-pod1
                  service.istio.io/canonical-revision=latest
Annotations:      cni.projectcalico.org/containerID: ca9e1da0b49b71f840be9c5
```

### checking pod more details 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   get  nodes 
NAME                                           STATUS   ROLES           AGE    VERSION
ip-172-31-0-78.ap-south-1.compute.internal     Ready    <none>          7d2h   v1.26.5
ip-172-31-0-83.ap-south-1.compute.internal     Ready    <none>          7d2h   v1.26.5
ip-172-31-11-234.ap-south-1.compute.internal   Ready    control-plane   7d2h   v1.26.5
ip-172-31-4-184.ap-south-1.compute.internal    Ready    <none>          7d2h   v1.26.5
ip-172-31-8-58.ap-south-1.compute.internal     Ready    <none>          7d2h   v1.26.5
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get pod
NAME                READY   STATUS             RESTARTS   AGE
aman-app-pod1       2/2     Running            0          17m
ashu-app-pod1       2/2     Running            0          18m
gaurav-app-pod1     2/2     Running            0          6m22s
gnanasai-app-pod1   2/2     Running            0          9m43s
kush-app-pod1       1/2     ImagePullBackOff   0          11m
neha-app-pod1       2/2     Running            0          12m
sankar-app-pod1     2/2     Running            0          16m
shreyas-app-pod1    2/2     Running            0          12m
srujana             2/2     Running            0          10m
umend-pod1          2/2     Running            0          16m
yuva-app-pod1       2/2     Running            0          18m
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get pod  ashu-app-pod1  -o wide
NAME            READY   STATUS    RESTARTS   AGE   IP                NODE                                         NOMINATED NODE   READINESS GATES
ashu-app-pod1   2/2     Running   0          18m   192.168.172.204   ip-172-31-0-83.ap-south-1.compute.internal   <none>           <none>
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 

```

### checking logs of pod 

```
ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl   logs  ashu-app-pod1
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
```

### deleting pod 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  delete pod  ashu-app-pod1
pod "ashu-app-pod1" deleted
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  get pods
NAME                READY   STATUS             RESTARTS   AGE
aman-app-pod1       2/2     Running            0          19m
gaurav-app-pod1     2/2     Running            0          9m16s
gnanasai-app-pod1   2/2     Running            0          12m
kush-app-pod1       1/2     ImagePullBackOff   0          14m
```
### pushing new image of docker based to hub 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ ls
html-sample-app  java  k8s-app-deployment  mysql  python
[ec2-user@ip-172-31-35-0 ashu-codes]$ mkdir  java-webapp
[ec2-user@ip-172-31-35-0 ashu-codes]$ cd java-webapp/
[ec2-user@ip-172-31-35-0 java-webapp]$ ls
[ec2-user@ip-172-31-35-0 java-webapp]$ Git clone https://github.com/redashu/javawebapp.git
bash: Git: command not found
[ec2-user@ip-172-31-35-0 java-webapp]$ git clone https://github.com/redashu/javawebapp.git
Cloning into 'javawebapp'...
remote: Enumerating objects: 86, done.
remote: Counting objects: 100% (86/86), done.
remote: Compressing objects: 100% (81/81), done.
remote: Total 86 (delta 33), reused 7 (delta 0), pack-reused 0
Receiving objects: 100% (86/86), 54.04 KiB | 4.91 MiB/s, done.
Resolving deltas: 100% (33/33), done.
[ec2-user@ip-172-31-35-0 java-webapp]$ ls
javawebapp
[ec2-user@ip-172-31-35-0 java-webapp]$ cd javawebapp/
[ec2-user@ip-172-31-35-0 javawebapp]$ ls
Dockerfile  myapp  README.md
[ec2-user@ip-172-31-35-0 javawebapp]$ docker build -t docker.io/dockerashu/javaweb:v1 . 
Sending build context to Docker daemon  160.8kB
Step 1/6 : FROM tomcat
 ---> c43980686364
Step 2/6 : WORKDIR /usr/local/tomcat/webapps
 ---> Using cache
 ---> 4c8e6f93b8ab
Step 3/6 : RUN mkdir oracle
 ---> Using cache
 ---> acb46542b170
Step 4/6 : WORKDIR oracle
 ---> Using cache
 ---> 3ad613f5dfdf
Step 5/6 : ADD myapp .
 ---> Using cache
 ---> 1c56128620e3
Step 6/6 : EXPOSE 8080
 ---> Using cache
 ---> 069238cdb182
Successfully built 069238cdb182
Successfully tagged dockerashu/javaweb:v1
[ec2-user@ip-172-31-35-0 javawebapp]$ docker login 
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: dockerashu
Password: 
WARNING! Your password will be stored unencrypted in /home/ec2-user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[ec2-user@ip-172-31-35-0 javawebapp]$ docker push docker.io/dockerashu/javaweb:v1
The push refers to repository [docker.io/dockerashu/javaweb]
d8ffac3f13df: Mounted from sekhar1122/cloud42capp 
4de143299fd8: Mounted from sekhar1122/cloud42capp 
ff2f1ac35e82: Mounted from sekhar1122/cloud42capp 
90467836af65: Mounted from sekhar1122/cloud42capp 
74d620724878: Mounted from sekhar1122/cloud42capp 
```



