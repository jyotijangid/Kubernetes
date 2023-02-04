####TLS Certificates in K8s

- Go through TLS Basics first.
- Three types of certificates:
    - Server Certificates: configured on the server
    - Root Certificates: configured on the CA
    - Client Certificates: configured on the clients
- SERVER CERTS for Servers to authenticate their clients. In K8s we have following server components:
    - kube-apiserver: `apiserver.crt || apiserver.key`
    - etcd server:   `etcdserver.crt || etcdserver.key`
    - Kubelet server:   `kubelet.crt || kubelet.key`
- CLIENT CERTS for Clients. Clients that access the server components of K8s are:
    - Admin: `admin.crt || admin.key`
    - Schedular: `schedular.crt || schedular.key`
    - Controller Manager: `controller-manager.crt || controller-manager.key`
    - Kube Proxy: `kube-proxy.crt || kube-proxy.key`
    - kube-apiserver is a client for ETCD server so,         `apiserver-etcd-client.crt || apiserver-etcd-client.key`
    - kube-apiserver is a client for Kubelet server so,    `apiserver-kubelet-client.crt || apiserver-kubelet-client.key` 
    - Kubelet is also a client for ETCD server so,
    `kubelet-client.crt || kubelet-client.key`
- We need CA for all the certificates. We can have more than one CA as well. i.e one for Server Certs & one for Client Certs. `ca.crt || ca.key`

####Certificate Creation:
Tools like: easyrsa, openssl, cfssl etc.
We will use openssl for now. First we will generate certificates for CA so, that other client/server components can get their certificates signed.
1. CA Certificates: Generate private key, create a csr using private key as input & then self-sign the csr using x509. As this cert is for CA itself so it is self-signed. 
```
>>> openssl genrsa -out ca.key 2048
>>> openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
>>> openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
** This ca.key will be used to sign other certificates 
```

2. Generating CLIENT CERTIFICATES

    - Admin User: (signed using ca.key)
        ```
        >>> openssl genrsa -out admin.key 2048
        >>> openssl req -new -key admin.key -subj "/CN=kube-admin/O=system:masters" -out admin.csr
        >>> openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt
        ```

    - KUBE-SCHEDULAR: system-component so name must have prefix system. 
        ```
        >>> openssl genrsa -out schedular.key
        >>> openssl req -new -key schedular.key -subj "/CN=system:kube-schedular/" -out schedular.csr
        >>> openssl x509 -req -in schedular.csr -CA ca.crt -CAKey ca.key -out schedular.crt
        ```

    - CONTROLLER-MANAGER: system-component so name must have prefix system. 
        ``` 
        >>> openssl genrsa -out controller-manager.key 
        >>> openssl req -new -key controller-manager.key -subj "/CN=system:kube-controller-manager/" -out controller-manager.csr 
        >>> openssl x509 -req -in controller-manager.csr -CA ca.crt -CAKey ca.key -out controller-manager.crt
        ```

    - KUBE-PROXY:
        ```
        >>> openssl genrsa -out kube-proxy.key 
        >>> openssl req -new -key kube-proxy.key -subj "/CN=system:kube-proxy/" -out kube-proxy.csr 
        >>> openssl x509 -req -in kube-proxy.csr -CA ca.crt -CAKey ca.key -out kube-proxy.crt
        ```

    - KUBELET acting as Client (Kubelet talks to kube-apiserver)
        - generated for every node as kubelet is present on every node.
        - As nodes are system components these are named as `system:node:node01` & added to a group named `SYSTEM:NODES`
            ```
            kubelet-client.key
            kubelet-client.csr
            kubelet-client.crt
            ```
        - Once the certificates are generated they go into `kube-config.yaml` file.
3. Generating SERVER CERTIFICATES

    - ETCD SERVER: 
        ```
        generate the key,crt as above:
        etcdserver.key
        etcdserver.csr
        etcdserver.crt
        ```
        - In case of high availablity environment etcd server can be deployed on multiple server. In this case to secure communication between different members of etcd we generate additional etcdpeer crts as well. `peer.key, peer.csr, peer.crt`
        - `etcd.yaml`
            ```
            - etcd
                --key-file = etcdserver.key
                --cert-file = etcdserver.crt
                --peer-cert-file = etcdpeer1.crt
                --peer-client-cert-auth=true
                --peer-key-file=peer.key
                --peer-trusted-ca-file = ca.crt    
                --trusted-ca-file = ca.crt   
            ```

    - KUBE-APISERVER:

        ```
        >>> openssl genrsa -out apiserver.key 2048
        >>> openssl rep -new -key apiserver.key -subj "/CN-kube-apiserver" -out apiserver.csr -config openssl.cnf
        >>> openssl x509 -req -in apiserver.csr -CA ca.crt -CAKey ca.key -out apiserver.crt
        ```
        - different names for kube-apiserver through which a user can call the server should be included in the apiserver.crt (e.g. kubernetes, kubernetes.default, kubernetes.default.svc, kubernetes.default.svc.cluster.local, ip of kube-api-server, ip of pod running kubeapiserver)
        - we define a openssl.cnf file & specify the alt_names in there.
        - `openssl.cnf`
            ```
            [req]
            req_extensions =    
            distinguished_name = 
            [v3_req]
            basicConstraints = 
            keyUsage = 
            subjectAltName = @alt_names
            [alt_name]
            DNS.1 = kubernetes
            DNS.2 = kubenetes.default
            DNS.3 = kubenetes.default.svc
            DNS.4 = kubenetes.default.svc.cluster.local
            IP.1 = 
            IP.2 =
            ```
        - `kube-apiserver.service`
            ```
            --client-ca-file = ca.pem
            --tls-cert-file = apiserver.crt
            --tls-private-key-file = apiserver.key
            --etcd-cafile = ca.pem
            --etcd-certfile = apiserver-etcd-client.crt
            --etcd-keyfile = apiserver-etcd-client.key
            --kubelet-certificate-authority = ca.pem
            --kubelet-client-certificate = apiserver-kubelet-client.crt
            --kubelet-client-key = apiserver-kubelet-client.key 
            ```

    - KUBELET SERVERS (Kubelet-nodes): Server Certs
        - as kubelet is on every node, we need key-cert pair for each node in cluster [kubelet-node01.crt, kubelet-node01.key, kubelet-node02.crt, kueblet-node02.key ....]
        - `kubelet-config.yaml` for each node in the cluster
            ```
            kind: KubeletConfiguration
            apiVersion: kubelet.config.k8s.io/v1beta1
            authentication:
            x509:
            clientCAFile: "/var/lib/kubernetes/ca.pem"
            authorization:
            mode: Webhook
            clusterDomain: "cluster.local"
            clusterDNS:
            - "10.32.0.10"
            podCIDR: "${POD_CIDR}"
            resolvConf: "/run/systemd/resolve/resolv.conf"
            runtimeRequestTimeout: "15m"
            tlsCertFile: "/var/lib/kubelet/kubelet-node01.crt"
            tlsPrivateKeyFile: "/var/lib/kubelet/kubelet-
            node01.key"              
            ```  
   
Use of the certificates:
`>>> curl https://kube-apiserver:6443/api/v1/pods --key admin.key --cert admin.crt --cacert ca.crt`
or Add them in `kube-config.yaml`

#####NOTE
```
- System components which are part of K8s control plane so, it's name must be prefixed with keyword system i.e. schedular, controller-manager, kube-proxy. 
- Like in browser we have a copy of crt of different CA, in K8s in order for each component to verify each other they all need a copy of ca.crt (root certificate), all clients & servers.
```


-------

#####CERTIFICATE API

- When a new admin2 wants access to the cluster they create a private key & generates a csr & sends to admin1. The admin1 takes the csr & get it signed by the ca server private key & root certificate thereby generating a crt & sending the crt back to admin2.
- Now admin2 as valid crt & key to access the cluster.
- When validity period is over a new csr is generated & agained gets signed by the ca.
- CA is just a pair of key & crt file we have generated for K8s environment. So, these need to be protected & stored in safe environment. 
- We create a server to store the key & crt file of CA - CA Server (so for signing a crt, we'll need to login into the CA server)
- Sometimes master nodes acts as a CA server. (Kubeadm)
- With Certificate API we can send CSR directly to K8s through an API call.
- Now when admin recieves a CSR instead of logging on to the master code and signing the CSR by himself, he creates a K8s API Object called CertificateSigningRequest Object. Admin will check all the CSRs, review & approve them easily with kubectl commands. Ther CERT is shared with users now.  
```
>>> openssl genrsa -out user-name.key
>>> openssl req -new -key user-name.key -subj "/CN=user-name" -out user-name.csr
>>> cat user-name.yaml (see the request field)   
>>> cat user-name.csr | base64 | tr -d "\n"
>>> kubectl get csr
>>> kubectl certificate approve user-name 
>>> kubectl get csr user-name -o yaml 
>>> echo wertyuifch[certificate in encoded] | base64 --decode              ----------- can be shared with the user
>>> kubectl delete csr <csr-name>
```
****NOTE

Who does all of the certificate related operations???
- by controller manager using controllers like CSR APPROVING, CSR SIGNING
- `kube-controller-manager.yaml`
    ```
    --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
    --cluster-signi-ng-key-file=/etc/kubernetes/pki/ca.key
    ```
