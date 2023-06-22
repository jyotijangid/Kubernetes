1. Local Machine (Laptop)
2. Virtual Machines

- LOCAL MACHINE
    ```
    1. LINUX: Set up a cluster locally using K8s linux binaries.
    2. Windows: Cannot setup K8s natively as there are no windows binaries. Below solutions can help us setup K8s on windows:
        - Using Virtaulization solftwares like Hyper-V, Vmware Workstation, VitualBox we can setup Linux VMs on which we can run K8s.
        - We can run K8s components as docker containers on Windows VMs. But even then the docker images are linux based and under the hoods they run on small linux OS created by Hyper-V for running Linux docker containers.

    Easily ways to run K8s locally:
    1. MINIKUBE: 
        - deploys a single node cluster.
        - it relies on one of the virtualization software like Oracle VirtualBox to create the VMs that run the K8s cluster components.
        - Deploys VMs.
    2. KUBEADM:
        - Used to deploy a single node or multi node cluster.
        - Provision the host with required configuration by yourself .
        - Require VMs to be ready.

    ```

##### PRODUCTION PURPOSE
(Both in private & public cloud environment)
```
1. TurnKey Solutions
    - you provision the required VMs and use some kind of tools or scripts to configure kubernetes cluster on them.
    - You Provision VMs
    - You Configure VMs
    - You Use Scripts to Deploy Cluster
    - You Maintain VMs yourself
    - Eg: Kubernetes on AWS using KOPS
    - e.g. Openshift, CloudFundary Container Runtime, VMware Cloud PKS, Vagrant

2. Hosted/Managed Solutions
    - Kubernetes-As-A-Service
    - Provider provisions VMs
    - Provider installs Kubernetes
    - Provider maintains VMs
    - Eg: Google Container Engine (GKE
```

##### TURNKEY SOLUTIONS: 
```
DEPLOY & MANAGE K8s Clusters privately within your Oraganization using: 
    1. OPENSHIFT:
        - Openshift is an open source container application platform and is build on top of K8s. 
        - It provides a set of additional tools and a nice GUI to create and manage K8s constructs and easily integrate with CI/CD pipelines etc.

    2. CLOUD FOUNDARY CONTAINER RUNTIME:
        - It is an open-source project from Cloud-Foundary that helps in deploying and managing hight available K8s clusters using their open-source tool called BOSH.

    3. VMWARE CLOUD PKS:
        - If you wish to leverage your existing VMware environment for K8s.

    4. VAGRANT:
        - It provides a set of useful scripts to deploy a K8s Cluster on different cloud service providers.
```

##### HOSTED SOLUTIONS:
```
1. GKE: Google Container Engine on  GCP
2. Openshift Online is an offering from RedHat (you'll get access to fully functional K8s cluster online).
3. AKS: Azure K8s Service
4. EKS: Amazon Elastic Container Service for K8s
```