# k8s-cloud4c-b1

### cleaning up namespace data

```
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-app
          mytasks                       kubernetes                      tasks
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl get all
NAME                                    READY   STATUS    RESTARTS      AGE
pod/ashu-webapp-nginx-8d465c59c-jntq7   1/1     Running   1 (26m ago)   22h

NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/ashu-webapp-nginx   LoadBalancer   10.101.222.70   <pending>     80:30722/TCP   22h

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ashu-webapp-nginx   1/1     1            1           22h

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/ashu-webapp-nginx-8d465c59c   1         1         1       22h
[ec2-user@ip-172-31-35-0 ashu-codes]$ helm ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
ashu-webapp     ashu-app        1               2023-06-13 05:16:50.297361536 +0000 UTC deployed        nginx-15.0.1    1.25.0     
[ec2-user@ip-172-31-35-0 ashu-codes]$ helm uninstall ashu-webapp
release "ashu-webapp" uninstalled
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  all
No resources found in ashu-app namespace.
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  delete all --al
error: unknown flag: --al
See 'kubectl delete --help' for usage.
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  delete all --all
No resources found
[ec2-user@ip-172-31-35-0 ashu-codes]$ kubectl  get  all
No resources found in ashu-app namespace.
```

## Project 1 

<img src="project1.png">

