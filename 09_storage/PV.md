attach Volume to POD just like we attach Vol to Containers in Docker.

VOLUMES:
```
    Pod.yaml
    
    spec:
        containers: 
            - image:
              name:
              command:
              args: 
              volumeMounts:
                - mountPath: path of container to be mounted
                  name: name of volume to be mounted on (Vol-1)
        volumes:
        - name: Vol-1
          hostPath: 
            path: path on host (path-on-node)
            type: Directory
``` 
- in single node cluster we can use this hostPath but on multi-node cluster we are not sure the dirctory-on-node is same or have the same data until configured externaly to be same
- K8s supports different types of standard storage solutions such as NFS, GlusterFS, EBS, Azure Disk, Google's Persistant Disk
```
        volumes:
        - name: Vol-1
          awsElasticBlockStore:
            volumeID:
            fsType: ext4
        - name: mypvc
          persistantVolumeClaim:
            claimName: myclaim
```
PERSISTANT VOLUMES: (Object in K8s)
- in a large cluster with a lot of pods, every pod configuration file needs to configure storage every time & everytime a change is made it must be made on all the configuration files.
- So, in order to manage storage more centrally like admin will create a large pool of storage & then have users carve out pieces from it as required.
- A Persistant Volume is a cluster wide pool of storage volumes configured by an adminstrator to be used by users deploying applications on the cluster.
- Using Persistant Volume Claims users can now select storage from this pool.  
```
persistant-volume.yaml

apiVersion: v1
kind: PersistantVolume
metadata:
    name: pv-vol1
spec: 
    accessModes:
        - ReadWriteOnce | ReadWriteMany | ReadOnlyMany
    capacity: 
        storage: 1Gi
    hostPath:                 ---                                        awsElasticBlockStore:
        path: /tmp/data       --- not to bes used in production env        volumeID:
                                                                           fsType: ext4
>>> kubectl create -f persistant-volume.yaml
>>> kubectl get persistantvolume
```