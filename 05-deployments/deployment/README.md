# Kubernetes Deployment Lab

This lab demonstrates the basic lifecycle and management of a Kubernetes Deployment.

## Concepts Practiced

- Creating and managing Deployments
- Understanding the Deployment → ReplicaSet → Pod relationship
- Scaling applications
- Kubernetes self-healing behavior
- Liveness and readiness probes
- Working with namespaces
- Label selectors
- Port forwarding
- Inspecting Kubernetes resources and events

## Deployment Structure

```text
Deployment
    ↓
ReplicaSet
    ↓
Pods
```

The Deployment manages the ReplicaSet, while the ReplicaSet maintains the desired number of Pods.

## Deployment Configuration

The application is deployed to the `production` namespace with 5 replicas.

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: deployment-web
  namespace: production
  labels:
    app: k8s-web

spec:
  replicas: 5

  selector:
    matchLabels:
      app: k8s-web

  template:
    metadata:
      labels:
        app: k8s-web

    spec:
      containers:
        - name: web-rs-container
          image: murataksunet/k8s-basic

          ports:
            - containerPort: 80

          livenessProbe:
            httpGet:
              path: /healthcheck
              port: 80

          readinessProbe:
            httpGet:
              path: /ready
              port: 80
```

## Running the Deployment

Create the namespace:

```bash
kubectl create namespace production
```

Apply the manifest:

```bash
kubectl apply -f deployment-web.yaml
```

Check the resources:

```bash
kubectl get deployment,rs,pods -n production
```

View Pod IP addresses and node information:

```bash
kubectl get pods -n production -o wide
```

## Inspecting Resources

```bash
kubectl describe deployment deployment-web -n production
kubectl get rs -n production
kubectl describe pod <pod-name> -n production
```

## Scaling

Scale the Deployment:

```bash
kubectl scale deployment deployment-web --replicas=7 -n production
```

Kubernetes automatically adjusts the number of Pods through the ReplicaSet.

## Self-Healing

Deleting a Pod demonstrates the self-healing behavior of the ReplicaSet:

```bash
kubectl delete pod <pod-name> -n production
```

A replacement Pod is automatically created to maintain the desired replica count.

## Liveness Probe Test

The health check file was intentionally removed from one container:

```bash
kubectl exec -n production <pod-name> -- rm /usr/share/nginx/html/healthcheck
```

The liveness probe started receiving HTTP `404` responses. After repeated probe failures, Kubernetes restarted the unhealthy container.

The restart can be observed with:

```bash
kubectl get pods -n production
kubectl describe pod <pod-name> -n production
```

## Port Forwarding

Forward local port `8080` to port `80` of a Pod:

```bash
kubectl port-forward pod/<pod-name> 8080:80 -n production
```

The application can then be accessed locally through `localhost:8080`.

## Cleanup

```bash
kubectl delete deployment deployment-web -n production
```

Optionally remove the namespace:

```bash
kubectl delete namespace production
```

## Key Takeaways

This lab demonstrates how Kubernetes Deployments provide declarative application management, scaling, self-healing, health checks, and automated ReplicaSet/Pod management.
