# ORCHESTRATING CONTAINERS ACROSS MULTIPLE VIRTUAL SERVERS WITH KUBERNETES. PART 1

This is one of the most challenging and interesting project in the curriculum. 

Broadly speaking, the project involves the following:

- Creating Public Key Infrastructure(PKI) for implementing TLS encryption for data on flight. This is a hard requirement for K8s
- Provisioning Ec2 instances for the master(3) and worker nodes(3)
- provisioning the Network infrastructure to power the Ec2 instances
- Configuring the instances: Installing the required software needed by the instances to perform their role.


## Architecture of K8s Cluster.

Our K8s cluster has 3 master nodes and 3 worker nodes.

The Master nodes has the following services running on each of them:

  - ETCD - Key value distributed store. Used to persist the state of the cluster.
  - Kube-Scheduler - Select the worker node to run containerized workload. 
  - kube-Controller-Manager - Ensures the desired state of the cluster is meet. 
  - Kube-Api-Server - This the brain of the cluster and serves as an entry point into the cluster. 


The Worker nodes has the following services running on each of them:

  - Kubelet - Its servers the agent and is in constant communication with kube-api-server
  - Kube-Proxy - Responsible for maintaing network rules withhin the node. Which allows communication between pods both within and outside the cluster
  - Container runtime - Is the Engine for running container work loads. In this project we used containerd




![Controlplane-coomponents](https://user-images.githubusercontent.com/52359007/175991616-299c556c-11c3-4e93-9a64-c4ffeeae7c8c.PNG)


![etcd-master-1](https://user-images.githubusercontent.com/52359007/175991797-dc558454-c829-4601-a4a5-e32d8d5339c8.PNG)

![etcd-master-2](https://user-images.githubusercontent.com/52359007/175991890-7b18838a-25c5-4777-b677-30d4d37e6433.PNG)

![master-0-etcd](https://user-images.githubusercontent.com/52359007/175992038-d1b6d2aa-94d9-479b-b347-dd0229fdc9e4.PNG)

![kubelet-and-kube-proxy-running](https://user-images.githubusercontent.com/52359007/175992125-bfa12bf9-4a0a-4b53-9d16-7ee562ebded9.PNG)

![worker-nodes-joining-cluster](https://user-images.githubusercontent.com/52359007/175992459-5e77dbde-6d5f-4e2d-9cd1-96695e16f426.PNG)

