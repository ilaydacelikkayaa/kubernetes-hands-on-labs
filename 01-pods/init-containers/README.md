# Kubernetes Init Containers Lab

This lab demonstrates how **Kubernetes Init Containers** can be used to control the startup order of containers and wait for required services before starting the main application.

## Architecture

The Pod contains two Init Containers and one main container:

```text
init-myservice
      ↓
Wait for myservice
      ↓
init-mydb
      ↓
Wait for mydb
      ↓
myapp-main
```

Init Containers run sequentially. The main application starts only after both Init Containers complete successfully.

## Pod Configuration

- **init-myservice:** Waits until the `myservice` Service can be resolved through Kubernetes DNS.
- **init-mydb:** Waits until the `mydb` Service becomes available.
- **myapp-main:** Starts after both dependencies are available.

The Services are checked using their Kubernetes DNS names:

```text
myservice.default.svc.cluster.local
mydb.default.svc.cluster.local
```

## Running the Lab

Create the Pod:

```bash
kubectl apply -f initcontainer.yaml
```

Initially, the Pod waits for its dependencies:

```text
READY   STATUS
0/1     Init:0/2
```

Create the first Service:

```bash
kubectl expose pod initpod --name=myservice --port=80 --target-port=9376
```

The first Init Container completes and the Pod moves to:

```text
0/1     Init:1/2
```

Create the second Service:

```bash
kubectl expose pod initpod --name=mydb --port=80 --target-port=9377
```

After both Init Containers complete, the main container starts:

```text
READY   STATUS
1/1     Running
```

## Verification

Check Init Container logs:

```bash
kubectl logs initpod -c init-myservice
kubectl logs initpod -c init-mydb
```

Check the main container:

```bash
kubectl logs initpod -c myapp-main
```

Expected output:

```text
ANA UYGULAMA CALISLAMAYA BASLADI
```

## Cleanup

Delete the Pod using its label and remove the Services:

```bash
kubectl delete pod -l app=initapp
kubectl delete service myservice mydb
```

## Key Learnings

- Init Containers run before application containers.
- Init Containers execute sequentially.
- Kubernetes DNS allows Services to be discovered by name.
- The main container starts only after all Init Containers complete successfully.
- Labels can be used to select and manage Kubernetes resources.
