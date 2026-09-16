# ReplicationController Lab

This lab demonstrates the basic behavior of a Kubernetes **ReplicationController**.

> **Note:** ReplicationController is a legacy Kubernetes resource. This lab is included to understand replica management before working with ReplicaSets and Deployments.

## What I Practiced

- Creating a ReplicationController using a YAML manifest
- Managing multiple Pod replicas
- Using labels and selectors
- Viewing Pod IP addresses and node information
- Filtering Pods by labels
- Watching Pod changes in real time
- Observing automatic Pod replacement
- Editing the replica count
- Scaling replicas with `kubectl scale`
- Using port forwarding
- Viewing resources with the Minikube Dashboard
- Deleting a ReplicationController

## ReplicationController Structure

The ReplicationController maintains the desired number of Pods.

```text
ReplicationController
        |
        | replicas: 10
        |
        v
   Matching Pods
   app=web-hello
        |
   +----+----+----+
   |    |    |    |
  Pod  Pod  Pod  ...
```

If one of the Pods is deleted, the ReplicationController automatically creates a new one to maintain the desired replica count.

## Create the ReplicationController

The ReplicationController is defined in:

```text
rcontroller-web.yaml
```

Create it with:

```bash
kubectl create -f rcontroller-web.yaml
```

## Check the ReplicationController

List ReplicationControllers:

```bash
kubectl get rc
```

View detailed information:

```bash
kubectl describe rc rcontroller-pod
```

## View Pods with IP Information

```bash
kubectl get pods -o wide
```

The `-o wide` option displays additional information such as Pod IP addresses and the nodes where the Pods are running.

## Filter Pods by Label

The ReplicationController uses the following selector:

```yaml
selector:
  app: web-hello
```

The Pod template contains the matching label:

```yaml
labels:
  app: web-hello
```

List only the Pods with this label:

```bash
kubectl get pods -l app=web-hello
```

## Watch Pods

Watch the Pods in real time:

```bash
kubectl get pods -l app=web-hello -w
```

Delete one of the Pods from another terminal:

```bash
kubectl delete pod <pod-name>
```

The ReplicationController detects that the actual number of Pods is lower than the desired number and automatically creates a replacement Pod.

## Edit the Replica Count

Edit the ReplicationController:

```bash
kubectl edit rc rcontroller-pod
```

Change:

```yaml
replicas: 10
```

to:

```yaml
replicas: 20
```

After saving the changes, Kubernetes creates additional Pods until the desired replica count is reached.

Check the Pods:

```bash
kubectl get pods
```

## Scale the ReplicationController

Scale the ReplicationController down to 5 replicas:

```bash
kubectl scale rc rcontroller-pod --replicas=5
```

Verify the result:

```bash
kubectl get pods
```

## Port Forwarding

Forward local port `8080` to container port `80`:

```bash
kubectl port-forward rc/rcontroller-pod 8080:80
```

The application can then be accessed locally through:

```text
localhost:8080
```

## Minikube Dashboard

Open the Kubernetes dashboard:

```bash
minikube dashboard
```

The ReplicationController and its managed Pods can be inspected through the dashboard.

## Delete the ReplicationController

Delete the ReplicationController:

```bash
kubectl delete rc rcontroller-pod
```

Verify that it has been deleted:

```bash
kubectl get rc
```

The Pods managed by the ReplicationController are also removed.

## Key Concepts

### Replicas

`replicas` defines the desired number of Pod copies.

```yaml
replicas: 10
```

The ReplicationController continuously tries to keep the actual number of matching Pods equal to this desired number.

### Selector and Labels

The selector determines which Pods belong to the ReplicationController.

```yaml
selector:
  app: web-hello
```

It matches Pods containing:

```yaml
labels:
  app: web-hello
```

### Pod Template

The `template` section describes how new Pods should be created.

```text
ReplicationController
        |
        +-- replicas
        |
        +-- selector
        |
        +-- template
               |
               +-- metadata
               |     +-- labels
               |
               +-- spec
                     +-- containers
```

## ReplicationController vs Modern Kubernetes

ReplicationController provides basic replica management, but it is considered a legacy resource.

Modern Kubernetes applications generally use:

```text
Deployment
    |
    v
ReplicaSet
    |
    v
Pods
```

ReplicaSets provide improved selector capabilities, while Deployments add features such as rolling updates and rollbacks.

This ReplicationController lab was completed to understand the fundamental replica-management concept before moving on to **ReplicaSets and Deployments**.
