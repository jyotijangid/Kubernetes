##### Questionaire

```
1. create a pod and allow it to be able to set system_time
>>> kubectl run pod <pod-name> --image <image> --command sleep 3200 --dry-run=client -o yaml > pod.yaml

add
    containers:
    - securityContext:
        capabilities:
            add: ["SYS_TIME"]

2. Validate a kubeconfig file
>>> k get nodes --kubeconfig <path-of-config> 

3. Deployemnet replicas not increaseing/working
- the issue is probably with the controller-manager-pod check & recitify
```


```
21. List of internal IPs of all nodes given in the cluster save result to a file
- go to cheat sheet
>>> k get nodes -o=jsonpath='.items[*].status.addresses[?(@.type="InternalIP")].address' > file 

22. Create a new deployment called nginx-deployment with an image nginx:1.16 and 5 replicas. There are 2 worker nodes in our cluster. Please make sure no pod will get deployed on node7. make sure revert back the changes once done
>>> set a taint on node7  or cordon the node which will disableschedilng
>>> k create deploy nginx-deployment --image nginx:1.16 --replicas 5
>>> k uncordon node7

/// kubectl create deployment nginx-deployment --image=nginx:1.16 --replicas=5 --dry-run -o yaml | kubectl label --overwrite -f - "node-role.kubernetes.io/worker!=node7" | kubectl apply -f -

the correct way is to use nodeAffinity as in case of rescheduling of pods, it'll be schedule only on specified node

23. Create a replicaset (name : web-replica, image-nginx, replicas=3), there is already a pod running in our cluster. Please make sure that total count of pods running in the cluster is not more than 3
>>> k get po --show-labels   --> get the label from here
>>> create a rs & in spec.selector.matchLabels & spec.template.metadata.labels make sure to add the same label 

24. Create a daemonset (prod-pd, image=nginx) on all nodes (node1 ... node7) except node8 & controlplane node.
?? How to check keyword for Operator (i.e. In, Exists, NotIn, Equal)
>>> k explain deploy.spec.template.spec.affinity 

sol: set taint on the node you don't want to schedule the pod to
>>> check the taints on nodes first
>>> k taint node node8 env=test:NoSchedule
>>> k create deployment prod-pd --image=nginx $do > ds.yaml (edit it for a DS V) & apply

/// kubectl create daemonset prod-pd --image=nginx --selector='node-role.kubernetes.io/worker' --template='spec.nodeName != "node8" and spec.nodeName != "controlplane"'

26. PV is not namespaced while pvc aligns the PV with a particular namespace.
In order to reassign the PVC to another namespace, we have to delete the pvc & recreate it again in the new ns. Delete the pod so that pvc can be claimed. Recreate the PV as well.

27. Create a pod called web-pod using image nginx, expose it internally with a svc web-pod-svc. Check that you can lookup the service as well as the pod within the cluster.
Use image busybox:1.28 for a new nslookup pod to cross-verify and place the record results in web-svc & web-pod

>>> k run web-pod --image nginx
>>> k expose pod web-pod --name web-pod-svc --port 80
>>> k run nslookup --image busybox:1.28 --command sleep 4800
>>> k exec -it nslookup -- nslookup web-pod-svc > web-svc
>>> k exce -it nslookup -- nslookup ip-of-web-pod-with-dash.default.pod > web-pod

28. Set the node named ek8s-node-0 as unavailable and reschedule all the pods running on it.
>>> k drain ek8s-node-0
>>> k get nodes (Schedulingdisabled)

29. move one static pod from node1 to node2
- identity the static pods directory in node1 & node2
- scp <static-pod-file> node2:<static-path-node2> 
<!-- - ssh into node & ps aux | grep kubelet ---> get the kubelet config file 
- check for "staticPodPath" in kubeletconfig.yaml and move the pod config from node1 diretory

SSH into the node1 and locate the static pod definition file.
Update the file to change the nodeName field to the desired node2.
Copy the updated file to the node2.
Restart the kubelet on node2 to create the pod.
Check the pod status on node2 to ensure that it's running and healthy
On node1, remove the static pod definition file and restart the kubelet. -->


31. For egress traffic in order to be able to connect to the service & resolve the dns we have to add -ports
egress:
    - to:
      - podSelector:
          matchLabels:
            db: mysql
      ports
      - protocol:
        port:
    - to:
      - podSelector:
          matchLabels:
            db: mysql
      ports
      - protocol:
        port:
    - ports:
      - port: 53
        protocol: TCP
      - port: 53
        protoocol: UDP

Network policy takes time to create & apply

34. run pod on node with a particular label
>>> k get nodes --show-labels
>>> add spec.nodeSelector: key:value
>>> OR use affinity: nodeAffinity: prefferredDuringSchedulingIgnoredDuringExection:nodeSelectorTerms: -matchExpressions -key,operator,values -


35. Create a new ClusterRole named deploy-clusterrole, which only allows
us to create the following resource types:
Deployment,StatefulSet,DaemonSet
Create a new ServiceAccount named cicd in the existing namespace app1. Bind the new ClusterRole deploy-clusterrole to the new ServiceAccount cicd, limit to the namespace app1
>>> k create clusterrole deploy-clusterole --verb=create --resource=ds,deploy,ds
>>> k create sa cicd -n app1
>>> k create rolebinding deploy-crb --clusterrole=deploy-clusterole --serviceaccount=app1:cicd

38. Create a file called "config.txt" with two values
key1=value1
key2=value2
Now create a configmap named "newcfgmap" and read data from the file "config.txt" and verify that configmap is created correctly.
>>> k create cm newcfmap --from-file=config.txt

39. View certificate expiry date/ CN name etc
>>> openssl x509 -in <path to .crt> -text

40. Apply pause on rollout of webapp deployment
>>> k rollout pause deploy webapp
>>> describe and check under conditions pause will be applied on deployment

41. Apply autoscaling to webapp deployment with minimum 10 and maximum 20 replicas and target CPU of 85% and verify hpa is created and replicas are increased to 10 from 1

HPA: replicas increase decrease
VPA: in/dec of CPU, mem, disk
>>> k autoscale deploy <deploy-name> --min=10 --max=20 --cpu-percent=85
>>> k get hpa
>>> k get deploy


44. check for all avaialble nodes where you can deploy your workloads (get the count in a file)
- check for taints

>>> k get nodes | grep Taints | grep none | wc -l > file.txt

45. We need a new NetworkPolicy named np that restricts all Pods in Namespace space1 to only have outgoing traffic to Pods in Namespace space2 . Incoming traffic not affected.

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space2
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
```

```
JSONPATH:
  for if condition:
  [?()]
  and now for each item in the list => @  ==/!=/>/<
  [?(@.item>number)]
```

1. ETCD backup & restore
```
>>> apt-get update && apt-get install etcd-client
>>> cat /etc/kubernetes/manifests/etcd.yaml
>>> ETCDCTL_API=3 etcdctl --endpoints=127.0.0.1:2379 --cert= --carcert= --key= snapshot save <path-of-etcd-backup-file>

>>> ETCDCTL_API=3 etcdctl snapshot status <path-of-etcd-backup-file>
>>> ETCDCTL_API=3 etcdctl --data-dir </var/lib/etcd-from-backup> snapshot restore <path-of-etcd-backup-file>

- Always update the data-dir **********
- when we are restoring the snapshot we provide a data-dir where the snapshot would be restored & this needs to be updated in data-dir of manifest of etcd but some downtime is observed 
- volume (etcd-data) of etcd-yaml
```