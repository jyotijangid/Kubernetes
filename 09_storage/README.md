1. Persistant Volumnes
2. Persistant Volume Claims
3. Configure Applications with Persistant Volumnes
4. Access Modes for Volumnes
5. Kubernetes Storage Object

##### DOCKER STORAGE
1. STORAGE DRIVER: Helps manage storage on images and containers. If we want to persist storage we create volumes & mount them. Volumes are handled by Volume Drivers.
- Docker host: /var/lib/docker ----> folders like aufs, containers, images, volumes
- Docker follows layered architecture: reuses the layers (through cache) that were previously downloaded even from other docker images.
- When we run a container from image, a new writable layer (Container layer) is created on top of the read only Image Layer.
- ReadWrite layer --> store data created by container, log files by app, temprorary file etc. Deleted when container is destroyed. 
- COPY-ON-WRITE MECHANISM: Docker automatically creates a copy of the file in readwrite layer and all edits will be done in the file present in readwrite layer. Image remains the same untill rebuild again.
- We can persist data using persistant volumes: 
```
>>> docker volume create data_volume     ---- creates a folder under /var/lib/docker/volumes/data_volume
>>> docker run -v data_volume:/var/lib/mysql mysql
>>> docker run --mount type=bind,source=data_volumne,target=/var/lib/mysql mysql
```
- Storage drivers are responsible for all these operations like maintaining layered architecture, creating writable layer, moving files across layers to enable copy & write etc. e.g aufs, zfs, btrfs, device mapper, overlay, overlay2 (in short storage drivers help manage storage on images & containers)
- For Ubuntu default storage driver is AUFS. Docker will chose the best storage driver based on the OS.
- Two types of mounting 1. Volume Mount (we have a volume inside /var/lib/volume/some-vol that we mount with the container) 2. Bind Mount: It binds mounts a directory from any location on the docker host

2. VOLUME DRIVER
- in order to persist storage we create volumes which are handled by volumne driver plugin. Vol Drivers helps in create a volume. 
- Default Volume Driver Plugin is local.
- a. Local Volume Plugin - helps create volume on docker host and store its data under var/lib/docker/volume dir
- third party volume drivers are Azure File Storage, Digital Ocean BLOCK Storage, Google Compute, Persistant Disks, Flocker, NetApp, RexRay etc
```
>>> docker run -it --name mysql --volume-driver rexray/ebs --mount src=ebs-vol,target=/var/lib/mysql mysql
```
3. CONTAINER STORAGE INTERFACE (CSI)
- container runtimes: docker, rocket (rkt), cri-o
- Container Runtime Interface (CRI): is a standard that defines how an orchestration tool like K8s would communicate with container runtimes like Docker so, that if in case new container runtime is introduced they can just follow the standard without depending on K8s source code. 
- CNI - Container Networking Interface: To extend support for different networking solutions the CNI was introduced. (flannel, weaveworks, cilium)
- CSI: developed to support multiple storage solutions (CSI Drivers). e.g. portworx, Amazon EBS, Dell, PowerMax, Unity, NetApp, Pure Storage 
- CSI: universal standard for making any orchestration tool work with any storage vendor 
- currently K8s, Mesos, Cloud Foundry are on board with CSI
- CSI: defines some RPCs (Remote Procedure Calls) that are called by container orchestrator & must be implemented by storage drivers. 
- e.g. When POD is created & requires volume the container orchestrator tool like K8s should call create volume RPC & pass a set of details like name. The storage driver must implement this RPC & handle the request by provsioning a new volume on the storage array & return the results of the operation
- RPCs: CreateVolume, DeleteVolume, ControllerPublishVolume

![alt text](/images/storage.png)


###### Configure Applications with Persistant Volumnes
- configure volume in application using volumeMounts in containers.

pod.yaml
```
containers:  
    - image: 
      name: 
      command: 
      args:  
      volumeMounts: 
        - mountPath: path of container to be mounted 
          name: name of volume to be mounted on (Vol-1)
```