# Expense Tracker Kubernetes Deployment

## Project Overview

- A simple expense tracking application built for a hackathon, allowing users to add, edit, and delete expenses with categories.
- The app features a persistent PostgreSQL database, a secure backend with ConfigMaps and Secrets, and a user-friendly frontend with inline editing.
- The project has been enhanced with monitoring using Prometheus and logging using Grafana/Kibana.

## Project Structure

- api/: Manifest for the API deployment and services.
- db/: Manifests to start the PostgreSQL with StatefulSet and PersistentVolumeClaim.
- config_secrets/: Manifests containing the ConfigMaps and Secrets for the API and DB to use.
- frontend/: Manifests for the web frontend service and deployment.
- logging/: Manifests for the logging setup that uses Grafana, Loki and Promtail.
- logging2/: Manifests for the logging setip that uses ElasticSearch, Fluentd and Kibana.
- monitoring/: Manifests for the Prometheus deployment, service and RBAC.

### Features

- Add, edit, and delete expenses with descriptions, amounts, categories, and dates.
- Persistent storage using a StatefulSet and PersistentVolumeClaim.
- Secure configuration with Kubernetes ConfigMaps and Secrets.
- Monitoring with Prometheus to track API requests and resource usage.
- Centralized logging using Grafana/Kibana.

### Tech Stack

- **Frontend**: HTML, CSS, JavaScript (served via Nginx)
- **Backend**: Node.js, Express, PostgreSQL
- **Monitoring**: Prometheus
- **Logging**: Grafana/Kibana
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
   kubectl apply -f api/ -f db/ -f config_secrets/ -f frontend/ -f monitoring/ -f logging/
   ```

3. **Confirm that all the pods are running**:

- You can either check via docker desktop or run the following commands in your terminal:

  - For the basic pods:

    ```bash
    kubectl get pods -w
    ```

  - Cancel the commmand (Ctrl-c), once all the pods are in the Running state.

  - For the monitoring pods:

    ```bash
    kubectl get pods -n monitoring
    kubectl get svc -n monitoring
    ```

  - Cancel the commmand (Ctrl-c), once all the pods are in the Running state.

4.**Access the App**:

- Open `http://localhost:30080` in your browser.
- Add, edit or delete expenses to experience the app functionalities.

5.**Setup Monitoring and Logging**:

- Deploy Prometheus for monitoring:

  - Prometheus is deployed to collect metrics from the backend (e.g api_requests_total) in the default and monitoring namespaces. RBAC permissions have been configured for pod discovery as well using ClusterRole and ClusterRoleBinding.
  - To access Prometheus, on a separate terminal tab run the command:

    ```bash
    kubectl port-forward svc/prometheus 9090:9090 -n monitoring
    ```

  - Keep the terminal running to ensure the service stays up.

- Deploy Grafana for logging:

  - Uses the EFK stack (Elasticsearch, Fluentd, Kibana) defined in `logging/`.agent.
  - Confirm that the logging service works:

    ```bash
    kubectl get pods -n monitoring
    ```

  - Port-forward Kibana to your local machine:

    ```bash
    kubectl port-forward svc/kibana -n monitoring 5601:5601
    ```

  - Keep the terminal running to ensure the service stays up.
  - Then, on the browser of choice, open `http://localhost:5601`
  - Once Kibana is running:
    - Navigate to Discover in the Kibana UI.
    - Select or create an index pattern:
    - Use pattern: `fluentd-*` or `logstash-*` depending on configuration.
    - Add filter to focus on your app:

    ```bash
    kubernetes.labels.app : "expense-tracker-api"
    ```

- You will now see all logs emitted by the API container!

6.**CI/CD Pipeline Overview**

- This project includes a GitHub Actions-based CI/CD pipeline that automates the deployment of the Expense Tracker application to a Kubernetes cluster.

Pipeline Features:

CI (Continuous Integration):

- Validates Kubernetes manifests (*.yaml) for syntax errors.
- Lints and checks for basic Kubernetes best practices.

CD (Continuous Deployment):

- Applies all Kubernetes manifests in the /api, /db, /frontend, /config_secrets, and /monitoring directories.
- Deploys the entire stack to the configured Kubernetes cluster automatically.
