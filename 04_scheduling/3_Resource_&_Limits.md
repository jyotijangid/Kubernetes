Resources and Limits

```
CPU | Mem | Disk
 
- pod.definition.yaml 
        spec:
           containers:
            - name:
              resources: 
                requests:
                    cpu: 1
                    memory: "1Gi"
                limit:
                    cpu: 2
                    memory: "2Gi"

- CPU: 0.1 = 100m (milli) --> lower limit is 1m, 1 cpu = 1 core
- Memory: 256Mi (Mebi Byte) = 268435456 Bytes = 268M, G= Gigabyte, Gi=Gibibyte

? What if POD Exceed this limit
    - CPU limit exceeded: POD will Throttle (applications response-time is slowed down), container cannot use more CPU than assigned
    - Mem limit exceeded: if it continues for sometime than POD is terminated, container can use more memory than mentioned

- When a pod is created the containers are assigned a default CPU request of .5 and memory of 256Mi.
-if we want to assign the default resource values to Containers we'll have to create Object `LimitRange`
e.g. https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/#create-a-limitrange-and-a-pod
```