
1. Mark node unschdeulable (draining the node will mark it as unschedulable also will evict the pods & schedule them on other nodes)
>>> k drain node01 --ignore-daemonsets
>>> k describe node node01 | grep Taints
>>> k uncordon node01 --- mark it as schedulable
>>> K get po --field-selector spec.nodeName=controlplane

2. Drain command fails
- because the pod scheduled is not managed by a controller (use --force in that case)
- A forceful drain of the node will delete any pod that is not part of a replicaset.

drain - evict the current running pods & schedule them on other node

3. do not schedule any more pods but dont evict the running ones
- no new pods are scheduled on this node and the existing pods will not be affected by this operation.
>>> kubectl cordon node01

4. Whenever checking the next version to upgrade to check
- latest version available remotely without looking at the current kubeadm version installed
>>> kubeadm upgrade plan 
& check the remote version

- latest version available for an upgrade with thekubeadm upgrade apply v1.25.5 current version of the kubeadm tool installed
- check the kubeadm upgrade apply <version>

5. Upgrading

-------------- for controlplane/master ---- (first upgrade the kubeadm, upgrade the node )
- see if the kubeadm version is required one or not else
>>> k drain controlplane-node --ignore-daemonsets

>>> ssh controlplane-node 
>>> sudo -i
>>> apt-get update && apt-get install kubeadm=1.25.0-00 kubelet=1.25.0-00 kubectl=1.25.0-00
>>> kubeadm upgrade plan 
>>> kubeadm upgrade apply v1.25.0 (etcd upgrade, kube-apiserver upgrade, kube-controller-manager, kube-schedular)

>>> systemctl daemon-reload
>>> service kubelet restart

--- for workernode ----
>>> ssh node01
>>> k drain node01 --ignore-daemonsets
>>> apt-get update && apt-get install kubeadm=1.25.0-00 kubelet=1.25.0-00 kubectl=1.25.0-00
>>> kubeadm version
>>> kubeadm upgrade node

.................
discuss whole process of upgrading kubeadm
- check version of kubeadm, kubelet, kubectl
- drain the controlplane node
- kubeadm install, then upgrade apply 
- kubectl & kubelet install
- reaload the system & restart kubelet
- uncordon 
- check any taints on controlplane if any (remove NoSchedule)

	node 
- drain node
- apt-get update, kubeadm install & kubelet install
- kubeadm upgrade node
- system reload & restart kubelet
- uncordon node01
- k get po -A
.......................