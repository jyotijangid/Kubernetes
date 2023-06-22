1. How to validate a kubeconfig file 
>>> k get nodes --kubeconfig=<config-location>

2. Why unable to scale up the replicas in deployment 
- scaling up is done by kube-controller manager 

3. How to get CN name configured on a certificate
>>> openssl x509 -in file-path.crt -text -noout

4. Show labels
>>> k get pods --show-labels

5. Get resources within the cluster with maximum listable information (but without headers)
>>> key get all -A -o wide --no-headers

6. Create secret 
>>> k create secret <secret-type> -h