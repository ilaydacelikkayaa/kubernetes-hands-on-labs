# MySQL & phpMyAdmin Multi-Container Pod Lab

This lab demonstrates how to run **MySQL and phpMyAdmin in the same Kubernetes Pod** and configure communication between the containers using environment variables.

It also covers Kubernetes annotations, labels, container-specific commands, port forwarding, and database connectivity.

## Architecture

```text
Browser
   |
   | localhost:8080
   |
   v
kubectl port-forward
   |
   v
+----------------------------+
|         mysql Pod          |
|                            |
|  phpMyAdmin       MySQL    |
|  Port: 80   ---> Port:3306 |
|                            |
+----------------------------+
```

The Pod contains two containers:

- `mysql-container` — runs the MySQL database
- `phpmyadmin` — provides a web interface for managing MySQL

## Pod Configuration

The Pod is defined in:

```text
environment-annotation.yaml
```

The MySQL container uses an environment variable to configure the root password:

```yaml
env:
  - name: MYSQL_ROOT_PASSWORD
    value: "onetwothree!!"
```

The phpMyAdmin container uses environment variables to locate the MySQL server:

```yaml
env:
  - name: PMA_HOST
    value: mysql
  - name: PMA_PORT
    value: "3306"
```

## Deploy

Create the Pod:

```bash
kubectl apply -f environment-annotation.yaml
```

Check its status:

```bash
kubectl get pods
```

Since the Pod contains two containers, a successfully running Pod should display:

```text
READY
2/2
```

## Inspect Pod Configuration

View Pod details, including annotations and environment variables:

```bash
kubectl describe pod mysql
```

## Working with Annotations

Add an annotation:

```bash
kubectl annotate pod mysql email="admin@murataksu.net"
```

Update an existing annotation:

```bash
kubectl annotate pod mysql version="2" --overwrite
```

Remove an annotation:

```bash
kubectl annotate pod mysql kbarticle-
```

## Inspect Environment Variables

Run `printenv` directly inside the MySQL container without opening an interactive shell:

```bash
kubectl exec mysql -c mysql-container -- printenv
```

Run the same command inside the phpMyAdmin container:

```bash
kubectl exec mysql -c phpmyadmin -- printenv
```

This verifies that the environment variables defined in the YAML configuration were passed to the containers.

## Working with Labels

List Pods with the `db=mysql` label:

```bash
kubectl get pods -l db=mysql
```

Add another label to the Pod:

```bash
kubectl label pod mysql app=phpmyadmin
```

Display Pods together with their labels:

```bash
kubectl get pods --show-labels
```

## Access phpMyAdmin

Forward port `8080` on the local machine to port `80` of the Pod:

```bash
kubectl port-forward pod/mysql 8080:80
```

phpMyAdmin can then be accessed locally through:

```text
http://127.0.0.1:8080
```

phpMyAdmin communicates with the MySQL container through port `3306`.

Database connectivity was verified by:

- Connecting to MySQL through phpMyAdmin
- Creating a test database
- Creating a table inside the database
- Confirming that data could be written to the MySQL server

## Cleanup

Delete the Pod using its YAML definition:

```bash
kubectl delete -f environment-annotation.yaml
```

## Key Learnings

- Running multiple containers inside a single Pod
- Configuring containers with environment variables
- Communication between containers in the same Pod
- Adding, updating, and removing annotations
- Managing Kubernetes labels
- Running commands in a specific container with `kubectl exec`
- Using port forwarding to access an application from the host
- Connecting phpMyAdmin to MySQL
- Testing database connectivity through a Kubernetes workload
