
# Google Kubernetes Engine (GKE)

## 1. GKE Fundamentals

1. **Cluster Location**
   - Zonal cluster: Control plane and cluster resources are associated with a single zone.
   - Regional cluster: Control plane is replicated across multiple zones in a region, providing higher availability.

2. **Public and Private Clusters**
   - Public cluster: Worker nodes can have external IP addresses (depending on configuration).
   - Private cluster: Worker nodes have internal IP addresses and no external IP addresses.
   - Private clusters provide better network isolation.

3. **GKE Modes**

   GKE is available in two modes:

   **Standard Mode**
   - We have control over node selection and configuration.
   - We manage node pools, machine types, disk sizes and node autoscaling.
   - Suitable when we need greater control over infrastructure.

   **Autopilot Mode**
   - Google manages the underlying worker nodes.
   - We primarily focus on deploying workloads.
   - Google manages node provisioning, scaling and infrastructure configuration.

---

## 2. GKE Console Components

### 2.1 Workloads
- Workloads represent applications or microservices deployed in GKE.
- Examples:
  - Deployments
  - StatefulSets
  - DaemonSets
  - Jobs
  - CronJobs

### 2.2 Services and Ingress
- Services expose Pods and provide a stable endpoint to access them.
- A Service forwards traffic to matching Pods.
- Example Service types:
  - ClusterIP: Exposes applications internally within the cluster.
  - NodePort: Exposes applications through a port on worker nodes.
  - LoadBalancer: Exposes applications through a cloud load balancer.
- Ingress: Provides HTTP/HTTPS routing to Kubernetes Services.

### 2.3 Storage
Used to provide persistent storage to applications running inside Pods.

Important Kubernetes storage concepts:
- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)
- StorageClass

---

## 3. Kubernetes Core Concepts

| Concept | Description |
|---|---|
| Namespace | Logically separates resources within a cluster. |
| Deployment | Manages stateless applications and their Pods. |
| Service | Provides a stable endpoint to access Pods. |
| Pod | Smallest deployable unit in Kubernetes. |
| ReplicaSet | Ensures the desired number of Pod replicas are running. |

### How Deployment and Service Work Together

1. Deployment creates and manages Pods.
2. Deployment uses ReplicaSets to maintain the desired number of Pods.
3. Pods contain labels.
4. Service uses a selector (matchLabels) to identify matching Pods.
5. Service forwards incoming traffic to those Pods.

Example:

```yaml
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: springboot
  template:
    metadata:
      labels:
        app: springboot
    spec:
      containers:
        - name: springboot
          image: springboot-image
          ports:
            - containerPort: 8080
```

```yaml
# Service
apiVersion: v1
kind: Service
metadata:
  name: springboot-service
spec:
  type: ClusterIP
  selector:
    app: springboot
  ports:
    - port: 80
      targetPort: 8080
```

**Important:** The Service selector `app: springboot` matches the Pod label `app: springboot`.

### Useful kubectl Commands

```bash
# List deployments
kubectl get deployments

# List services
kubectl get services

# List pods
kubectl get pods

# List namespaces
kubectl get namespaces

# List ReplicaSets
kubectl get replicasets

# Get resources from all namespaces
kubectl get pods -A
```

---

## 4. How to Connect to a GKE Cluster from Your Machine

### Step 1: Install GKE Authentication Plugin

```bash
gcloud components install gke-gcloud-auth-plugin
```

This plugin allows kubectl to authenticate with GKE using Google Cloud credentials.

### Step 2: Install kubectl

```bash
gcloud components install kubectl
```

kubectl is the command-line tool used to interact with Kubernetes clusters.

### Step 3: Configure kubeconfig

```bash
gcloud container clusters get-credentials <CLUSTER_NAME> \
    --region <REGION> \
    --project <PROJECT_ID>
```

For a zonal cluster, use:

```bash
gcloud container clusters get-credentials <CLUSTER_NAME> \
    --zone <ZONE> \
    --project <PROJECT_ID>
```

This command:
- Retrieves cluster connection details.
- Updates the local kubeconfig file.
- Configures the cluster context for kubectl.
- Enables kubectl to communicate with the GKE API server.

### Step 4: Verify Connection

```bash
kubectl config get-contexts

kubectl cluster-info

kubectl get nodes

kubectl get pods -A
```

---

## 5. How to Work with Google Cloud from Your Machine

### Step 1: Install Google Cloud CLI

Install the Google Cloud CLI (gcloud).

Official installation guide:

https://cloud.google.com/sdk/docs/install

### Step 2: Initialize gcloud

```bash
gcloud init
```

This helps you:
- Authenticate with your Google account.
- Select a Google Cloud project.
- Configure the default region and zone.

### Step 3: List Authenticated Accounts

```bash
gcloud auth list
```

Lists accounts whose credentials are stored locally.

### Step 4: Create a New Configuration

```bash
gcloud config configurations create <CONFIG_NAME>
```

A configuration allows you to maintain separate settings for different projects or environments.

### Step 5: List Configurations

```bash
gcloud config configurations list
```

### Step 6: Activate a Configuration

```bash
gcloud config configurations activate <CONFIG_NAME>
```

### Step 7: View Current Configuration

```bash
gcloud config list
```

### Step 8: Set Default Project

```bash
gcloud config set project <PROJECT_ID>
```

### Step 9: Set Default Region and Zone

```bash
gcloud config set compute/region <REGION>

gcloud config set compute/zone <ZONE>
```

---

## 6. Important Difference: gcloud vs kubectl

| gcloud | kubectl |
|---|---|
| Interacts with Google Cloud resources. | Interacts with Kubernetes resources. |
| Creates and manages GKE clusters. | Manages Deployments, Pods and Services. |
| Manages GCP IAM and infrastructure. | Manages Kubernetes objects and configurations. |
| Example: `gcloud container clusters list` | Example: `kubectl get pods` |

**Remember:**
- `gcloud` → Google Cloud infrastructure management.
- `kubectl` → Kubernetes cluster and application management.
