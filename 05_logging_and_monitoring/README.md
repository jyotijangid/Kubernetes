1. Monitor Cluster: (metrics-server, prometheus, ELK etc shows the performance metrics of pods, nodes)
-- Metric Server (previous version: Heapster), in-memory, does not store historical data in the disk. kubelet contains a component as cAdvisor which is responsible to collect performance metrics from pods & make it available for the metric-server. 
git clone metric-srever & apply the deployment files
```
kubectl top node || kubectl top pod
```    
2. Application logs: 
```
kubectl logs -f <pod-name> <container-name>
-f: show live  
```