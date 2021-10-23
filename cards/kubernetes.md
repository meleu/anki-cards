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
  template:
    $pod-metadata
    $pod-spec
  selector:
    matchLabels: # could be 'matchExpressions'
      pod-label-key: $pod-label-value
```


## create a Pod yaml file from CLI

```sh
kubectl run \
  redis --image=redis \
  --dry-run=client \
  -o yaml
```


## create a Deployment yaml from CLI

```sh
kubectl create deployment \
  httpd-frontend \
  --image=httpd:2.4-alpine \
  --replicas=3 \
  --dry-run=client \
  -o yaml
```


## basic yaml for a Service NodePort 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: $service-name
spec:
  type: NodePort
  ports:
    # ports config
  selector:
    key: $pods-label-value
```

## Service NodePort ports config

```yaml
- port: $service-port-connected-to-pods-port
  targetPort: $pod-port
  nodePort: $port-available-to-outworld
```


## Service NodePort image

![](img/Service-NodePort.png)


## What is ClusterIP Service used for?

Allow communication between pods.
(pods and their IPs are ephemeral)


## basic yaml for Service ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: $service-name
spec:
  # optional (ClusterIP is the default)
  type: ClusterIP 
  ports:
    - targetPort: $pods-port
      port: $services-port
  selector:
    label-key: $label-value
    # more labels key-values
```

## 6 basic components of a kubernetes cluster

- API Server
- etcd
- Scheduler
- Controller
- Container Runtime
- Kubelet

## API Server responsibility

Acts as a frontend for kubernetes.

When you use the `kubectl` command, you're interacting with the API Server.

## etcd responsibility

It's a key-value store used to store the data needed to manage the cluster.


## Controller responsibility

Responsible to respond when containers/endpoints go down.

## Scheduler responsibility

Responsible to distribute work across multiple nodes.


## Container Runtime responsibility

Underlying software responsible to run the containers.


## Kubelet responsibility

An agent running on each node. Responsible to make sure the containers are running as expected.

## Custom entries for container's `/etc/hosts` 

Add `hostAliases` to PodSpec:
```yaml
spec:
  hostAliases:
    - ip: "127.0.0.1"
      hostnames:
        - "foo.local"
```

Note: any content in the container's `/etc/hosts` will be overwritten by kubernetes.


## 2 ways to set env variables in kubernetes

- ConfigMap
- Secret


## ConfigMap yaml file

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ${configMapName}
data:
  KEY: ${value}
  KEY2: ${value2}
```


## Accessing the ConfigMap/Secret in Pods

Basic usage: Filling the env vars with the ConfigMap/Secret contents.

In a Pod's spec:
```yaml
containers:
  - envFrom:
    - configMapRef: # secretRef
        name: ${configMapName}
```
**Note**: `name` indentation can be tricky.


## 3 ways to reference ConfigMap/Secret in Pods

- env vars from whole ConfigMap/Secret
- a single env var from the ConfigMap/Secret
- mount the ConfigMap/Secret in a volume


## Mount a ConfigMap/Secret in a volume

In a Pod's spec:
```yaml
containers:
  volumes:
  - name: ${appConfigVolume}
    configMap: # secret
      name: ${configMapName}
```
It creates a directory `/opt/${configMapName}` with the contents of the ConfigMap/Secret in files.

## Pod: run a container as a specific user

In pod's spec:
```yaml
containers:
  - securityContext:
      runAsUser: ${userId}
```
**Note**: it can also be used in Pod's level (above the container level)


## Pod: enable Linux capabilities in a container

In pod's spec:
```yaml
containers:
  - securityContext:
      capabilities: ["CAP1", "CAPN"]
```
See the list of capabilities in `/usr/include/linux/capability.h`.


## 3 possible taint effects:

- `NoSchedule`: new intollerant pods won't be scheduled.
- `PreferNoSchedule`: prefer to start intollerant pods in another Node.
- `NoExecute`: intollerant pods won't be scheduled and running ones will be finished.


## Applying a taint to a Node

```bash
kubectl taint nodes \
  nodeName \
  key=value:taintEffect
```

`taintEffect`: determines what happens to Pods that do not tolerate the taint

Example:
```bash
kubectl taint nodes node1 app=blue:NoSchedule
```


## Applying a toleration to a Pod

Pod's spec:
```yaml
tolerations:
  - key: ${key}
    operator: ${operator}
    value: ${value}
    effect: ${effect}
```


## kubectl -o custom-columns to get restartCount

```bash
kubectl get pod podName \
  -o custom-columns=\
restarts:.status.containerStatuses[].restartCount
```

## Create a Secret from command line

```bash
kubectl create secret generic secretName \
  --from-literal key1=value1 \
  --from-literal keyN=valueN
```

## liveness vs. readiness probes

Liveness: restarts the pod.

Readiness: tells the load balancer to not send more requests to the pod.


## 3 types of liveness/readiness probes

- HTTP Test - `httpGet`
- TCP Test - `tcpSocket`
- Command Test - `exec.command`



## liveness/readiness probe definition (yaml)

In containers' spec:
```yaml
livenessProbe: # same for readinessProbe
  httpGet:
    path: /healthy
    port: 8080
  initialDelaySeconds: 5
  timeoutSeconds: 1
  periodSeconds: 10
  failureThreshold: 3
```

## debug: connect to a running pod

```bash
kubectl exec -it podName -- bash
```

## debug: copy files from/to a pod

```bash
kubectl cp \
  podName:/path/to/pods/file \
  /path/to/local/file
```

## debug: attach to the running process

```bash
kubectl attach -it podName
```

## debug: port forward from local host to a pod

```bash
kubectl port-forward podName
  localPort:podPort
```

## apply a label to a resource

```bash
kubectl label \
  resource name \
  key=value
```

Example:
```bash
kubectl label \
  nodes node01 \
  size=Large
```


## Dockerfile vs. Pod's spec

k8s' `command` overwrites Dockerfile's `ENTRYPOINT`.

k8s' `args` overwrites Dockerfile's `CMD`.


## Spawning pods in a specific Node

Check the labels of the Node you want and then set the `Pod.spec.nodeSelector`, like this:

```yaml
nodeSelector:
  key: $value
```

For more complex situations, use NodeAffinity.


## Basic NodeAffinity config

To run pods in `Large` or `Medium` nodes, put this in Pod's spec:
```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values:
              - Large
              - Medium
```

## 2 Node Affinity types

- requiredDuringSchedulingIgnoredDuringExecution
- preferredDuringSchedulingIgnoredDuringExecution


## Possible Pod Statuses

- Pending
- ContainerCreating
- Running


## Pod Conditions


- PodScheduled
- Initialized
- ContainersReady
- Ready

**Note**: they're all booleans


## Two types of deployment strategies

- `Recreate`: Destroy all pods first, and then bring up the new ones (implies downtime)
- `RollingUpdate`: Destroy 1 pod and bring up the new one. One at a time.


## create a Job from CLI

```bash
kubectl create job jobName \
  --image imageName \
  --dry-run=client \
  -o yaml
```


## create a CronJob from CLI

```bash
kubectl create cronjob cronjobName \
  --image imageName \
  --schedule "30 12 * * *" \
  --dry-run=client \
  -o yaml
```
**Note**: `--schedule`'s argument is a cron-like time config.


## Create a NodePort Service via CLI:

```bash
kubectl expose deployment \
  webapp-deployment \
  --name=webapp-service \
  --type=NodePort \
  --target-port=8080 \
  --port=8080 \
  --dry-run=client \
  -o yaml
```
**Notes:**

1. the deployment must exist
2. nodePort should be added to the file
