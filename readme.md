# Kubernetes Ports Explained

This document explains the purpose of `containerPort`, `port`, `targetPort`, and `nodePort` in Kubernetes.

---

# 1. `containerPort` (Deployment)

`containerPort` is **optional**.

It is used as metadata/documentation to indicate which port the container is expected to listen on.

Even if you don't specify `containerPort`, the application inside the container can still listen on its configured port.

### Without `containerPort`

```yaml
containers:
  - name: mysql
    image: mysql:8.0
```

### With `containerPort`

```yaml
containers:
  - name: mysql
    image: mysql:8.0
    ports:
      - containerPort: 3306
```

Both configurations are valid.

> **Interview Tip:** `containerPort` is **not mandatory**.

---

# 2. `port` (Service)

`port` is **mandatory** in a Kubernetes Service.

It specifies the port on which the Service is exposed inside the cluster.

Example:

```yaml
spec:
  ports:
    - port: 3306
```

If `ports` is omitted, the Service will not be created successfully.

> **Interview Tip:** `port` is **mandatory** for a Service.

---

# 3. `targetPort`

`targetPort` specifies the port on the container to which the Service forwards traffic.

Example:

```yaml
spec:
  ports:
    - port: 3306
      targetPort: 3306
```

If `targetPort` is omitted, Kubernetes automatically sets it equal to the value of `port`.

So these two configurations are equivalent:

### Example 1

```yaml
ports:
  - port: 3306
```

### Example 2

```yaml
ports:
  - port: 3306
    targetPort: 3306
```

---

# 4. `nodePort`

`nodePort` is used only when the Service type is `NodePort`.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  type: NodePort
  selector:
    app: mysql
  ports:
    - port: 3306
      targetPort: 3306
      nodePort: 30306
```

For a `ClusterIP` Service, `nodePort` is **not required**.

---

# MySQL Example

## Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8.0
```

Notice that `containerPort` is not specified, and the Deployment is still valid.

---

## Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
    - port: 3306
```

Since `targetPort` is omitted, Kubernetes automatically uses `3306`.

---

# Summary

| Field           | Resource             | Mandatory | Purpose                                                      |
| --------------- | -------------------- | --------- | ------------------------------------------------------------ |
| `containerPort` | Deployment           | ❌ No      | Documentation/metadata for the container port                |
| `port`          | Service              | ✅ Yes     | Port exposed by the Service                                  |
| `targetPort`    | Service              | ❌ No      | Port on the container receiving traffic (defaults to `port`) |
| `nodePort`      | Service (`NodePort`) | ❌ No      | External port on the Kubernetes node                         |

---

# Interview Questions

### Is `containerPort` mandatory?

**Answer:** No. It is optional and mainly used for documentation and metadata. The container can still listen on its port even if `containerPort` is not specified.

### Is `port` mandatory in a Service?

**Answer:** Yes. Every Kubernetes Service must define at least one `port`.

### Is `targetPort` mandatory?

**Answer:** No. If omitted, Kubernetes automatically sets `targetPort` equal to `port`.

### Is `nodePort` mandatory?

**Answer:** No. It is used only for `NodePort` Services. It is not required for `ClusterIP` Services.
