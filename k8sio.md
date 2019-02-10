# Kubernetes

K8s automates the distribution and provisioning of applications containers across a cluster in a more efficient way.


1. K8s cluster:
    Coordinates a high availability cluster of computers connected to work as a single unit.
    Allow to deploy containerized applications without tying them to individual machines.


    a K8s clusters =  2 types of resources:
    - A master node that coordinates the cluster: 3 process that run on a single node that can be replicated
    - Nodes = workers that run the applications

    Each node has a Kubelet, agent for managing the node and communicating with the master. 
    Nodes also have tools for handling containers operations. (docker).
    A prod cluster should have a minimum of 3 nodes.

    When you deploy an app on k8s, you tell the master to start the application containers (docker run ...)

    Minikube is a lightweight k8s implementation that starts a VM on your machine and deploys a simple cluster on only one node.


2. K8s Deployments:

    Once we have a K8s cluster ready, we can deploy a containered application on top of it, through a **Deployment configuration**
    The deployments instructs K8s how to create and update instances on your app. K8s master node schedule app instances on individual nodes.
    After app instances are created, a **k8 deployment controller ** monitor those instances. 
    Self-healing mechanism to address machine failure or maintenance.
 
    To create a deployment, we need a container image and a number of replicas, we can modify that by changing the deployment. (scale up / update)

3. k8s Pods:

    A pod is a group of one or more containers tied together for administration and networking.  A K8s deployment checks on the health of your pod and
    restarts the container if they fails. Deployments are the way to scale or manage the creation of pods.

    kubctl create deployment hello-node --image=docker.io/gbelbe/nodejsk8s
        
    kubectl get xxx  (nodes / pods / deployments...)

