#  Experiment 12: Study and Analyse Container Orchestration using Kubernetes

---

##  Objective

To understand the importance of Kubernetes in container orchestration, learn its core concepts, and perform deployment, scaling, and self-healing of applications using Kubernetes commands.

---

##  Introduction

Kubernetes is an open-source container orchestration platform used to automate the deployment, scaling, and management of containerized applications. It helps manage complex applications running across multiple containers and systems efficiently.

---

##  Why Kubernetes over Docker Swarm?

| Reason               | Explanation                                             |
| -------------------- | ------------------------------------------------------- |
| Industry Standard    | Widely adopted by companies for production environments |
| Powerful Scheduling  | Automatically assigns workloads to nodes                |
| Large Ecosystem      | Supports many tools for monitoring, logging, CI/CD      |
| Cloud-Native Support | Works seamlessly with AWS, Azure, and Google Cloud      |

---

##  Core Kubernetes Concepts

* **Pod** → Smallest unit in Kubernetes, runs containers
* **Deployment** → Manages Pods and ensures desired state
* **Service** → Exposes Pods to external users
* **Replica** → Number of Pod copies running
* **Node** → Machine where Pods run
* **Cluster** → Group of nodes

---

##  Task 1: Create a Deployment

### Step 1: Create YAML File

```bash
nano wordpress-deployment.yaml
```

### Add the following:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
      - name: wordpress
        image: wordpress:latest
        ports:
        - containerPort: 80
```

### Step 2: Apply Deployment

```bash
kubectl apply -f wordpress-deployment.yaml
```

 Kubernetes creates 2 Pods running WordPress containers.

---

##  Task 2: Expose Deployment (Service)

### Step 1: Create Service File

```bash
nano wordpress-service.yaml
```

### Add:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress-service
spec:
  type: NodePort
  selector:
    app: wordpress
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30007
```

### Step 2: Apply Service

```bash
kubectl apply -f wordpress-service.yaml
```

---

##  Task 3: Verify Setup

### Check Pods

```bash
kubectl get pods
```

### Check Services

```bash
kubectl get svc
```

### Access Application

```
http://localhost:8888
```

---

##  Task 4: Scale Deployment

Increase replicas from 2 to 4:

```bash
kubectl scale deployment wordpress --replicas=4
```

### Verify

```bash
kubectl get pods
```

---

##  Task 5: Self-Healing

### Step 1: List Pods

```bash
kubectl get pods
```

### Step 2: Delete a Pod

```bash
kubectl delete pod <pod-name>
```

### Step 3: Verify Again

```bash
kubectl get pods
```

 Kubernetes automatically creates a new Pod to maintain desired state.

---

##  Docker Swarm vs Kubernetes

| Feature      | Docker Swarm | Kubernetes  |
| ------------ | ------------ | ----------- |
| Setup        | Easy         | Complex     |
| Scaling      | Basic        | Advanced    |
| Ecosystem    | Limited      | Extensive   |
| Industry Use | Rare         | Widely used |

---

##  Conclusion

Kubernetes provides a powerful and flexible platform for managing containerized applications. It supports automatic scaling, self-healing, and efficient resource utilization, making it the preferred choice for modern cloud-based applications.

---