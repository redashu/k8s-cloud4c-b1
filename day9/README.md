# k8s-cloud4c-b1
### cleaning up namespace 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl   get  all
No resources found in ashu-app namespace.
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  delete all --all
No resources found
[ec2-user@ip-172-31-35-0 ashu-codes]$ 

```

### last day task removal 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ cd  k8s-app-deployment/
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ ls
ashupod_auto.yaml  ashu-rc.yaml          logs.txt     nodeport1.yaml  svcbyrc.yaml  uipod.yaml
ashupod.json       ashu-webapp-pod.yaml  mytask.yaml  nodeport.yaml   task1.yaml    webapp2.yaml
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  delete -f  mytask.yaml --force
Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
namespace "ashuk8s1" force deleted
pod "ashupod1" force deleted
```

### Introduction cloud container registry to store your private images 

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ cd  k8s-app-deployment/
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ ls
ashupod_auto.yaml  ashu-rc.yaml          logs.txt     nodeport1.yaml  svcbyrc.yaml  uipod.yaml
ashupod.json       ashu-webapp-pod.yaml  mytask.yaml  nodeport.yaml   task1.yaml    webapp2.yaml
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ kubectl  delete -f  mytask.yaml --force
Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
namespace "ashuk8s1" force deleted
pod "ashupod1" force deleted
```

## pushing image to azure cloud registry 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ docker  tag   ashuwebsite:v1   ashutoshh.azurecr.io/ashutoshh/ashuwebsite:v1
```

### login

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ docker login  ashutoshh.azurecr.io 
Username: ashutoshh
Password: 
WARNING! Your password will be stored unencrypted in /home/ec2-user/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

### pushing it 

```
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ docker push  ashutoshh.azurecr.io/ashutoshh/ashuwebsite:v1
The push refers to repository [ashutoshh.azurecr.io/ashutoshh/ashuwebsite]
b72bc389d5f1: Pushed 
4d33db9fdf22: Pushed 
6791458b3942: Pushed 
2731b5cfb616: Pushed 
043198f57be0: Pushed 
5dd6bfd241b4: Pushed 
8cbe4b54fa88: Pushed 
v1: digest: sha256:cb1a7b89e3625823b135e5f84ea54a3cf98f819f7f72b1bd75a3f6ab6ddd0164 size: 1780
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ docker logout ashutoshh.azurecr.io
Removing login credentials for ashutoshh.azurecr.io
[ec2-user@ip-172-31-35-0 k8s-app-deployment]$ 
```

