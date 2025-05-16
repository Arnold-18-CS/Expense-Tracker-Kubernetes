# Expense Tracker Kubernetes Deployment

## Project Overview
- A simple expense tracking application built for a hackathon, allowing users to add, edit, and delete expenses with categories.
- The app features a persistent PostgreSQL database, a secure backend with ConfigMaps and Secrets, and a user-friendly frontend with inline editing.
- The project has been enhanced with monitoring using Prometheus.

## Project Structure
- api/: Manifest for the API deployment and services.
- db/: Manifests to start the PostgreSQL with StatefulSet and PersistentVolumeClaim.
- config_secrets/: Manifests containing the ConfigMaps and Secrets for the API and DB to use.
- frontend/: Manifests for the web frontend service and deployment.
- monitoring/: Manifests for the Prometheus deployment, service and RBAC.

### Features
- Add, edit, and delete expenses with descriptions, amounts, categories, and dates.
- Persistent storage using a StatefulSet and PersistentVolumeClaim.
- Secure configuration with Kubernetes ConfigMaps and Secrets.
- Monitoring with Prometheus to track API requests and resource usage.

### Tech Stack
- **Frontend**: HTML, CSS, JavaScript (served via Nginx)
- **Backend**: Node.js, Express, PostgreSQL
- **Monitoring**: Prometheus
- **Deployment**: Kubernetes (Docker Desktop), Docker

## Prerequisites
- Docker Desktop with Kubernetes enabled
- Node.js and npm (for local development)
- `kubectl` configured to interact with your Kubernetes cluster

## Setup Instructions
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/Arnold-18-CS/Expense-Tracker-Kubernetes.git
   cd Expense-Tracker-Kubernetes
   ```

2. **Deploy to Kubernetes**:
```bash
kubectl create namespace monitoring
kubectl apply -f api/ -f db/ -f config_secrets/ -f frontend/ -f monitoring/
```

3. **Access the App**:
- Open **http://localhost:30080** in your browser.
- Add, edit or delete expenses to experience the app functionalities.

4. **Setup Monitoring and Logging**:
- Deploy Prometheus for monitoring:
  - Prometheus is deployed to collect metrics from the backend (e.g api_requests_total) in the default and monitoring namespaces. RBAC permissions have been configured for pod discovery as well using ClusterRole and ClusterRoleBinding.
  - To access Prometheus:
  ```bash
  kubectl port-forward svc/prometheus 9090:9090 -n monitoring
  ```
  - Then ,on the browser of choice, open **http://localhost:9090**
  - 
