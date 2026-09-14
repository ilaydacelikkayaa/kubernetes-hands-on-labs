# Kubernetes emptyDir Volume Lab

This lab demonstrates how `emptyDir` works as a shared temporary volume between multiple containers inside the same Pod.

## Objective

* Create a Pod with two containers (`httpd` and `alpine`).
* Mount the same `emptyDir` volume into both containers.
* Verify that a file created in one container is visible from the other.
* Observe that all data is deleted when the Pod is removed.

## Pod Structure

* **Pod:** `web-emptydir`
* **Volume:** `cache-memory` (`emptyDir`)
* **Mount Path:** `/cache`

## Steps

### 1. Create the Pod

```bash
kubectl apply -f emptydir-httpd.yaml
```

### 2. Verify the Pod

```bash
kubectl get pods
kubectl describe pod web-emptydir
```

### 3. Create a file in the `web` container

```bash
kubectl exec -it web-emptydir -c web -- sh
cd /cache
touch web001.log
```

### 4. Verify from the `logs` container

```bash
kubectl exec web-emptydir -c logs -- ls /cache
```

Expected output:

```text
web001.log
```

### 5. Delete the Pod

```bash
kubectl delete pod web-emptydir
```

## What I Learned

* `emptyDir` creates temporary storage for a Pod.
* Multiple containers inside the same Pod can share the same volume.
* Files written by one container are immediately accessible from another.
* Data stored in `emptyDir` is permanently deleted when the Pod is deleted.

