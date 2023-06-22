# Kubernetes
Documenting my learning of Kubernetes as a beginner.



Tips & Tricks

1. export do='--dry-run-client -o yaml'
2. export nk='-n kube-system'
3. alias kaf='k apply -f'
4. alias kdp='k delete -f'
5. export now='--grace-period 0 force'
6. mkcd() {mkdir -p "$@" && cd "$@" ;}
7. alias c=clear
8. Copy: ctrl+shift+c paste: ctrl+shift+v



static pod
in case of nodeport service --port is port of svc & how can we supply port of application
get the specs of pv & pvc cleared
check the fields required in creating pv & pvc(accessModes, resources[storage]) for sure


k exec podname <command you want to run>
k exec -it podname -- command you want to run

learn to create network policies (expose UDP&TCP port 53)

# KILLER CODA

vim ~/.vimrc
```
set expandtab
set tabstop=2
set shiftwidth=2
```
1. Logs of pods like kube-apiserver, controller, etcd etc
>>> /var/log/pods/  if not found then go to syslogs (/var/log/syslog or journalctl | grep 'apiserver')
-- check the last few lines & find the error, solve it and check if kube-apiserver is working or not
-- if not go to pods logs & identify the error
>>> /var/log/containers/
>>> journalctl | grep apiserver 
>>> tail -f /var/log/syslog | grep apiserver



>>> crictl ps
>>> crictl logs
>>> docker ps
>>> docker logs

2. Deployment pods stuck in containercreating stage
>>> k describe pod <deploy-pod-1>

3. nginx & httpd both runs on port 80

4. Ingress 
- important to add spec.ingressclassname

5. Priority in pods
Larger the number, the higher the priority
>>> k get prorityclass 
- see the prirotyclassname & priroty and then assign to pod


---------------- 
We can specify ns in deployment, pod by adding -n <ns name> in the imperative command