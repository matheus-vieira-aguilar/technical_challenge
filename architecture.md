# Architecture Documentation

![k8s Depoy](/diagrams/architecture.jpg)

## **High-Level Project Overview**

The architecture ensures:

- **Performance**: API read/write operations respond within **500ms**.
- **Scalability**: Supports millions or billions of records with **minimal performance degradation**.
- **Reliability**: Fault-tolerant design with **Kubernetes, MongoDB, Redis, and Kafka**.
- **Maintainability**: Modular structure with well-defined responsibilities.

## **Architecture Design and Rationale**

### **System Components**
1. **API Layer** (Express.js + Node.js)
   - Serves as the single entry point for external requests.
   - Handles authentication, validation, and request routing.
2. **Ingestion Service** (Express.js + Kafka)
   - Processes and persists incoming data asynchronously.
   - Uses **Kafka** for event-driven data streaming.
3. **Query Service** (Express.js + Redis + MongoDB)
   - Fetches data efficiently using **Redis caching** for performance.
   - Queries MongoDB for persistent storage.
4. **Database Layer**
   - **MongoDB Cluster**: Stores structured JSON data efficiently.
   - **Redis Cluster**: Caches frequently accessed data to reduce query times.
5. **Infrastructure** (Kubernetes + Ingress + HPA)
   - **Kubernetes**: Ensures high availability and auto-scaling.
   - **Horizontal Pod Autoscaler (HPA)**: Scales services based on CPU/memory utilization.
   - **Ingress Controller**: Routes external requests efficiently.
   
## **Justification for Technology Choices**

### **Scalability & Performance**
- **Kubernetes**: Enables dynamic scaling, fault tolerance, and deployment automation.
- **Kafka**: Provides distributed, high-throughput messaging, ensuring event-driven architecture scalability.
- **Redis Cluster**: Supports high-speed data access and caching to reduce database load.
- **MongoDB Cluster**: Offers flexible schema, high availability, and replication for handling large-scale data.

### **Ease of Maintenance**
- **Express.js + Node.js**: Simple, lightweight, and widely adopted for microservices.
- **Microservices**: Enables independent deployment and scalability per service requirements.
- **Containerized Deployment (Docker + Kubernetes)**: Ensures consistent runtime environments across development, testing, and production.
- **Ingress Controller**: Simplifies external access while maintaining control over routing.

### **Resilience & Fault Tolerance**
- **Kafka**: Ensures durability and fault tolerance with partitioning and replication.
- **MongoDB Replica Sets**: Provides automatic failover and data consistency.
- **Redis Cluster**: Distributes cache across multiple nodes to prevent single points of failure.
- **HPA (Horizontal Pod Autoscaler)**: Adjusts the number of running instances based on resource usage.

## **Key Technical Decisions and Trade-offs**

| Decision | Justification | Trade-off |
|----------|--------------|------------|
| **Ingress Controller** | Simple and Kubernetes-native solution for routing. | May lack advanced API gateway features. |
| **Kafka for Ingestion** | Enables async, distributed processing. | Adds complexity in deployment and monitoring. |
| **Redis for Caching** | Improves query performance significantly. | Requires cache invalidation strategies. |
| **MongoDB for Storage** | Scales well for document-based storage. | Not ideal for complex relational queries. |

## **Setup and Deployment Instructions**

### **Prerequisites**
- Kubernetes cluster (min **3 nodes**)
- Docker & Kubectl installed
- Helm for managing Redis and MongoDB

### **Deployment Microservices in Kubernetes**
1. **Deploy Microservices & API Layer**
   ```sh
   kubectl apply -f /kubernetes/<service>.yaml
   ```
2. **Enable Autoscaling**
   ```sh
   kubectl apply -f /kuberntes/<service>.hpa.yaml
   ```
3. **Configure Ingress**
   ```sh
   kubectl apply -f ingress.yaml
   ```

### **Deployment Strategy for Kafka, Redis and MongoDB**

1. **Kafka**
  - Use **Confluent Cloud** or **AWS MSK** for hassle-free operations.
2. **Redis**
  - Use **AWS ElastiCache**, **Azure Cache for Redis**, or **Google Memorystore**.
3. **MongoDB**
- Use **MongoDB Atlas**, **AWS DocumentDB**, or **Azure CosmosDB**.

## **Future Roadmap and Optimization Ideas**

1. **API Gateway Instead of Ingress**
   - **Why?** Ingress works well but lacks **rate limiting, authentication, and request transformation**.
   - **Solution:** Use **Kong, Traefik, or API Gateway** for better control over API traffic.

2. **Optimize Kafka Processing**
   - **Why?** Improve ingestion performance with **partitioning** and **consumer groups**.
   - **Solution:** Increase Kafka partitions and optimize message batching.

3. **Advanced Caching Strategies**
   - **Why?** Reduce unnecessary database calls and **optimize cache invalidation**.
   - **Solution:** Implement **write-through and TTL-based caching**.

4. **Better Deployment with ArgoCD**
   - **Why?** Improve GitOps-driven deployment, rollback, and monitoring.
   - **Solution:** Use **ArgoCD** to automate deployments, manage Kubernetes manifests declaratively, and enable continuous delivery with Git integration.
