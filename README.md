
# azure-aks-vagrant-ubuntu
![enter image description here](https://lh3.googleusercontent.com/NQuESf_ZpYfzj3orgLRtH4W4CXeWd_xnOecfJJSTKHztTgz8JiNg0wASLeoqLJRbVI3A72l1D6RErQ)

# About...

This setup is used to install ***prerequisites*** that are required to ***access/create/delete Azure Kubernetes Service***.

# Table of Contents

* [Prerequisites](#prerequisites)
* [Install Vagrant Box](#deploy)
* [Vagrant Box Details](#configuration)
* [Create AKS cluster](#create_cluster)
* [Access AKS cluster](#aks)
* [Delete AKS cluster](#delete_cluster)
* [Add-Ons provided](#addons)
* [Login Vagrant VM](#access)
* [Stop Vagrant VM](#stop)
* [Restart Vagrant VM](#restart)
* [Destroy Vagrant VM](#destroy)




<a id="prerequisites"></a>

# Prerequisites 
* Enable Virtualization(VT) in BIOS for Hyper-V 
* [Git](https://git-scm.com/downloads "Git") command line utility which is used to target an existing repository and create a **clone**, or copy of the target repository
* [Vagrant (Version >= 2.2.0)](https://www.vagrantup.com/downloads.html "Vagrant") is an open-source software product for building and maintaining portable virtual software development environments, e.g. for VirtualBox, Hyper-V, Docker containers, VMware, and AWS
* [Oracle VM VirtualBox (Version >= 6.0)](https://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html "Oracle Virtual Manger") lets you run multiple operating systems on Mac OS, Windows, Linux, or Oracle Solaris.
* Minimum laptop/desktop configuration  - 8GBRAM , 4CPU , support for VT-X



<a id="deploy"></a>

# Install Vagrant Box
 Default settings:`env.yaml`. These settings can be changed as per your requirements and the below are minimum requirements.
```yaml
VM:
  ip: 100.10.10.112
  cpus: 2
  memory: 2048
  vmname: azure-aks-ubuntu
  hostname: azure-aks-ubuntu.com
```
Open `windows (OR) bash` terminal from your `local windows` system

* `$ cd azure-aks-vagrant-ubuntu/provisioning` 
* `$ git clone https://github.com/SubhakarKotta/azure-aks-vagrant-ubuntu.git` 
* `$ vagrant up`


<a id="configuration"></a>

# Vagrant Box Details

Name|IP|OS|RAM|CPU|
|----|----|----|----|----|
azure-aks-ubuntu  |100.10.10.112|Ubuntu 18.04|2G|2|


<a id="create_cluster"></a>

# Create AKS Cluster

**Login to vagrant box from your local machine**
* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant ssh azure-aks-ubuntu`

**Provide Azure Credentials**
* `$ az login --username <YOUR_NAME> --password <YOUR_PASSWORD>`


**Commands to know Azure supported locations/sizes/versions**
* `$ az account list-locations` [To list supported locations to create AKS cluster]
* `$ az vm list-sizes -l <LOCATION_NAME>`[To list supported sizes]
* `$ az aks get-versions -l <LOCATION_NAME>`[To list supported kubernetes versions]
 
 **Create AKS Cluster**
 
* `$ az group create --name <YOUR_RESOURCE_GROUP_NAME>  --location <YOUR_LOCATION_NAME>`[To create Resource Group]
* `$ az aks create   -g <YOUR_RESOURCE_GROUP_NAME>  -n <YOUR_CLUSTER_NAME>   -s <YOUR_NODE_VM_SIZE>  -c <YOUR_NODE_COUNT>  -k <YOUR_KUBERNETES_VERSION> -l <YOUR_LOCATION_NAME>  --enable-addons monitoring`
* `$ kubectl get nodes` [To verify kubectl is connected to aks cluster]


**[Access Kubernetes Dashboard](#access_dashboard) by following the steps given in the link**


<a id="access_dashboard"></a>

# Access Kubernetes Dashboard

From ***local windows system terminal*** execute the below commands

* `$ ssh -L 8001:localhost:8001 vagrant@100.10.10.112` [***password : vagrant***]

* `$ kubectl create clusterrolebinding dashboard-admin -n kube-system --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard`

Use the below command to generate ***access token*** to login ***Dashboard***

* `$ kubectl get secret $(kubectl get serviceaccount default -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode`

Start proxy to access kubernetes dashboard
* `$ kubectl proxy`

***Click !***
[http://localhost:8001/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/#!/login](http://localhost:8001/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/#!/login)


<a id="aks"></a>

# Access AKS Cluster
**Login to vagrant box from your local machine**
* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant ssh azure-aks-ubuntu`

**Provide AKS Credentials**
* `$ az login --username <YOUR_NAME> --password <YOUR_PASSWORD>`

**Command to know list of AKS Clusters**
* `$ az aks list`

**Fetch/Update kubeconfig to your vagrant box**
* `$ az aks get-credentials --resource-group <YOUR_RESOURCE_GROUP_NAME> --name <YOUR_CLUSTER_NAME>`
* `$ kubectl get nodes` [To verify kubectl is connected to aks cluster]

**[Access Kubernetes Dashboard](#access_dashboard) by following the steps given in the link**

<a id="delete_cluster"></a>

# Delete AKS Cluster

**Login to vagrant box from your local machine**
* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant ssh azure-aks-ubuntu`

**Provide AKS Credentials**
* `$ az login --username <YOUR_NAME> --password <YOUR_PASSWORD>`

When the cluster is no longer needed, use the az group delete command to remove the ***resource group, container service, and all related resources***.
* `$ az  group  delete --name <YOUR_RESOURCE_GROUP_NAME> --yes --no-wait`


<a id="addons"></a>

# Add-Ons provided
* `kubectl`
* `az`
* `helm`

<a id="access"></a>

# Login Vagrant VM
**The Vagrant VM can be accessed in two ways from your local windows machine**

**vagrant ssh**
* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant ssh azure-aks-ubuntu`

**putty/mobaxterm**
* `100.10.10.112` [***vagrant/vagrant*** ]
	
          
<a id="stop"></a>

# Stop Vagrant VM
**Run the below command to stop the Vagrant Box from your local windows machine**

* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant halt`


<a id="restart"></a>

# Restart Vagrant VM
**Run the below command to restart the Vagrant Box if the vagrant box is stopped from your local windows machine**
* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant up`


<a id="destroy"></a>

# Destroy Vagrant VM
**Run the below command to delete the Vagrant Box from your local windows machine**
* `$ cd azure-aks-vagrant-ubuntu/provisioning`
* `$ vagrant destroy`
