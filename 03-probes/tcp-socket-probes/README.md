# TCP Socket Liveness & Readiness Probes

This lab demonstrates how Kubernetes uses **Liveness** and **Readiness Probes** to monitor the Tomcat container's `8080` port using TCP socket checks.

## Configuration

- **Pod:** `tcpsocketprobe-tomcat`
- **Image:** `tomcat:8.0`
- **Port:** `8080`
- **Probe:** `tcpSocket`
- **Initial Delay:** 5 seconds
- **Period:** 5 seconds

## Run

```bash
kubectl apply -f tcpsocketprobe-tomcat.yaml
kubectl get pods -w
kubectl describe pod tcpsocketprobe-tomcat
```

To access Tomcat from the local machine:

```bash
kubectl port-forward tcpsocketprobe-tomcat 8080:8080
```

This creates a temporary connection:

```text
localhost:8080 → Pod:8080 → Tomcat
```

## Key Learnings

- `readinessProbe` determines whether the Pod is ready to receive traffic.
- `livenessProbe` restarts the container if the health check repeatedly fails.
- `tcpSocket` checks whether a connection can be established to the specified TCP port.
- `port-forward` provides temporary local access to an application running inside the cluster.

## Cleanup

```bash
kubectl delete -f tcpsocketprobe-tomcat.yaml
```
