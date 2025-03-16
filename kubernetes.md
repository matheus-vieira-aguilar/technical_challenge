# Microservice Scalability Strategy

![k8s Depoy](/diagrams/k8s_diagram.jpg)

The manifets yaml files are located in the /kubernetes folder

## **Scalability Strategy**

### **Horizontal Pod Autoscaling (HPA)**

- **HPA** automatically adjusts the number of replicas for each service based on CPU and memory utilization.
- **API Layer**, **Ingestion Service**, and **Query Service** all scale horizontally to handle high traffic.

#### **Key HPA Configuration:**
- **API Layer**: Routes external requests and scales based on CPU (50% utilization) and memory (60% utilization).
- **Ingestion Service**: Scales based on data ingestion load.
- **Query Service**: Scales based on read-heavy queries and resource usage.

### **Kubernetes Horizontal Pod Autoscaling (HPA) Configuration Examples**

#### **API Layer HPA**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-layer-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-layer
  minReplicas: 3
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 60
```

## **Scalability Considerations**

* API Layer: Scales based on external traffic, ensuring high availability through Ingress and HPA.
* Ingestion Service: Scales when handling large amounts of incoming data, ensuring efficient data processing.
* Query Service: Scales for read-heavy workloads, fetching data from MongoDB and Redis, and scaling based on resource usage.



