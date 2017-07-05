# Cyan Compute Deployed in Kubernetes Cluster
This article presents how the **Cyan compute** components are deployed into Bluemix Kubernetes Cluster. Each broker and web app components have a dockerfile to containerize them.

## Value proposition for container
Just to recall the value of using container for the cognitive application are the following:
* Docker ensures consistent environments from development to production. Docker containers are configured to maintain all configurations and dependencies internally.
* Docker containers allows you to commit changes to your Docker images and version control them. It is very easy to rollback to a previous version of your Docker image. This whole process can be tested in a few minutes.
* Docker is fast, allowing you to quickly make replications and achieve redundancy.
* Isolation: Docker makes sure each container has its own resources that are isolated from other containers
* Removing an app/ container is easy and won’t leave any temporary or configuration files on your host OS.
* Docker ensures that applications that are running on containers are completely segregated and isolated from each other, granting you complete control over traffic flow and management

## Value proposition for Kubernetes on Bluemix
* high availability 24/7
* Deploy new version multiple times a day
* Standard use of container for apps and business services
* Allocates  resources and tools when applications need them to work
* Single-tenant Kubernetes clusters with compute, network and storage infrastructure isolation
* Automatic scaling of apps
* Use the cluster dashboard to quickly see and manage the health of your cluster, worker nodes, and container deployments.
* Automatic re-creation of containers in case of failures

## Pre-requisites
You need a set of tools before using Kubernetes cluster on Bluemix, but we are reusing the script from Blue Compute, so run `./install_cli.sh` (for windows use the `install_cli.bat`). This script should install for you bluemix command line interface, bluemix container service plugin, Bluemix Container Registry Service, Kubernetes CLI (kubectl), Helm CLI (helm) and yaml.


## Configure the clusters
There are multiple model for Kubernetes cluster, for demonstration purpose the lite model is used, but for production the paid model is mandatory as it brings a lot of useful features for high availability, hostname, load balancing...

Clusters are specific to an account and an organization, but are independent from a Bluemix space.

The following diagram illustrates the **cyan compute** cluster configuration:
![](cyan-kube.png)  
Nodes or Worker nodes are virtual or physical server, managed by the Kubernetes master, and hosting containerized applications. An app in production runs replicas of the app across multiple worker nodes to provide higher availability for your app. A node has a public IP address.
Every containerized app is deployed, run, and managed by a pod.

Using Bluemix service and CLI we can create cluster. The following steps were done to create environment:
* bx cs cluster-create --name cyancomputecluster
* bx cs workers cyancomputecluster

Then specific to each application.

### Some useful commands

```
$ bx login -a api.ng.bluemix.net
# Review the locations that are available.
$ bx cs locations
# Choose a location and review the machine type
$ bx cs machine-types dal10
# Assess if a public and private VLAN already exists in the Bluemix Infrastructure  NEED a paid account
$ bx cs vlans dal10
# When the provisioning of your cluster is completed, the status of your cluster changes to deployed
$ bx cs clusters
# Check the status of the worker nodes
$ bx cs workers cyancomputecluster
```

When a container is deployed the following kubeclt commands are used:
```
$ kubectl get pods

$ kubectl describe pods

$ export POD_NAME=$(kubectl get pods -o go-template='{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}’)

$ kubectl exec $POD_NAME env

$ kubectl logs $POD_NAME

# run an alpine shell connected to the container
$ kubectl exec -ti $POD_NAME /bin/ash
> ls

$ kubectl get services

$ export NODE_PORT=$(kubectl get services/casewdsbroker -o go-template='{{(index .spec.ports 0).nodePort}}')

$ kubectl describe deployment
```