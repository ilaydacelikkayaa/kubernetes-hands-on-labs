# Kubernetes NodeSelector Lab

This lab demonstrates how Kubernetes can schedule a Pod on a specific Node using **Node labels** and **nodeSelector**.

## Architecture

```text
Kubernetes Cluster
│
├── minikube
│
├── minikube-m02
│
└── minikube-m03
      │
      ├── Label: disktype=ssd
      │
      └── nselectorpod
            └── redis
```

## Lab Steps

The Minikube cluster was configured with three Nodes:

```bash
minikube start --nodes 3
```

The Nodes were verified with:

```bash
kubectl get nodes
```

A label was added to `minikube-m03`:

```bash
kubectl label node minikube-m03 disktype=ssd
```

The label was verified using:

```bash
kubectl get node minikube-m03 --show-labels
```

## NodeSelector Configuration

The Pod was configured to run only on a Node with the following label:

```yaml
nodeSelector:
  disktype: ssd
```

Since `minikube-m03` was the Node with the `disktype=ssd` label, Kubernetes scheduled the Pod on that Node.

The scheduling result was verified using:

```bash
kubectl get pods -o wide
```

The `NODE` column showed:

```text
minikube-m03
```

Pod details and the NodeSelector configuration were inspected using:

```bash
kubectl describe pod nselectorpod
```

## Cleanup

The Pod was deleted:

```bash
kubectl delete pod nselectorpod
```

The Node label was removed:

```bash
kubectl label node minikube-m03 disktype-
```

## Key Learnings

- Creating a multi-node Minikube cluster
- Working with Kubernetes Node labels
- Understanding Kubernetes scheduling
- Using `nodeSelector` to control Pod placement
- Verifying which Node a Pod is running on
- Adding and removing Node labels
