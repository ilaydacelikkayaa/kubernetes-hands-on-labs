# Kubernetes Preferred Node Affinity Lab

This lab demonstrates how Kubernetes uses **Preferred Node Affinity** and weights to influence Pod scheduling.

## Cluster

The lab was performed on a three-node Minikube cluster:

```text
minikube
minikube-m02
minikube-m03
```

## Preferred Node Affinity

The Pod was configured with two scheduling preferences:

- `disktype=fast` with weight `5`
- `hardware=high` with weight `4`

Unlike required Node Affinity, preferred Node Affinity does not prevent a Pod from being scheduled when no matching Node exists.

## Initial Test

The Pod was first created without adding the preferred labels to any Node.

The Pod was still successfully scheduled because the affinity rules were preferences rather than requirements.

Pod status was monitored using:

```bash
kubectl get pods -o wide --watch
```

## Adding Node Labels

The following labels were added:

```bash
kubectl label node minikube-m02 disktype=fast
kubectl label node minikube-m03 hardware=high
```

The Nodes and their labels were verified using:

```bash
kubectl get nodes --show-labels
```

With the initial weights:

```text
minikube-m02 → disktype=fast → weight 5
minikube-m03 → hardware=high → weight 4
```

the Pod was scheduled on `minikube-m02`.

## Changing Scheduling Preference

The weight for the `hardware=high` preference was increased to `10`.

```text
minikube-m02 → disktype=fast → weight 5
minikube-m03 → hardware=high → weight 10
```

After recreating the Pod, Kubernetes preferred `minikube-m03`.

## Cleanup

The labels were removed:

```bash
kubectl label node minikube-m02 disktype-
kubectl label node minikube-m03 hardware-
```

The Pod was then deleted.

## Key Learnings

- Difference between required and preferred Node Affinity
- Using `preferredDuringSchedulingIgnoredDuringExecution`
- Using weights to influence Kubernetes scheduling
- Preferred rules do not prevent scheduling when no Node matches
- Higher-weight preferences have a stronger influence on Node selection
- Adding and removing Node labels
