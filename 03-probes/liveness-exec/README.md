# Kubernetes Liveness Probe - Exec

This lab demonstrates how Kubernetes uses an **Exec Liveness Probe** to detect an unhealthy container and restart it.

## Configuration

- **Pod:** `liveness-exec`
- **Image:** `busybox`
- **Probe:** Exec
- **Command:** `cat /healthy`
- **Initial Delay:** 5 seconds
- **Period:** 5 seconds

## Run

```bash
kubectl apply -f liveness-exec.yaml
kubectl get pods -w
```

Check that the `/healthy` file exists:

```bash
kubectl exec liveness-exec -- ls /
```

Delete the file to simulate a failure:

```bash
kubectl exec liveness-exec -- rm /healthy
```

Check the Pod events:

```bash
kubectl describe pod liveness-exec
```

After the probe fails repeatedly, Kubernetes restarts the container and the `RESTARTS` count increases.

## Key Learning

```text
/healthy exists → Probe succeeds → Container healthy

/healthy deleted → Probe fails → Container restarted
```

> The container is restarted, not the entire Pod.

## Cleanup

```bash
kubectl delete -f liveness-exec.yaml
```
