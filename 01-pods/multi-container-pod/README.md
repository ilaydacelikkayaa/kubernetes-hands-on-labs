# Multi-Container Pod Lab
### Objective

This lab demonstrates how multiple containers can run and be managed inside a single Kubernetes Pod.

The Pod contains three containers:

- **container1:** BusyBox
- **container2:** Alpine
- **container3:** Nginx

### Practice

After creating the Pod, all three containers were verified as running:

```bash
kubectl get pods multipod
```

Container-specific commands and logs were inspected using:

```bash
kubectl exec multipod -c container1 -- cat /readmeBLUE
kubectl logs multipod -c container2
```

The Nginx container was accessed locally using port forwarding:

```bash
kubectl port-forward pod/multipod 8080:80
```

### Troubleshooting

A broken manifest (`multipod-broken.yaml`) was intentionally created to practice debugging.

Two common Kubernetes errors were reproduced:

- **CrashLoopBackOff / ContainerCannotRun:** caused by the invalid `/bin/shall` command.
- **ImagePullBackOff:** caused by the invalid `alpineee` image.

The errors were investigated using:

```bash
kubectl describe pod multipod
```

### Key Takeaways

This lab provided hands-on practice with multi-container Pods, container-specific commands and logs, port forwarding, and Kubernetes troubleshooting using `State`, `Reason`, `Message`, and `Events`.
