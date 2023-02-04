####Authentication: acccess to K8s Cluster

- Users accessing the Cluster:
    User: Admins & Developers 
    Service-Accounts: Third-Party Applications

K8s does not manage User accounts natively it relies on external source like a file with user details or certificates or third-party identity service like LDAP. In case of service-accounts K8s can create & manage Service accounts. So, we cannot create & view users in K8s like:

```
cmds like these does not work in K8s:
>>> kubectl create user admin 
>>> kubectl list users
these do work:
>>> kubectl create serviceaccount sa1
>>> kubectl get serviceaccount
```
- All User access through `kubectl` or `curl https://kube-api-server:6443/` are managed by `kube-api-server`. kube-apiserver authenticates the request before processing it.
- `kube-apiserver` authenticates using four mechanisms: 

    a) Static password file 
    - create a `user-details.csv` file & add user, pass details as below.
    - add `--basic-auth-file = user-details.csv` to kube-api-server.service || in kubeadm add in /etc/kubernetes/mainfest/kube-apiserver.yaml file && restart the kube-apiserver.
    ```
    >>> user-details.csv >> password123,user1,u0001,group123
    >>> user-details.csv >> password234,user2,u0002,group234
    >>> curl -v -k https://master-node-ip:6443/api/v1/pods -u "user1:password123"
    ``` 

    b) Static Token File
    ```
    >>> user-details.csv >> token1,user1,u0001,group123
    >>> user-details.csv >> token2,user2,u0002,group345
    >>> curl -v -k https://master-node-ip:6443/api/v1/pods --header "Authorization: Bearer token1"
    ``` 
    c) Certificates
    d) Third party authentication providers(LDAP) 

#####NOTE
- The authentication mechanism which stores the username, passwords, tokens in clear text in static file is not recommended. 
- Consider volume mount while providing the auth file in a kubeadm setup.
- Setup Role Based Authorization for the new users.


