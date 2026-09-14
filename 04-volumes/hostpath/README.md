# Kubernetes HostPath Volume Lab

This lab demonstrates persistent node-level storage using a Kubernetes `hostPath` volume.

## What I Learned

* Using a `hostPath` volume to store data on a Kubernetes node
* Persisting data after a Pod is deleted
* Sharing a single volume between multiple containers
* Using `subPath` to mount different directories from the same volume
* Using an `initContainer` to prepare files before the main containers start

## Architecture

The Pod contains:

* An `install` initContainer that downloads `index.php`
* A PHP/Apache container
* A MySQL container
* A shared `hostPath` volume named `storage`

The volume uses `/appdata` on the Minikube node.

```text id="1yy8dr"
/appdata
├── html
│   └── index.php
└── mysql
    └── MySQL data
```

The PHP container uses:

```text id="66tlqu"
/appdata/html → /var/www/html
```

The MySQL container uses:

```text id="4hlyw2"
/appdata/mysql → /var/lib/mysql
```

These directories are selected using `subPath`.

## Persistence Test

A MySQL database was created and the Pod was deleted.

After connecting to the Minikube node, the data was still available under:

```bash id="vpylx5"
ls /appdata
ls /appdata/html
ls /appdata/mysql
```

The Pod was then recreated and the existing PHP and MySQL data became available again.

## Key Concepts

`hostPath` mounts a directory from the Kubernetes node into a Pod.

`mountPath` specifies where the volume appears inside a container.

`subPath` allows a container to mount only a specific directory from a volume.

`initContainer` runs before the main application containers and can be used for initialization tasks.

> Note: `hostPath` is useful for learning and specific node-level workloads, but production applications commonly use PersistentVolumes and PersistentVolumeClaims for persistent storage.

