# Wordpress Deployment

Hi! shadow this side today we are going to deploy wordpress with just only one  **command**. I am going to navigate you through this whole project and at the end i will provide you with master deployment where you can just simply use one command and your wordpress will be deployed
**So buckle up and let's go**

# Wordpress

WordPress is a versatile and user-friendly platform for creating websites and blogs. It provides a simple interface for managing content, themes, and plugins, making it accessible even for those without technical expertise. Users can customize their sites with various themes and extend functionality through plugins, which add features like e-commerce, SEO optimization, and social media integration. WordPress is widely used due to its open-source nature, robust community support, and continuous updates, making it suitable for everyone from personal bloggers to large businesses aiming to establish an online presence efficiently and effectively.

## Content

- [wordpress-pvc.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-pvc.yaml "wordpress-pvc.yaml")
- [wordpress-pv.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-pv.yaml "wordpress-pv.yaml")
- [mysql-deployment.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/mysql-deployment.yaml "mysql-deployment.yaml")
- [mysql-service.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/mysql-service.yaml "mysql-service.yaml")
- [wordpress-deployment.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-deployment.yaml "wordpress-deployment.yaml")
- [wordpress-service.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-service.yaml "wordpress-service.yaml")
- [main-deployment.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/main-deployment.yaml "main-deployment.yaml")

## What are all these files ?

**Ok so i will make it easy for you** basically these all files are k8s manifest or deployment files which we are going to use to deploy our wordpress where we will we creating services, persistent volumes and deployments. So let me explain you all these file in detail so that you can understand everything more clearly.

# Files :- 

## Wordpress-pvc.yaml

Before digging into this file first lets understand what is Persistent volume.

**Persistent Volume :-**
Persistent Volumes provide a way to manage and consume storage resources in a Kubernetes cluster efficiently, ensuring data persistence and reliability for stateful applications such as databases or file storage systems.

I hope you understant now lets get back to the file:- 
**File :-** 
[wordpress-pv.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-pv.yaml "wordpress-pv.yaml")
### Explanation of Fields:

-   **apiVersion**: Specifies the Kubernetes API version (`v1` in this case).
-   **kind**: Defines the type of Kubernetes resource (`PersistentVolume`).
-   **metadata**: Contains metadata about the resource, including its name (`wordpress-pv`).
-   **spec**: Specifies the details of the PersistentVolume.
    -   **capacity**: Defines the storage capacity (`2Gi` for 2 gigabytes).
    -   **accessModes**: Specifies the access modes the volume supports (`ReadWriteOnce` allows read-write access by a single node).
    -   **persistentVolumeReclaimPolicy**: Defines what happens to the volume when released (`Retain` means the volume is not deleted automatically, allowing manual reclamation).
    -   **storageClassName**: Associates the PersistentVolume with a specific StorageClass (`manual` in this case).
    -   **hostPath**: Specifies a path on the host node's filesystem (`/data`), where Kubernetes will create the volume
    
This configuration provides a basic setup for a PersistentVolume in Kubernetes, ensuring that a 2GB volume with specified access and reclaim policies is available for use by pods within the cluster.

>**Command to deploy pv** you are **not** going to use it but still for your knowledge :- 
`kubectl apply -f wordpress-pv.yaml`
This will create pv and you can check that pv by command :-
`kubectl get pv	`
   
## Wordpress-pvc.yaml

I hope you understood pv now lets understand what pvc is doing :- 
**File :-**
[wordpress-pvc.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-pvc.yaml "wordpress-pvc.yaml")

### Explanation of Fields:

-   **apiVersion**: Specifies the Kubernetes API version (`v1` in this case).
-   **kind**: Defines the type of Kubernetes resource (`PersistentVolumeClaim`).
-   **metadata**: Contains metadata about the resource, including its name (`wordpress-pvc`).
-   **spec**: Specifies the details of the PersistentVolumeClaim.
    -   **accessModes**: Specifies the access modes the PVC requires (`ReadWriteOnce` allows read-write access by a single node).
    -   **resources**: Specifies the resource requirements for the PVC.
        -   **requests**: Specifies the requested resources (`storage: 2Gi` requests 2 gigabytes of storage).
    -   **storageClassName**: Associates the PVC with a specific StorageClass (`manual` in this case).

This PVC definition (`wordpress-pvc`) sets up a request for a 2GB PersistentVolume with `ReadWriteOnce` access mode, expecting manual provisioning by an administrator. It provides the necessary configuration for Kubernetes to manage and bind the PersistentVolume to pods requiring persistent storage, such as a WordPress deployment.

>**Command to deploy pvc** you are **not** going to use it but still for your knowledge :- 
`kubectl apply -f wordpress-pvc.yaml`
This will create pvc and you can check that pv by command :-
`kubectl get pv	`

## Mysql-deployment.yaml

  Now these are some important files going to deploy mysql with some of its enviorenment variable.
 
 **File :-**
 [mysql-deployment.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/mysql-deployment.yaml "mysql-deployment.yaml")
 ### Explanation of Fields:

-   **apiVersion**: Specifies the Kubernetes API version (`apps/v1` for Deployments).
-   **kind**: Defines the type of Kubernetes resource (`Deployment`).
-   **metadata**: Contains metadata about the resource, including its name (`mysql`) and labels (`app: mysql`).
-   **spec**: Specifies the details of the Deployment.
    -   **replicas**: Specifies the number of replicas (instances) of the MySQL pod (`1` replica).
    -   **selector**: Defines how the Deployment finds which Pods to manage based on labels (`matchLabels: app: mysql`).
    -   **template**: Specifies the pod template used to create new Pods.
        -   **metadata**: Labels applied to Pods created from this template (`app: mysql`).
        -   **spec**: Specifies the specification of the pod.
            -   **containers**: Defines the containers within the pod.
                -   **name**: Name of the container (`mysql`).
                -   **image**: Docker image used for the container (`mysql:5` for MySQL version 5).
                -   **env**: Environment variables set for the container (`MYSQL_ROOT_PASSWORD` set to `akash1234`).
                -   **ports**: Ports exposed by the container (`3306` for MySQL).
                -   **volumeMounts**: Mounts persistent storage into the container.
                    -   **name**: Name of the volume (`mysql-persistent-storage`).
                    -   **mountPath**: Path within the container where the volume is mounted (`/var/lib/mysql`).
            -   **volumes**: Defines the volumes available to the pod.
                -   **name**: Name of the volume (`mysql-persistent-storage`).
                -   **persistentVolumeClaim**: Specifies that the volume is backed by a PersistentVolumeClaim (PVC).
                    -   **claimName**: Name of the PVC (`wordpress-pvc`), which must be pre-existing and properly configured to bind to a PersistentVolume.

>Well that's alot going on in this file but I hope you understand.

This Deployment configuration (`mysql`) sets up a MySQL database instance using Kubernetes, ensuring data persistence with a PersistentVolumeClaim (`wordpress-pvc`). It provides essential configuration for running MySQL in a Kubernetes environment, suitable for development or production use cases requiring reliable database storage.

>**Command to deploy mysql** you are **not** going to use it but still for your knowledge :- 
`kubectl apply -f mysql-deployment.yaml`
This will create pvc and you can check that pv by command :-
`kubectl get deployments	`

## Mysql-service.yaml

This file is used to expose the mysql deployment by giving it port.

**File :-**
[mysql-service.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/mysql-service.yaml "mysql-service.yaml")
### Explanation of Fields:

-   **apiVersion**: Specifies the Kubernetes API version (`v1` for core/v1).
-   **kind**: Defines the type of Kubernetes resource (`Service`).
-   **metadata**: Contains metadata about the resource, including its name (`mysql`).
-   **spec**: Specifies the details of the Service.
    -   **selector**: Specifies a set of labels used to identify the Pods that the Service should apply to (`app: mysql` selects Pods with the label `app` set to `mysql`).
    -   **ports**: List of ports that the Service exposes.
        -   **protocol**: Specifies the protocol used (`TCP`).
        -   **port**: Port number on the Service itself (`3306`).
        -   **targetPort**: Port on the Pods to which the traffic will be forwarded (`3306` in this case, which matches the MySQL database port).
   
This Kubernetes Service (`mysql`) sets up networking for accessing the MySQL database deployment (`Deployment` resource) within the Kubernetes cluster. By defining this Service, other applications or services within the cluster can communicate with MySQL using the service name (`mysql`) and the specified port (`3306`). This setup abstracts the internal details of Pod IP addresses and allows for dynamic service discovery within the Kubernetes environment.
>**Command to deploy mysql-service** you are **not** going to use it but still for your knowledge :- 
`kubectl apply -f mysql-services.yaml`
This will create pvc and you can check that pv by command :-
`kubectl get svc	`

## Wordpress-deployment.yaml

This is another important file which is going to deploy the wordpress file in the kubernetes with mysql env in it.

**File :-**
[wordpress-deployment.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-deployment.yaml "wordpress-deployment.yaml")
### Explanation of Fields:

-   **apiVersion**: Specifies the Kubernetes API version (`apps/v1` for Deployments).
-   **kind**: Defines the type of Kubernetes resource (`Deployment`).
-   **metadata**: Contains metadata about the resource, including its name (`wordpress`) and labels (`app: wordpress`).
-   **spec**: Specifies the details of the Deployment.
    -   **replicas**: Specifies the number of replicas (instances) of the WordPress pod (`1` replica).
    -   **selector**: Defines how the Deployment finds which Pods to manage based on labels (`matchLabels: app: wordpress`).
    -   **template**: Specifies the pod template used to create new Pods.
        -   **metadata**: Labels applied to Pods created from this template (`app: wordpress`).
        -   **spec**: Specifies the specification of the pod.
            -   **containers**: Defines the containers within the pod.
                -   **name**: Name of the container (`wordpress`).
                -   **image**: Docker image used for the container (`wordpress:5.2` for WordPress version 5.2).
                -   **env**: Environment variables set for the container.
                    -   **WORDPRESS_DB_HOST**: Specifies the MySQL host (`mysql:3306`), where `mysql` is the service name and `3306` is the port.
                    -   **WORDPRESS_DB_USER**: Specifies the MySQL user (`root` in this case).
                    -   **WORDPRESS_DB_PASSWORD**: Specifies the MySQL password (`akash1234` in this case).
                -   **ports**: Ports exposed by the container.
                    -   **containerPort**: Port on which WordPress listens (`80`).
                    -   **name**: Name of the port (`wordpress`).
                -   **volumeMounts**: Mounts persistent storage into the container.
                    -   **name**: Name of the volume (`wordpress-persistent-storage`).
                    -   **mountPath**: Path within the container where the volume is mounted (`/var/www/html`).
            -   **volumes**: Defines the volumes available to the pod.
                -   **name**: Name of the volume (`wordpress-persistent-storage`).
                -   **persistentVolumeClaim**: Specifies that the volume is backed by a PersistentVolumeClaim (PVC).
                    -   **claimName**: Name of the PVC (`wordpress-pvc`), which must be pre-existing and properly configured to bind to a PersistentVolume.
>Again load of explanation.

This Deployment configuration (`wordpress`) sets up a WordPress instance connected to a MySQL database within a Kubernetes cluster. By defining this Deployment, Kubernetes manages the lifecycle of WordPress pods, ensuring high availability and scalability. The PersistentVolumeClaim (`wordpress-pvc`) ensures that WordPress data persists across pod restarts or rescheduling, providing a reliable storage solution for web content and configurations.
>**Command to deploy wordpress-deployment** you are **not** going to use it but still for your knowledge :- 
`kubectl apply -f wordpress-deployment.yaml`
This will create pvc and you can check that pv by command :-
`kubectl get deployment	`

## Wordpress-service.yaml

This file is used to expose the mysql deployment by giving it port.

**File :-**
[wordpress-service.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/wordpress-service.yaml "wordpress-service.yaml")
### Explanation of Fields:

-   **apiVersion**: Specifies the Kubernetes API version (`v1` for core/v1).
    
-   **kind**: Defines the type of Kubernetes resource (`Service`).
    
-   **metadata**: Contains metadata about the resource, including its name (`wordpress`).
    
-   **spec**: Specifies the details of the Service.
    
    -   **selector**: Specifies a set of labels used to identify the Pods that the Service should apply to (`app: wordpress` selects Pods with the label `app` set to `wordpress`).
    -   **ports**: List of ports that the Service exposes.
        -   **protocol**: Specifies the protocol used (`TCP`).
        -   **port**: Port number on the Service itself (`80`).
        -   **targetPort**: Port on the Pods to which the traffic will be forwarded (`80` in this case, which is typically the port where WordPress is listening).
-   **type**: Specifies the type of Service. In this case, it's set to `LoadBalancer`, which instructs Kubernetes to provision a load balancer in the cloud provider's environment (if supported) and expose the Service externally

This Kubernetes Service (`wordpress`) sets up networking for accessing the WordPress Deployment (`Deployment` resource) externally. By defining this Service with type `LoadBalancer`, Kubernetes ensures that WordPress pods are accessible from outside the cluster via a dynamically provisioned load balancer, facilitating external access to the WordPress application.

>**Command to deploy wordpress-service** you are **not** going to use it but still for your knowledge :- 
`kubectl apply -f wordpress-services.yaml`
This will create pvc and you can check that pv by command :-
`kubectl get svc	`


## Main-deployment.yaml

Now this the main file that we are going to use for deploying our project.

**File :-**
[main-deployment.yaml](https://github.com/shadowsince1999/Wordpress/blob/main/main-deployment.yaml "main-deployment.yaml")

Now here this is 123 lines of code written by yours only `shadow` to make your works easy. Here now you dont have to go all these previous files step by step na now you just have to follow the steps that i am going to explain you next and by using **One Command** you are going to deploy your project . can you belive it `belive me`

## Steps to deploy this project  :-

As this is a git repo so first we need to clone this repo in our local machine in which kubernetes is installed so lets go :- 

**First step :-**

- Click on green color button name as **code** in right corner in my repo now select one of `http` or `ssh` and clone in your machine by command
`git clone <url>`

**Second step :-**

- Now check that all the stuff is proiperly clonned.
- Now find the file name `mail-deployment.yaml` and just deployment it simply by command
`kubectl apply -f main-deployment.yaml`
- Nothing more your wordpress in deployed now just search for minikube ip and use it with the port you will get in the wordpress svc by commands :-
`minikube ip`
`kubectl get svc`

**As i told you you are joust ghoing to use one command.** 


# Summary

This project is to provide us with one master file. By just deploying that file our project should be deployed with just one command . This project help us to save time and deploy wordpress along with persistance volumes.
