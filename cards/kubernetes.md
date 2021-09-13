# DevOps::kubernetes

## required top-level entries in a yaml file

```
apiVersion: 
kind: 
metadata: 
spec: 
```


## basic yaml for Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: $pod-name
  labels:
    key: $value
spec:
  containers:
    - name: $container-name
      image: $docker-image
    # it can have multiple containers here
```



## basic yaml for ReplicaSet

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: $replicaset-name
  labels:
    key: $value
spec:
  replicas: $number
  template:
    $pod-metadata
    $pod-spec
  selector:
    matchLabels: # could be 'matchExpressions'
      pod-label-key: $pod-label-value
```



## basic yaml for Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: $replicaset-name
  labels:
    key: $value
spec:
  replicas: $number
  template
    $pod-metadata
    $pod-spec
  selector:
    matchLabels: # could be 'matchExpressions'
      pod-label-key: $pod-label-value
```
