# **Containerizing the Application**
Each microservice is containerized using **Docker**, ensuring a consistent and portable deployment environment.

### **Dockerfile Structure**
A standard Dockerfile is used for the **API Layer, Ingestion Service, and Query Service**. Below is a generic example for an Express.js-based microservice:

```dockerfile
# Use official Node.js base image
FROM node:18-alpine

# Set working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install --only=production

# Copy application source code
COPY . .

# Expose service port
EXPOSE 3000

# Command to run the application
CMD ["node", "server.js"]
```

### **Multi-Container Setup with Docker Compose**
For local development and testing, **Docker Compose** is used to define and run multiple containers. Below is an example configuration:

```yaml
version: '3.8'

services:
  api-layer:
    build: ./api-layer
    ports:
      - "8080:3000"
    depends_on:
      - ingestion-service
      - query-service
    environment:
      - NODE_ENV=production

  ingestion-service:
    build: ./ingestion-service
    depends_on:
      - kafka
    environment:
      - NODE_ENV=production

  query-service:
    build: ./query-service
    depends_on:
      - redis
      - mongodb
    environment:
      - NODE_ENV=production

  kafka:
    image: bitnami/kafka:latest
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
    ports:
      - "9092:9092"

  redis:
    image: redis:latest
    ports:
      - "6379:6379"

  mongodb:
    image: mongo:latest
    ports:
      - "27017:27017"
```

## **Key Considerations**
- **Minimal Base Image**: Using `node:18-alpine` to keep images lightweight.
- **Dependency Management**: Only installing production dependencies to reduce image size.
- **Port Exposure**: Mapping necessary ports for inter-service communication.
- **Service Dependencies**: Ensuring proper startup order using `depends_on`.
