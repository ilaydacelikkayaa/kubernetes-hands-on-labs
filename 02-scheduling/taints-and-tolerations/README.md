# Kubernetes Taints and Tolerations Lab

This lab demonstrates how Kubernetes **Taints and Tolerations** control Pod scheduling and how they can be combined with **NodeSelector**.

The lab was performed on a three-node Minikube cluster.

## Lab Architecture

```text
Kubernetes Cluster
│
├── minikube
├── minikube-m02
└── minikube-m03
      │
      ├── Label: dedicated=devops
      ├── Taint: cpu_speed=fast:NoSchedule
      │
      └── podtaint
```

## 1. Add a Node Label

The `minikube-m03` node was labeled with:

```bash
kubectl label node minikube-m03 dedicated=devops
```

This label was later used with `nodeSelector` to target the Pod to this node.

## 2. Add a NoSchedule Taint

A taint was added to `minikube-m03`:

```bash
kubectl taint node minikube-m03 cpu_speed=fast:NoSchedule
```

The node configuration was inspected with:

```bash
kubectl describe node minikube-m03
```

At this point, the node had:

```text
Label → dedicated=devops
Taint → cpu_speed=fast:NoSchedule
```

## 3. Create a Pod Using NodeSelector

The Pod was configured to target the node with the `dedicated=devops` label:

```yaml
nodeSelector:
  dedicated: devops
```

The Pod therefore targeted `minikube-m03`.

However, `minikube-m03` also had the following taint:

```text
cpu_speed=fast:NoSchedule
```

Since the Pod did not initially have a matching toleration, Kubernetes could not schedule it.

The Pod remained:

```text
Pending
```

The scheduling problem was inspected using:

```bash
kubectl describe pod podtaint
```

The Pod status was monitored continuously using:

```bash
kubectl get pods -o wide --watch
```

## 4. Add a Toleration

A matching toleration was added to the Pod:

```yaml
tolerations:
  - key: cpu_speed
    operator: Equal
    value: fast
    effect: NoSchedule
```

The toleration matched:

```text
cpu_speed=fast:NoSchedule
```

After recreating the Pod, Kubernetes allowed it to run on `minikube-m03`.

```text
Pending
   ↓
ContainerCreating
   ↓
Running
```

This demonstrates that:

```text
Taint      → Node rejects incompatible Pods
Toleration → Pod is allowed to tolerate the taint
```

## 5. Remove the NoSchedule Taint

After the first test, the Pod was deleted and the taint was removed:

```bash
kubectl taint node minikube-m03 cpu_speed-
```

## 6. Test NoExecute

A new nginx Pod named `webtaint` was created:

```bash
kubectl run webtaint --image=nginx
```

Its assigned node was checked using:

```bash
kubectl get pods -o wide
```

A new taint was then added to the node where `webtaint` was running:

```bash
kubectl taint node <NODE_NAME> ram_capacity=high:NoExecute
```

Because `webtaint` did not have a matching toleration, Kubernetes removed it from the node.

## NoSchedule vs NoExecute

```text
NoSchedule
│
└── Prevents new incompatible Pods from being scheduled.

NoExecute
│
├── Prevents new incompatible Pods from being scheduled.
└── Removes existing incompatible Pods from the node.
```

## 7. Exists Toleration

The `podtaint.yaml` configuration was updated for the new taint.

Instead of matching a specific value with `Equal`, the `Exists` operator was used:

```yaml
tolerations:
  - key: ram_capacity
    operator: Exists
    effect: NoExecute
```

This allows the Pod to tolerate a `ram_capacity` taint without requiring a specific value in the toleration.

The Pod was then allowed to run on the tainted node.

## 8. Cleanup

The `ram_capacity` taint was removed:

```bash
kubectl taint node <NODE_NAME> ram_capacity-
```

The Pod was deleted after completing the test.

The additional Minikube worker nodes were also removed after completing the scheduling labs.

## Key Learnings

- Using Node labels and `nodeSelector`
- Understanding Kubernetes Taints and Tolerations
- Using the `NoSchedule` effect
- Using the `NoExecute` effect
- Understanding the difference between `NoSchedule` and `NoExecute`
- Using the `Equal` toleration operator
- Using the `Exists` toleration operator
- Investigating `FailedScheduling` events
- Understanding how Taints and Tolerations affect Pod scheduling

## Important Note

A **toleration does not force a Pod to run on a specific node**.

It only allows the Pod to be scheduled on a node with a matching taint.

Node placement can be controlled separately using mechanisms such as:

- `nodeSelector`
- Node Affinity
