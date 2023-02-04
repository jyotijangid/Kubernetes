- Admin creates PVs & users creates PVCs to make the storage available to a node.
- When PVC is created the K8s binds the PV to Claims based on request & properties set on the volume. 1-to-1 relationship (PVC-PV) even if space left on PV it cannot be utilized for another PVC.
- PVC is assigned to a PV by request properties like Sufficient Capacity, Access Modes, Volume Modes, Storage Class, Selector. Also, labels & slectors can be used to bind PVC to PV. 
- If there are multiple possible matches for a single claim, we can use labels & selectors on pvc to bind to a particlar persistant volume. 

```
pvc-definition.yaml

apiVersion: v1
kind: PersistantVolumeClaim
metadata:
    name: myclaim
spec:
    accessMode:
        - ReadWriteOnce
    resources:
        requests: 
            storage: 500Mi

>>> kubectl create -f pvc-definition.yaml
- After this K8s looks for PVs & mataches the requested properties
>>> kubectl get persistantvolumeclaim
>>> kubectl delete pvc myclaim
```
- we can chose what happens with the PV when PVC deleted (persistantVolumereclaimPolicy: Retain|Deleted|Recycle) by default the PV doesn't get deleted by also it is also not available for other PVC to reuse. Recycle: data in data volume will be scrubbed before making the PV available to other PVC to reuse.
```
pod-yaml
    spec:
        containers:
        - name:
          image:
          volumeMounts:
          - mountpath: /var/www/html
            name: mypd
        volumes:
        - name: mypd
          persistantVolumeClaim:
            claimName: myclaim
```