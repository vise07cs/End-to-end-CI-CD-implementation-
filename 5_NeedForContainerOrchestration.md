# Docker vs Kubernetes

Docker --> solves the problem of: but  It works on my machine

jsut pull docker image and runs the app 


Then whats the need of K8S  ?

There are few issues with docker . 
1) --> containers are ephermaral in nature (short lived).
They can go down for several reasons 
 Ex: ui container is talking to cart container , but due to some reason the cart container went down and next time when the cart container starts again , the ip addess of the cart container changes 
Due to this the connection between the cart container and ui container fails.
This problem in technical terms is known as service discovery  

2) To run your app in production , you also need to have high availiblity , load balancing , integration with API gateways , distater recovery . Docker doesnt provide much of these services to that extent 

hence we need container orchestration services 

best one is K8S

# Docker compose vs Kubernetes



# Docker Compose
Docker compose is used to run multiple containers in a single step .

you define all the microservices configuration in a single yaml file (docker compose yaml file ) 

best for Development, testing, small-scale deployments

docker compose up 


# Kubernetes

K8S is container orchestration platform 
resolves the issue of service discovery , clustering , HA, LB

Designed for Large-scale container orchestration across cluster
Best for Production, high-availability, large-scale systems
Provides High fault tolerance and recovery capabilities



# Steps to onboard to Kubernetes

   create a local K8S cluster (minikube , kind ,k3d )   --> not used in real time prod,used for developemnt usecases

   spin up multiple ec2 and create K8S cluster there (kubeadm)  --> requires lot of management and cost optimization that you will have to do yourself 

   managed K8S cluster (EKS by AWS ) --> upgrades becomes very easy , control plane is managed by cloud providers,scaling becomes easy , ui is good , cost optimization is also not a very big challenege


In our project we will be using EKS , and we will provision it using Terraform (IAC)






