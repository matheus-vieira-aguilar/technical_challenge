# Design System

### **Key Features**
- **API Layer (Express.js, Node.js)** – Handles request validation, routing, and response formatting.
- **Microservices**:
  - **Ingestion Service** – Processes and stores incoming data asynchronously via Kafka.
  - **Query Service** – Retrieves data from Redis (for caching) or MongoDB (fallback).
- **Database Layer**:
  - **MongoDB Cluster** – Persistent storage for structured and semi-structured data.
  - **Redis Cluster** – High-speed caching to optimize query performance.
- **Event-Driven Architecture**:
  - Kafka enables decoupled, scalable processing between ingestion and storage layers.
- **Kubernetes Deployment**:
  - API Gateway and microservices are deployed in **Kubernetes clusters** for scalability and resilience.
  - Uses **Horizontal Pod Autoscaling (HPA)** based on CPU and memory metrics.

## **Technology Choices & Justifications**
- **Node.js & Express.js** – Lightweight, high-performance for API handling.
- **Kafka** – Asynchronous processing, event-driven architecture for scalability.
- **MongoDB** – Document-oriented database optimized for high-scale workloads.
- **Redis** – Caching to reduce database load and improve query response time.
- **Kubernetes** – Container orchestration with auto-scaling capabilities.

## **Setup and Deployment Instructions**
### **1. Local Development (Docker Compose)**
1. Clone the repository:
   ```sh
   git clone <repo-url>
   cd <project-folder>
   ```
2. Start all services with Docker Compose:
   ```sh
   docker-compose up -d
   ```
3. Access the API at `http://localhost:3000`.

### **2. Kubernetes Deployment (Production)**
1. Deploy services to Kubernetes:
   ```sh
   kubectl apply -f kubernetes/ingress.yaml
   kubectl apply -f kubernetes/<service>.yaml
   ```
2. Check service status:
   ```sh
   kubectl get pods -n <namespace>
   ```
3. Scale services dynamically:
   ```sh
   kubectl scale deployment/api-layer --replicas=5
   ```

## **CI/CD Pipeline**
- **GitHub Actions** automates:
  - Linting and unit tests.
  - Docker image builds and pushes.
  - Kubernetes deployments with ArgoCD.

## **Future Roadmap & Optimizations**
- **Migrate from Ingress to API Gateway** for better security, rate limiting, and load balancing.
- **Advanced Monitoring & Observability** with Prometheus, Grafana, and Loki.
- **Optimize Redis Cache Eviction Policies** for better data consistency.
- **Automated Performance Testing** integrated into CI/CD pipeline.
- **Implement ArgoCD for GitOps-based Kubernetes deployment.**

