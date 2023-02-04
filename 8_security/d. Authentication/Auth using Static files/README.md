####Setting up Basic Authentication:
***(this is deprecated in 1.19)
1. Create a file with user details locally at `/tmp/users/user-details.csv` or any other location.
2. Edit the kube-apiserver static pod configured by kubeadm to pass in the user details & startup options to include the basic-auth file. Restart the kube-apiserver. `/etc/kubernetes/manifests/kube-apiserver.yaml
`
3. Create the necessary roles and role bindings for these users. `role.yaml` & `role-binding.yaml`.
4. Authenticate into kube-apiserver using the users credentials. `curl -v -k https://localhost:6443/api/v1/pods -u "user1:password123"`.


