
### First stop. Always...

`kubectl explain`


### Rescope cluster

WARNING: Starting in Kubernetes v1.10, new clusters will no longer get compute-rw and storage-ro scopes added to what is specified in --scopes (though the latter will remain included in the default --scopes). To use these scopes, add them explicitly to --scopes. To use the new behavior, set container/new_scopes_behavior property (gcloud config set container/new_scopes_behavior true).


```
gcloud container node-pools create np1 --cluster NAME --machine-type n1-standard-1 --num-nodes 5 --scopes https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/trace.append,https://www.googleapis.com/auth/devstorage.full_control,https://www.googleapis.com/auth/compute  --zone=us-central1-a
```

```
kubectl cordon <NODE_NAME>
```
of for GKE..
```
for node in $(kubectl get nodes -l cloud.google.com/gke-nodepool=default-pool -o=name); do
  kubectl cordon "$node";
done
```

```
kubectl drain <NODE_NAME> --force
```

```
kubectl get pods -o wide
```

```
gcloud container node-pools delete default-pool \
   --cluster <YOUR_CLUSTER_NAME> --zone <YOUR_ZONE>
```

### Proxy a pod

```
sudo kubectl port-forward kibana-83rrj 5601:5601 --namespace=elasticsearch
```

### View pod on node

```
kubectl get pods -o wide --sort-by="{.spec.nodeName}"
```

### Spin up quick pod

```
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh 
```

### Role binding

```
kubectl create clusterrolebinding cluster-admin-binding \
--clusterrole cluster-admin --user=<yada>
```

### Labelling nodes & affinity
```
kubectl label node gke-foundation-default-pool-cc60e51f-88lm nodepool=test
kubectl get nodes --show-labels=true | grep test
```

```
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/nodepool
            operator: In
            values:
            - test
```
### Node pool affinity
```
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: dedicated
            operator: In
            values: ["my-pool"]
  tolerations: 
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
 ``
