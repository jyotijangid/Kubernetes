1. Manual Scheduling
    ```
    - If nodeName not already defined in pod, schedular takes that pod & based on scheduling algorithm schedules it on a node.
    - Schedular creates a binding object & assigns pod to the node & sets the nodeName property.
    If there is no schedular pods will be in 'Pending State'.

    - K8s doesn't allow to change node of a running pod but it can be achieved by creating a binding object & sending POST request to the POD's binidng API. This way mimiking what the actual schedular does. 

        pod-bind-definition.yaml

            apiVersion: v1
            kind: Binding
            metadata:
                name: nginx
            target:
                apiVersion: v1
                kind: Node
                name: <node-name>
        >>> curl --header "Content-Type:application/json" --request POST --data {<pod-bind-definition.yaml in json format>} http://$SERVER/api/v1/namespaces/default/pods/$PODNAME/binding
    ```
2. Labels & Selectors

    a. TAINTS (Node) & TOLERATIONS(Pods) -> restricting nodes in scheduling some kind of pods
        
    ```
    >>> kubectl taint nodes <node-name> key=value:<taint-effect>   -> taint-effect=NoSchedule|NoExecute|PreferNoSchedule(what happens to the pods who do not Tolerate this taint)
                                                                    - POD's labels in key-value
    >>> kubectl taint nodes <node-name> key=value:<taint-effect>-  -> untaint a node (- at the end of taint command)

    - Add Toleration to a POD (pod.yaml)
        spec:
            tolerations:
            - key: " "
              operator: " "
              value: " "
              effect: " "
    - by Default pods has no tolerations. 
    - NoExecute -> Running pods & future pods with the key value will be evicted. 

    - Taints & Tolerations does not tell the pod to go to a particular node, instead it tells nodes to only accept pods with certain tolerations.
    - Restrict a pod to certain nodes will be achieved by nodeAffinity
    ```

    Node Affinity
    ```
    - Node Selectors
        pod-definition.yaml
            spec:
                nodeSelector:
                    key: value
    - Node Labeling will only select the nodes with labels but what if we want to select nodes with multiple conidtions then nodeAffinity comes in picture
    >>> kubectl label node <node-name> size=Large
    Now, place the pod on this labeled node.
        - pod.definition.yaml
                spec:
                    nodeSelector:
                        size: Large
            
        - pod.definition.yaml 
                spec: 
                    affinity: 
                        nodeAffinity:
                            requiredDuringSchedulingIgnoredDuringExecution:
                                nodeSelectorTerms:
                                - matchExpersiions:
                                - key:
                                    operator: In | NotIn | Exists
                                    values:
                                    - 

                Condition                                     During Scheduling            During Execution                                               
    requiredDuringSchedulingIgnoredDuringExecution                Required                    Ignored
    preferredDuringSchedulingIgnoredDuringExecution               Preferred                   Ignored
    requiredDuringSchedulingIgnoredDuringExecution                Work in Progress
    preferredDuringSchedulingRequiredDuringExecution              Work in Progress
    ```

3. Resource Limits
4. Daemon Sets
    ```
    - K8s object which runs a single instance of POD in every node of cluster. Similar to ReplicaSet but only one instance.
    - daemon-set.yaml
            kind: DaemonSet
    - all other properties similar to ReplicaSet

    >>> kubectl get daemonset
    >>> kubectl describe daemonset <daemon-set-name>
    ```
5. Multiple Schedulars
6. Schedular Events
7. Configure Kubernetes Schedular