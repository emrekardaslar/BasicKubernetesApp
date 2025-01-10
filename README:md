# Kubernetes Deployment for BasicKubernetesApp

This README provides instructions to deploy the `BasicKubernetesApp` C# application using Docker and Kubernetes.

---

## Prerequisites

- **Docker**: Ensure Docker is installed and running on your machine.
- **Kubernetes**: A Kubernetes cluster is set up (e.g., Docker Desktop Kubernetes, Minikube, or a cloud provider).
- **kubectl**: Installed and configured to interact with your cluster.
- **Container Registry**: Access to a registry like Docker Hub to push your Docker image.

---

## Steps

### 1. Build and Push Docker Image

#### Build the Docker Image
```bash
docker build -t basic-kubernetes-app .
```

#### Tag the Image for Your Registry
Replace `<your-dockerhub-username>` with your Docker Hub username:
```bash
docker tag basic-kubernetes-app <your-dockerhub-username>/basic-kubernetes-app:latest
```

#### Push the Image to the Registry
```bash
docker push <your-dockerhub-username>/basic-kubernetes-app:latest
```

---

### 2. Create Kubernetes YAML Manifests

#### `deployment.yaml`
Define the deployment configuration:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: basic-kubernetes-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: basic-kubernetes-app
  template:
    metadata:
      labels:
        app: basic-kubernetes-app
    spec:
      containers:
      - name: basic-kubernetes-app
        image: <your-dockerhub-username>/basic-kubernetes-app:latest
        ports:
        - containerPort: 5000
```

Save this file as `deployment.yaml`.

#### `service.yaml`
Define the service configuration:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: basic-kubernetes-service
spec:
  selector:
    app: basic-kubernetes-app
  ports:
  - protocol: TCP
    port: 9090
    targetPort: 5000
  type: LoadBalancer
```

Save this file as `service.yaml`.

---

### 3. Deploy to Kubernetes

#### Apply the Deployment
```bash
kubectl apply -f deployment.yaml
```

#### Apply the Service
```bash
kubectl apply -f service.yaml
```

---

### 4. Verify Deployment

#### Check the Pods
```bash
kubectl get pods
```

#### Check the Service
```bash
kubectl get services
```

Look for the external IP of the service (for `LoadBalancer`).

---

### 5. Access the Application

#### With External IP (LoadBalancer):
Visit `http://<EXTERNAL-IP>:9090` in your browser.

#### With NodePort (Local Clusters):
1. Find the node's IP:
   ```bash
   kubectl cluster-info
   ```
2. Access the application at `http://<node-ip>:<node-port>`.

---

### 6. Clean Up Resources (Optional)

To remove the deployment and service:
```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

---

## Troubleshooting

- **Service EXTERNAL-IP is pending**:
  If using a local cluster, `LoadBalancer` may not work. Use `NodePort` or access the cluster IP directly.

- **Application not responding**:
  - Verify the Docker image is working locally: `docker run -d -p 9090:5000 basic-kubernetes-app`
  - Check pod logs: `kubectl logs <pod-name>`

