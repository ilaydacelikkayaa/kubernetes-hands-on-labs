# Kubernetes Pod Labels Lab

This lab demonstrates how to use **Labels and Label Selectors** to organize, filter, and manage Kubernetes Pods and Nodes.

## Overview

This lab covers:

- Adding labels to Pods
- Listing Pods with their labels
- Updating existing label values
- Using Equality-based selectors
- Using Set-based selectors
- Filtering and deleting Pods by labels
- Viewing Node labels
- Adding and removing labels from Nodes

## Pod Configuration

The `tomcat-labelpod.yaml` file defines two Pods:

- `tomcat-labelpod-1`
- `tomcat-labelpod-2`

Both Pods run the `tomcat:8.0` image and initially use the following labels:

```yaml
app.kubernetes.io/name: tomcat
app.kubernetes.io/version: "8.0"
app.kubernetes.io/managed-by: helm
app.kubernetes.io/part-of: tomcat
```

## Deployment

Create both Pods using the YAML file:

```bash
kubectl apply -f tomcat-labelpod.yaml
```

List the Pods together with their labels:

```bash
kubectl get pods --show-labels
```

View detailed information about a Pod:

```bash
kubectl describe pod tomcat-labelpod-1
```

## Updating a Label

Update the version label of `tomcat-labelpod-2` from `8.0` to `9.0`:

```bash
kubectl label pod tomcat-labelpod-2 app.kubernetes.io/version=9.0 --overwrite
```

The `--overwrite` option allows an existing label value to be replaced.

## Equality-Based Selector

List Pods with the version label equal to `8.0` and display their IP addresses:

```bash
kubectl get pods -l 'app.kubernetes.io/version=8.0' -o wide
```

## Set-Based Selector

List Pods where the application name is `tomcat` and the version is not `8.0`:

```bash
kubectl get pods -l 'app.kubernetes.io/name=tomcat,app.kubernetes.io/version notin (8.0)' -o wide
```

## Deleting Pods by Label

Delete all Pods with the `app.kubernetes.io/name=tomcat` label:

```bash
kubectl delete pods -l 'app.kubernetes.io/name=tomcat'
```

## Node Labels

Display Nodes together with their labels:

```bash
kubectl get nodes --show-labels
```

Add a label to the Minikube Node:

```bash
kubectl label node minikube kubernetes.io/say=hello
```

Verify the label:

```bash
kubectl get nodes --show-labels
```

Remove the label:

```bash
kubectl label node minikube kubernetes.io/say-
```

## Key Learnings

- Labels provide metadata for organizing Kubernetes resources.
- Label selectors can be used to filter resources.
- Equality-based selectors match exact label values.
- Set-based selectors support operations such as `in` and `notin`.
- Labels can be added, updated, and removed without recreating a resource.
- Labels can be used to manage multiple Pods at once.
