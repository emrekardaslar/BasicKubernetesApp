# Argo CD Operations Guide

This README provides an overview of the Argo CD operations we handled, including setting up applications and managing them through the CLI and dashboard.

---

## **Prerequisites**

- **Argo CD Installed**: Ensure Argo CD is installed and running in your Kubernetes cluster.
- **Argo CD CLI Installed**: The `argocd` CLI should be installed and configured.
- **Kubernetes Configured**: You have access to the Kubernetes cluster Argo CD is managing.
- **Git Repository**: Kubernetes manifests stored in a Git repository.

---

## **Operations Overview**

### **1. Login to Argo CD CLI**
To log in to your Argo CD server using the CLI:

```bash
argocd login <ARGOCD_SERVER>
```

Replace `<ARGOCD_SERVER>` with the Argo CD server address:
- For local setups: `localhost:8080`

Retrieve the initial admin password:
```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

Example:
```bash
argocd login localhost:8080 --username admin --password <password>
```

---

### **2. Create a New Project**
To organize applications, you can create a new Argo CD project:

```bash
argocd proj create <project-name> \
  --description "Description of the project" \
  --src <repo-url> \
  --dest https://kubernetes.default.svc,<namespace>
```

- `<project-name>`: The name of the project (e.g., `basic-kubernetes-app`).
- `<repo-url>`: The Git repository URL (e.g., `https://github.com/emrekardaslar/BasicKubernetesApp.git`).
- `<namespace>`: Kubernetes namespace where the resources will be deployed (e.g., `default`).

Example:
```bash
argocd proj create basic-kubernetes-app \
  --description "Project for Basic Kubernetes App" \
  --src https://github.com/emrekardaslar/BasicKubernetesApp.git \
  --dest https://kubernetes.default.svc,default
```

---

### **3. Add a New Application**
To add a new application to Argo CD:

```bash
argocd app create <app-name> \
  --repo <repo-url> \
  --path <relative-path> \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace <namespace> \
  --project <project-name>
```

- `<app-name>`: The name of your application (e.g., `basic-kubernetes-app`).
- `<repo-url>`: The Git repository URL (e.g., `https://github.com/emrekardaslar/BasicKubernetesApp.git`).
- `<relative-path>`: Relative path within the repository to the manifests (e.g., `BasicKubernetesApp/manifests`).
- `<namespace>`: Kubernetes namespace (e.g., `default`).
- `<project-name>`: The Argo CD project (e.g., `default`).

Example:
```bash
argocd app create basic-kubernetes-app \
  --repo https://github.com/emrekardaslar/BasicKubernetesApp.git \
  --path BasicKubernetesApp/manifests \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --project basic-kubernetes-app
```

---

### **4. Sync the Application**
To deploy resources to Kubernetes:

```bash
argocd app sync <app-name>
```

Example:
```bash
argocd app sync basic-kubernetes-app
```

---

### **5. Monitor Application Status**
To check the status of an application:

```bash
argocd app get <app-name>
```

Example:
```bash
argocd app get basic-kubernetes-app
```

---

### **6. Delete an Application**
To delete an application and its resources:

```bash
argocd app delete <app-name>
```

Example:
```bash
argocd app delete basic-kubernetes-app
```

---

### **7. Update an Application**
To update your application:
1. Make changes to the manifests in your Git repository.
2. Commit and push the changes.
3. Sync the application:

```bash
argocd app sync <app-name>
```

---

## **Using the Argo CD Dashboard**

### Accessing the Dashboard
To access the Argo CD dashboard:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open your browser and navigate to: [http://localhost:8080](http://localhost:8080)

### Dashboard Features
- **Sync**: Synchronize the application state with the Git repository.
- **Health**: Monitor resource health (pods, deployments, services, etc.).
- **Rollback**: Revert to previous versions of your application.
- **Auto-Sync**: Enable automatic updates when changes are pushed to Git.

---

## **Troubleshooting**

### Common Issues

#### Application Not Allowed in Project
Error:
```text
App is not allowed in project "<project-name>", or the project does not exist
```
Solution:
- Ensure the project exists.
- Use valid project names (lowercase, alphanumeric, dashes allowed).

#### CLI Not Installed
Error:
```text
zsh: command not found: argocd
```
Solution:
Install the Argo CD CLI:
```bash
brew install argocd
```

---

This guide provides the essential commands and steps to manage applications in Argo CD effectively. Let me know if you need further assistance! 🚀

