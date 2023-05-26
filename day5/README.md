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

