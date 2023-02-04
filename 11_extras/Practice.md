##### Basic commands in mock 

```
Create service by directly exposing pod, ClusterIP, NodePort (describe & always check the endpoints)
    >>> k expose po <pod-name> --port <port-number> --name <svc-name>
    >>> k expose deploy <deploy-name> --name <svc-name> --type NodePort --port <application-port> 
        - k edit svc <evc-name> to change the nodeport


Create pod directly in yaml
>>> k run <pod-name> --image <image> -n <ns-name> --dry-run=client -o yaml --command -- sleep 4000 > pod.yaml
Delete & recreate Pod
>>> k replace --force -f <pod.yaml>
>>> k get all --selector key=value
>>> kubectl create -f /tmp/kubectl-edit-ccvrq.yaml

Change the config of running pod
- Edit a pod, a temp file will be generated at a the location. replace command with --force

Deployment
    >>> k set image deploy/rs ...
    >>> k scale deploy/rs
When K8s is launched a default clusterIP svc is created: >>> kubenetes in default namespace


DaemonSet
- create the deployment first then edit
```

Technical Errors
```
POD:
    Init:CrashloopBackOff -> issue with the process running in init container of pod
    >>> k logs <pod-name> <container-name> 
    ImagePullBackOff -> image name/location is incorrect
    Pending State -> Scheduling of pod is not happening (schedular not present or resources not available etc)
```

Silly Errors
```
- get resource details but I tend to not read in which ns they are asking i assume default namespace by default
```


