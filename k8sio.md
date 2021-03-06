# Kubernetes

K8s automates the distribution and provisioning of applications containers across a cluster in a more efficient way.


1. K8s cluster and nodes:
    Coordinates a high availability cluster of computers connected to work as a single unit.
    Allow to deploy containerized applications without tying them to individual machines.


    a K8s clusters =  2 types of resources:
    - A master node that coordinates the cluster: 3 process that run on a single node that can be replicated
    - Nodes = workers that run the applications

    Each node has a 
        - Kubelet agent for managing the node and communicating with the master, 
        - kube proxy, a network proxy which reflects k8s networking on each node.
    Nodes also have tools for handling containers operations. (docker).
    A prod cluster should have a minimum of 3 nodes.

    When you deploy an app on k8s, you tell the master to start the application containers (docker run ...)

    Minikube is a lightweight k8s implementation that starts a VM on your machine and deploys a simple cluster on only one node.

    A node is a worker machine (either virtual or physical). Each node is managed by the master. A node can have multiple pods. The master automatically handle scheduling pods across nodes in the cluster.


2. k8s Pods:

    A pod is a group of one or more containers tied together for administration and networking.  A K8s deployment checks on the health of your pod and
    restarts the container if they fails. Deployments are the way to scale or manage the creation of pods.

    kubctl create deployment hello-node --image=docker.io/gbelbe/nodejsk8s:1

    note: si ça ne marche pas, tester la commande docker pull nom_de_limage, pour savoir si tout se passe bien
            kubectl get xxx  (nodes / pods / deployments...)

    Pods inside a kubernetes cluster are under a private isolated network. By default they are visible from other pods and services
    within the same cluster. but not outside that network. When we use Kubectl we interact through an API endpoint with the master node.

    **kubectl proxy** can create a proxy that will forward communication into the cluster private network from our terminal.

A pod models an application specific "logical host" and can contain different application containers that are tightly coupled.
> ex: nodejs app in a container, and another container with the data used by the app.

A ReplicaSet ensures that a specified number of pod replicas are running at any given time. However, a Deployment is a higher-level concept that manages ReplicaSets and provides declarative updates to Pods along with a lot of other useful features. This actually means that you may never need to manipulate ReplicaSet objects: use a Deployment instead, and define your application in the spec section

    pod lifecycle (status):
        pending: pod accepted by k8s, but one or more container image not yet created.
    liveliness: is the container running?
    readiness: it the container ready to service requests?
    


3. K8s Deployments:  A deployment provides declarative update for Pods and Replicas

    Once we have a K8s cluster ready, we can deploy a containered application on top of it, through a **Deployment configuration**
    The deployments instructs K8s how to create and update instances on your app. K8s master node schedule app instances on individual nodes.
    After app instances are created, a **k8 deployment controller ** monitor those instances. 
    Self-healing mechanism to address machine failure or maintenance.
 
    To create a deployment, we need a container image and a number of replicas, we can modify that by changing the deployment. (scale up / update)

    We declare a "desired state" in a Deployment object, and the deployment controller changes the actual state to the desired state.
    We can define a deployment to define replicaSets of pods where the app will be deployed.
    Deployment ==> update a software, scale up the deployment, rollback to an earlier deployment version

    When an update of the deployment (version change) has been made, kubectl get rs (shows the status of the 2 replica sets, the old one and the new one)
    
    we can scale a deployment ==> kubectl scale deployment.namedepl --replicas=10 



4. Kubernetes Services:

A service in K8s is a logical set of pods and a policy by which to access them. Services enable a loose coupling between dependant pods.
Altough each pod has an IP address, these IP address are not exposed outside the cluster without a Service. Service allow your app to receive traffic.
Discovery and routing among dependent Pods (such as the frontend and backend components in an application) is handled by Kubernetes Services.
Services match a set of pods using labels and selectors


5. Ingress: 

Exposes HTTP routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules on the ingress resource.
Use ingress to give service external URLs, to load balance traffic, 


5. Namespaces:

Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespace/
Namespaces are a way to divide cluster resoureces between multiple users with quotas. Objects within the same namespace will have the same access control.

To separate slightly different resources, you can use "labels" instead of namespace (for ex. different versions of the same software)

Namespaces are used to categorize, filter and manage arbitrary group of objects within a cluster. Each object in a K8s cluster must be assigned to only one namespace.

Across different namespaces, you can have the same names for objects: this can be useful for managing different environments (tests / pre / prod)

Namespaces makes it easy to apply policies (quota / security...) to specific slices of your cluster. 
Use cases:
    1. Mapping namespaces to teams or projects (we can use role binding for security purposes)
    2. Using namespaces to partition Life Cycle environments (dev / staging / prod) within the cluster. 
        While it is better to separate Prod environment, it can be a useful solution for smaller projects. 
        (network policy, role based access control, and quotas are useful for this case).
    3. Isolate different consumers: we can separate workloads to different consumers, 
        we can keep track of usage of the same cluster separately for     different customer (for billing purposes)

In case the offer is generic, namespaces allow to develop and deploy a different instance of the same templated environment.
The consistency can make the management much easier.


kubectl get namespaces (default: default, kube-public, kube-system)

the ability to reuse the same name is better, as objects can be rolled up to different environments while they are being tested and released.


About namespaces: https://rancher.com/blog/2019/2019-01-28-introduction-to-kubernetes-namespaces/









