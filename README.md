## MongoDB Liveness & Readiness Probe Lab

This lab demonstrates how Kubernetes uses **Liveness** and **Readiness Probes** to monitor the health and availability of a MongoDB container.

### What I Practiced

* Created a MongoDB Pod
* Configured environment variables for MongoDB authentication
* Configured an `exec`-based Readiness Probe
* Configured an `exec`-based Liveness Probe
* Used `mongosh` to check MongoDB health
* Connected to MongoDB inside the Pod
* Listed available databases
* Used `kubectl describe` to inspect probe events
* Simulated a Liveness Probe failure
* Observed Kubernetes automatically restarting an unhealthy container

### Probe Configuration

The healthy probe checks MongoDB using:

```bash
mongosh --eval "db.adminCommand('ping')"
```

A successful check returns:

```text
{ ok: 1 }
```

### Liveness Failure Test

To simulate a failure, the Liveness Probe was intentionally changed to query an invalid object:

```bash
mongosh --eval "dbaaaa.adminCommand('ping')"
```

Kubernetes reported:

```text
Warning  Unhealthy  Liveness probe failed: ReferenceError: dbaaaa is not defined
```

Because the Liveness Probe continued to fail, Kubernetes eventually restarted the container after reaching the configured `failureThreshold`.

The Readiness Probe remained healthy, so the Pod could still become `Ready` before the Liveness failure triggered a restart.

### Useful Commands

```bash
kubectl apply -f probe-mongodb.yaml
kubectl get pod mongo
kubectl describe pod mongo
kubectl exec -it mongo -- /bin/bash
kubectl get pod mongo -w
kubectl delete pod mongo
```

### Key Takeaway

* **Readiness Probe** determines whether a Pod is ready to receive traffic.
* **Liveness Probe** determines whether the container is healthy and should continue running.
* A failed Readiness Probe removes the Pod from ready endpoints.
* Repeated Liveness Probe failures cause Kubernetes to restart the container.

