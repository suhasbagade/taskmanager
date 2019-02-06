<h1>TaskManager</h1>

## Build Status

[![CircleCI](https://circleci.com/gh/suhasbagade/taskmanager/tree/build_with_circleci.svg?style=svg)](https://circleci.com/gh/suhasbagade/taskmanager/tree/build_with_circleci)

Circleci config file is available [here](https://github.com/suhasbagade/taskmanager/blob/master/.circleci/config.yml)

  - Config file contains the necessary configuration to build and archive artifacts(jar files) generated after building web and scheduler modules.
  - CI build job will be triggered upon every commit to the branch.
  
  
## Dockerization


#### Objective : 

To dockerise taskmanager application (endpoints & scheduler modules)

#### Pre-requisites & Installations:

* Docker
    Installation procedure for docker can be found below.
    - [Windows](https://docs.docker.com/docker-for-windows/install)
    - [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)
    - [MacOS](https://docs.docker.com/docker-for-mac/install/)
* Dockerhub account [click here](https://hub.docker.com/)

#### Steps to generate docker image and push it to DockerHub:

1)  Navigate to the directory where codebase has been checked out.(Branch to use is <b>build_with_circleci</b>)
2)  Locate the Dockerfile for particular module and run below command. Dockerfiles - [endpoints](https://github.com/suhasbagade/taskmanager/blob/master/endpoints/Dockerfile) & [scheduler](https://github.com/suhasbagade/taskmanager/blob/master/scheduler/Dockerfile) 
      ```
      docker build .
      ```
      A docker images will be created with a unique id and can be listed by running 
      ``` 
      docker images 
      ```
3)   Tag the image and push it to dockerhub.
      ```
      docker tag <Image_ID> <Dockerhub_User_ID>/<Repository_Name>:<Version>
      
      Ex : docker tag <image_id> suhasedunet/endpoints:v1.0
      ```

## Deployment to Kubernetes cluster

#### Objective : 

  To install minikube and bring up a single node cluster.
  Deploy postgresql database, endpoints & scheduler applications. 

#### Pre-requisite & Installations :

* [Hypervisor (virtualbox)](https://www.virtualbox.org/wiki/Linux_Downloads)
* [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl)
* [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/#install-minikube)
* [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)

#### Steps to bring up minikube cluster and deploy postgresql database, endpoints & scheduler applications:

1)  Run below command to start minikube which inturn brings up the kubernetes cluster.
    ```
    minikube start
    ```
2)  Deploy postgresql to kubernetes cluster.
    ```
    kubectl create -f postgresql-deployment.yml
    kubectl create -f postgresql-service.yml
    ```
3)  Deploy Endpoints & Scheduler to kubernetes cluster.
    ```
    kubectl create -f endpoints-deployment.yml
    kubectl create -f endpoints-service.yml
    ```
    Wait 5mins for endpoints container to come up.
    ```
    kubectl create -f scheduler-deployment.yml
    kubectl create -f scheduler-service.yml
    ```
    Application can be accesed only, within the cluster as it is exposed as clusterIP service

4)  Deploy Nginx Ingress Controller.
    ```
    kubectl create -f configmap.yml
    kubectl create -f service_account.yml
    kubectl create -f default-http-backend_deployment.yml
    kubectl create -f default-http-backend_service.yml
    kubectl create -f nginx-ingress-controller_deployment.yml
    kubectl create -f nginx-ingress-network-policy.yml
    kubectl create -f ingress-nginx.yml
    ```
5)  Apply Ingress for Endpoints.
    ```
    kubectl create -f endpoints-ingress.yml
    ```
6)  Smoke Test.
    ```
    Access the URL - https://<IP Address>/ which redirects to the taskmanager webpage.
    ```
    where, IP Address refers to External Load Balancer IP in cloud platform and Node IP in Minikube.
     
