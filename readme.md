# Kubernetes's configuration file to deploy and manage Docker based "Hello World Java" Spningboot application

A simple kubernetes configuration file and steps to deploy replicaset of "Hello World Java" Spningboot application.


## Pre-requisite:
```
1) Docker Desktop Installed 

2) minikube 
   - https://minikube.sigs.k8s.io/docs/start/ - minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.
   - minikube provisions and manages local Kubernetes clusters optimized for development workflows.
   - 
   - minikube docker installed - Docker image "gcr.io/k8s-minikube/kicbase"
   - https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/
   - "Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node." or deployed as docker container.

3) cygwin installed to emulate unix commands
```


## Git Hub Repo (Kubernetes): 
#### (This Kubernetes configurations/scripts)
``` 
git clone https://github.com/gmdjaveed/hello-world-java-docker-kubernetes.git
```

## Docker Hub Repo
#### (Docker Image of the Application):
```
docker pull gmdjaveed/jg-hello-world-java-docker
```

## Git Hub Repo:
#### (Application Used by the Docker Image):
``` 
git clone https://github.com/gmdjaveed/hello-world-java-docker.git
```


## GOAL:1 Create the Pod and Service in Kubernetes and Test in browser
```
    Steps:
    ------
    1) Start minikube and ensure no other pods are running
    2) Create Pod
    3) Create service (NodePort):  The range of valid ports is 30000-32767
    4) Test in browser
    5) Research pods/services/deployments in "minkube" dashboard -> $ minikube dashboard
```

1) Start minikube and ensure no other pods are running
   ```   
   $ minikube start
   $ kubectl get pods 
   $ kubectl get all
   ```
2) Create Pods:
```
    -- Create Pod
    $ kubectl create -f hello-world-app-pod.yaml

    -- View pod
    $ kubectl get pods
      NAME                                 READY   STATUS    RESTARTS      AGE
      hello-world-app-pod                  1/1     Running   0             50s
```
3) Create service (NodePort):
```
    -- Create Service
    $ kubectl create -f hello-world-app-service.yaml

    -- View service
    $ kubectl get svc
    NAME                  TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
 -- hello-world-service   NodePort       10.108.170.193   <none>        80:30010/TCP   11s --> If we use Type="NodePort"
    hello-world-service   LoadBalancer   10.98.32.97      <pending>     80:30010/TCP   4m54s  --> If we use Type="LoadBalancer"
```
4) Test in browser:
```
    i) minikube service hello-world-service (Automatically opens up in browser with http://127.0.0.1:61055)
    $ minikube service hello-world-service
        output:
        -------
        |-----------|---------------------|-------------|---------------------------|
        | NAMESPACE |        NAME         | TARGET PORT |            URL            |
        |-----------|---------------------|-------------|---------------------------|
        | default   | hello-world-service |          80 | http://192.168.49.2:30010 |
        |-----------|---------------------|-------------|---------------------------|
        * Starting tunnel for service hello-world-service.
        |-----------|---------------------|-------------|------------------------|
        | NAMESPACE |        NAME         | TARGET PORT |          URL           |
        |-----------|---------------------|-------------|------------------------|
        | default   | hello-world-service |             | http://127.0.0.1:62703 |
        |-----------|---------------------|-------------|------------------------|
        * Opening service default/hello-world-service in default browser...
        ! Because you are using a Docker driver on windows, the terminal needs to be open to run it.
        
       (OR)

    ii) $ minikube service hello-world-service --url
         output:
         -------
         http://127.0.0.1:62781
         ! Because you are using a Docker driver on windows, the terminal needs to be open to run it.     
```

## GOAL:2 Create Kubernetes deployment to deploy replicas and to maintain at the required number of pods and replicas.
```
 Steps:
  ------
  1) Delete the running hello-world pods and service
  2) Ensure minikube is running (Start minikube to start...)
  3) Create deployments
  4) Test in browser
  5) Research pods/services/deployments in "minkube" dashboard -> $ minikube dashboard
```

1) Delete the running hello-world pods and service:
```
    $ kubectl delete pod hello-world-app-pod
    $ kubectl delete svc hello-world-service
```   

2) Ensure minikube is running (Start minikube to start...):
```
    $ minikube --help
```

3) Create deployments:
```
    $ kubectl create -f hello-world-app-service.yaml      (We need service as well)
    $ kubectl create -f hello-world-app-deploy.yaml

    -- Verify new deployments and service
    $ kubectl get deployments
      NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
      hello-world-app-deploy   2/2     2            2           87s

    $ kubectl get all
      NAME                                          READY   STATUS    RESTARTS       AGE
      pod/hello-world-app-deploy-67d4c558fb-c8lk5   1/1     Running   0              112s
      pod/hello-world-app-deploy-67d4c558fb-f7bb4   1/1     Running   0              112s
      :::
      NAME                          TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      service/hello-world-service   LoadBalancer   10.108.243.209   <pending>     80:30010/TCP   7s
      :::
      NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
      deployment.apps/hello-world-app-deploy   2/2     2            2           112s
      :::
      NAME                                                DESIRED   CURRENT   READY   AGE
      replicaset.apps/hello-world-app-deploy-67d4c558fb   2         2         2       112s
      :::
```
4) Test in browser:
```
    (i) $ minikube service hello-world-service
        output:
        -------    
        Opens in browser: http://127.0.0.1:63197/
        
        Browser output:
        ---------------
        {
            "localHostName" : "hello-world-app-deploy-67d4c558fb-c8lk5",
            "success" : "true",
            "APP_NAME" : "HELLO",
            "localHostAddress" : "172.17.0.16",
            "hello" : "world",
            "remoteHostAddress" : "127.0.0.1",
            "remoteHostName" : "localhost"
        }
        
        (OR)
        
    (ii) $ minikube service hello-world-service --url
        output:
        -------
        http://127.0.0.1:63239
        ! Because you are using a Docker driver on windows, the terminal needs to be open to run it.
         
        Browser output:
        ---------------
        {
            "localHostName" : "hello-world-app-deploy-67d4c558fb-f7bb4",
            "success" : "true",
            "APP_NAME" : "HELLO",
            "localHostAddress" : "172.17.0.13",
            "hello" : "world",
            "remoteHostAddress" : "127.0.0.1",
            "remoteHostName" : "localhost"`
         }
```
5) View all
```
        $ kubectl get all
        NAME                                          READY   STATUS    RESTARTS   AGE
        pod/hello-world-app-deploy-67d4c558fb-c8lk5   1/1     Running   0          15m
        pod/hello-world-app-deploy-67d4c558fb-f7bb4   1/1     Running   0          15m

        NAME                          TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
        service/hello-world-service   LoadBalancer   10.108.243.209   <pending>     80:30010/TCP   10m
        service/kubernetes            ClusterIP      10.96.0.1        <none>        443/TCP        5d3h

        NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/hello-world-app-deploy   2/2     2            2           15m

        NAME                                                DESIRED   CURRENT   READY   AGE
        replicaset.apps/hello-world-app-deploy-67d4c558fb   2         2         2       15m
```