# Kubernetes Namespace & Resource Limits Lab

This lab demonstrates how to isolate Kubernetes resources using **Namespaces** and control container resource usage with **CPU and Memory requests/limits**.

## Overview

The lab covers:

- Creating and working with Kubernetes Namespaces
- Running a Pod inside a specific Namespace
- Configuring CPU and Memory requests and limits
- Monitoring real-time resource usage
- Accessing a Pod using port forwarding
- Managing resources using imperative and declarative approaches

## Resource Configuration

The `cpuramlimit-ns-pod` Pod runs inside the `frontend` namespace with the following resource configuration:

| Resource | Request | Limit |
|---|---:|---:|
| CPU | 100m | 250m |
| Memory | 128Mi | 256Mi |

Requests define the resources Kubernetes considers when scheduling the Pod, while limits define the maximum resources the container can use.

## Deploy

The Namespace and Pod are defined in the same YAML file:

```bash
kubectl apply -f hardwarelimit-namespace.yaml
```

Verify the Namespace and Pod:

```bash
kubectl get namespace frontend
kubectl get pod cpuramlimit-ns-pod -n frontend
```

Check the configured resource requests and limits:

```bash
kubectl describe pod cpuramlimit-ns-pod -n frontend
```

## Resource Monitoring

View the Pod's current CPU and memory usage:

```bash
kubectl top pod cpuramlimit-ns-pod -n frontend
```

View resource usage across all namespaces:

```bash
kubectl top pods -A
```

## Port Forwarding

Forward the host's port `8080` to the Pod's port `80`:

```bash
kubectl port-forward pod/cpuramlimit-ns-pod 8080:80 -n frontend
```

The application can then be accessed locally on port `8080`.

## Cleanup

Remove the resources using imperative commands:

```bash
kubectl delete pod cpuramlimit-ns-pod -n frontend
kubectl delete namespace frontend
```

## Key Learnings

- Namespaces provide logical isolation for Kubernetes resources.
- Resource requests are considered during Pod scheduling.
- Resource limits restrict container CPU and memory usage.
- `kubectl top` displays real-time resource consumption.
- Port forwarding provides local access to applications running inside Pods.
