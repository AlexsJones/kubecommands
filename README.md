
### First stop. Always...

`kubectl explain`

### Build a cluster
```

# This example includes cloud-platform which grants all API access but is required for monitor metric create
gcloud container clusters create example-cluster \
--zone us-central1-a \
--node-locations us-central1-a,us-central1-b,us-central1-c  --min-nodes=1 --scopes=https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/trace.append,https://www.googleapis.com/auth/devstorage.full_control,https://www.googleapis.com/auth/compute,https://www.googleapis.com/auth/cloud-platform --project=myproject
```
### Rescope cluster

WARNING: Starting in Kubernetes v1.10, new clusters will no longer get compute-rw and storage-ro scopes added to what is specified in --scopes (though the latter will remain included in the default --scopes). To use these scopes, add them explicitly to --scopes. To use the new behavior, set container/new_scopes_behavior property (gcloud config set container/new_scopes_behavior true).

`https://www.googleapis.com/auth/cloud-platform` now required for stack trace

```
gcloud container node-pools create np1 --cluster NAME --machine-type n1-standard-1 --num-nodes 5 --scopes https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/trace.append,https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/compute,https://www.googleapis.com/auth/service.management,https://www.googleapis.com/auth/servicecontrol  --zone=us-central1-a
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

### View pod state over nodes

```
kubectl get pod -o=custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName --all-namespaces
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
 ```

### View pod to node relation

```
kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name --all-namespaces
```

### Serverless knative triggermesh pod

`kubectl run -i --tty busybox --image=gcr.io/triggermesh/tm:v0.0.9 --restart=Never -- sh `

### Delete evicted pods

```
kubectl get po -a --all-namespaces -o json | \
jq  '.items[] | select(.status.reason!=null) | select(.status.reason | contains("Evicted")) | 
"kubectl delete po \(.metadata.name) -n \(.metadata.namespace)"' | xargs -n 1 bash -c
```

## Replace an image
```
NAME=demo ;IMAGE=latest;kubectl set image deployment/$NAME $NAME=quay.io/alex/$DEMO:$IMAGE -n default
```
