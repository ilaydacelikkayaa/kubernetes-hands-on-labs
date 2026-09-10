# Kubernetes Node Affinity Lab

This lab demonstrates how Kubernetes Node Affinity can control Pod scheduling based on Node labels.

## Architecture

```text
Kubernetes Cluster
│
├── minikube
├── minikube-m02
│     ├── Label: disksize=large
│     └── nodeaffinity
└── minikube-m03
```

## Node Affinity Configuration

The Pod uses a required Node Affinity rule:

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: disksize
              operator: In
              values:
                - large
                - medium
```

This means the Pod can only be scheduled on a Node where the `disksize` label is either `large` or `medium`.

## Scheduling Test

Initially, none of the three Nodes matched the Node Affinity rule.

Therefore, the Pod remained in:

```text
Pending
```

The following label was then added to `minikube-m02`:

```bash
kubectl label node minikube-m02 disksize=large
```

Kubernetes then successfully scheduled the Pod on `minikube-m02`.

The result was monitored using:

```bash
kubectl get pods -o wide --watch
```

Pod scheduling details were inspected with:

```bash
kubectl describe pod nodeaffinity
```

## Cleanup

The Node label was removed:

```bash
kubectl label node minikube-m02 disksize-
```

The Pod was deleted using the YAML file:

```bash
kubectl delete -f requirednodeaffinity.yaml
```

## Key Learnings

- Understanding Kubernetes Node Affinity
- Using `requiredDuringSchedulingIgnoredDuringExecution`
- Using `matchExpressions`
- Using the `In` operator with multiple label values
- Observing a Pod remain Pending when no Node matches
- Dynamically making a Node eligible by adding a label
- Understanding the difference between NodeSelector and Node Affinity
