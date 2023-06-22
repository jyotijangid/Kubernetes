- Static Provisioning Volume: Before creating a PV in K8s we manually have to provision the disk on Cloud platform (like EMS on AWS) & then create PV with the same name.
- Dynamic Provisioning Volume: With Storage classes we can define a provisional such as Google Storage that can automatically provision on Google cloud and attach that to PODs when a claim is made. We don't need PV definition anymore because PVC will be created automatically when the storage class is created. We add `storageClassName` in pvc.yaml. Storage class uses the default provisioner to provision the required size on gcp/aws/azure. It still creates a PV but as user we don't have to write & create pv by ourselves `pv.yaml`.
- Storage Classes: We can create different storage class each using different type of disks. Silver storage class (standard disk), a gold storage class (ssd), platium storage class (ssd + replication).
```
storage-class.yaml

    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
        name: google-storage
    provisioner: kubernetes.io/gce-pd
    parameters:
        type: pd-standard | pd-ssd
        replication-type: none | regional-pd
```
- now we don't need PV definition anymore as the associated storage is going to be created automatically when the storage class is created (PV is created but we manually don't have to write pv-definition anymore )
- look for `no-provisoner` in PROVISIONER for storage class that does not support dynamic-provsioning;
pvc-definition:
```
    spec:
        accessMode:
            - ReadWriteOnce
        storageClassName: google-storage
```