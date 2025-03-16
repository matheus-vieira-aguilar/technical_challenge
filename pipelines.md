# CI/CD Pipeline

![k8s Depoy](/diagrams/pipelines_diagram.jpg)

## **Pipeline Stages**
The CI/CD pipeline consists of the following stages:

### **1. Code Integration**
- **Linting & Formatting**: Ensures code consistency and prevents syntax errors.
- **Static Analysis**: Runs tools like ESLint and Prettier to check code quality.
- **Automated Unit Testing**: Executes Jest or Mocha tests to validate core functionality.

### **2. Containerization**
- **Build Docker Images**: Creates lightweight, production-ready images for each microservice.
- **Tag & Push to Container Registry**: Pushes built images to **Docker Hub** or **GitHub Container Registry**.

### **3. Deployment to Kubernetes**
- **Apply Kubernetes Manifests**: Deploys services, including API Layer, Ingestion Service, and Query Service.
- **Scale with HPA**: Ensures auto-scaling based on CPU and memory thresholds.
- **Verify Deployment**: Confirms service health using `kubectl rollout status`.

---

## **GitHub Actions Workflow**
A sample **GitHub Actions** workflow for automating the process:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Install Dependencies
      run: npm install

    - name: Run Linting
      run: npm run lint

    - name: Run Tests
      run: npm test

  containerize:
    needs: build
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Log in to Docker Hub
      run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

    - name: Build & Push Docker Images
      run: |
        docker build -t registry/<service>:latest ./<service>
        docker push registry/<service>:latest
  deploy:
    needs: containerize
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Set up Kubernetes
      uses: azure/setup-kubectl@v3

    - name: Apply Kubernetes Manifests
      run: |
        kubectl apply -f kubernetes/ingress.yaml
        kubectl apply -f kubernetes/<service>.yaml

    - name: Verify Deployment
      run: kubectl rollout status deployment/<service>
```

## **Key Considerations**
- **Automated Testing**: Ensures code quality before deployment.
- **Dockerized Deployment**: Provides portability and consistency.
- **Kubernetes Auto-Scaling**: Handles load dynamically with HPA.
- **Secure Image Management**: Uses private Docker registries.

## **Next Steps**
- Implement **ArgoCD** for GitOps-based Kubernetes deployment.
- Integrate **observability tools** (e.g., Prometheus, Grafana) for monitoring.
