##### Secuirty KUBECONFIG

```
List pods using K8s Rest API & certificates
>>> curl https://my-kube-playground:6443/api/v1/pods --key admin.key --cert admin.crt --cacert ca.crt
    {
        "kind": "PodList",
        "apiVersion": "v1",
        "metadata": {
        "selfLink": "/api/v1/pods",
        },
        "items": []
    }

>>> kubectl get pods \
        --server my-kube-playground:6443 \ 
        --client-key admin.key \
        --client-certificate admin.crt \
        --certificate-authority ca.crt \

>>> kubectl get pods --kubeconfig config
>>> kubectl get pods  ------ no need to provide config file explicitly as the default config is loaded automatically
```

##### KubeConfig File
```
- config file has three sections:
    1. Clusters: Various K8s clusters that you need access to. i.e. production, testing, development
    2. Users: User accounts with which we have access to the clusters. i.e. Admin user, Dev user, Prod user
    3. Contexts: Defines which user account will be used to access which cluster. i.e. Admin@Production, Dev@Development 
- This file does not define any new users or configuring any kind of user access or authorization, it is using existing users to login into clusters. So, that we don't have to define the user & certificates in `kubectl` command everytime.
```
- Writing the config everytime is tedious task so, we move it to a `config` file at `$HOME/.kube/config` (by default the kubectl looks at the file in this directory so, no need to supply explicitly).
- `config` file
    ```
    --server my-kube-playground:6443 
    --client-key admin.key 
    --client-certificate admin.crt 
    --certificate-authority ca.crt
    ```

```
>>> kubectl config view  ----- view the current config file
>>> kubectl config view --kubeconfig=my-custom-config  ---- use this config file
>>> kubectl config use-context dev-user@production         ----- sets the default context
>>> kubectl config -h
>>> kubectl config --kubeconfig=<location-of-config-file to be used> use-context <context-name>
*** In order to change the config file, add your config file on default location with `config` name
```

