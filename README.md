# Flight Service - Container Design & Deployment Project

## 📋 Project Overview

This Flight Service is a Spring Boot microservice application designed for the **Container Design & Deployment** assignment (30% weighting). The project demonstrates containerization and orchestration across three platforms: **Docker Compose**, **Docker Swarm**, and **Kubernetes**.

**Submission Date:** Monday, December 15th, 2025, 17:00

---

## 🎯 Assignment Objectives

- Describe the principles of microservices and containerisation
- Deploy and manage applications using Docker Compose
- Implement orchestration with Docker Swarm
- Deploy applications to Kubernetes with scaling, updates, and rollbacks
- Integrate ELK stack for centralized logging (optional)
- Package deployments using Helm (optional)

---

## 🏗️ Architecture

### Application Components
- **Flight Service**: Spring Boot REST API (Port 8080)
- **Database**: MySQL 8.0 (Port 3306)
- **Network**: Bridge/Overlay networking depending on orchestration platform

### API Endpoints
```
GET    /api/flights              - List all flights
GET    /api/flights/{id}         - Get flight by ID
POST   /api/flights              - Create new flight
PUT    /api/flights/{id}         - Update flight
DELETE /api/flights/{id}         - Delete flight
GET    /actuator/health          - Health check endpoint
```

---

## 📦 Prerequisites

### Required Software
- **Java**: JDK 17 or higher
- **Maven**: 3.6+ (or use Maven wrapper)
- **Docker**: 20.10+
- **Docker Compose**: 2.0+
- **Kubernetes**: Minikube or Docker Desktop with Kubernetes enabled
- **Helm**: 3.0+ (optional)
- **Git**: For version control

### Optional Tools
- **Postman**: For API testing
- **Kibana**: For log visualization (ELK stack)
- **kubectl**: Kubernetes CLI

---

## 🚀 Quick Start Guide

### Step 1: Build the JAR File
```bash
# Clone the repository
git clone https://github.com/JohannLieberto/Flight-Service.git
cd Flight-Service

# Build with Maven
mvn clean package -DskipTests

# Or using Maven wrapper
./mvnw clean package -DskipTests

# Verify JAR creation
ls -lh target/*.jar
```

### Step 2: Build Docker Image
```bash
# Build the Docker image
docker build -t flightservice:v1.0 .

# Verify image
docker images | grep flightservice
```

### Step 3: Push to Docker Hub (Optional)
```bash
# Login to Docker Hub
docker login

# Tag the image
docker tag flightservice:v1.0 YOUR_DOCKERHUB_USERNAME/flightservice:v1.0

# Push to Docker Hub
docker push YOUR_DOCKERHUB_USERNAME/flightservice:v1.0
```

---

## 🐳 Deployment Option 1: Docker Compose

### Start the Application
```bash
# Start all services
docker-compose up -d

# Check running containers
docker-compose ps

# View logs
docker-compose logs -f flightservice

# Test the API
curl http://localhost:8080/api/flights
curl http://localhost:8080/actuator/health
```

### Stop the Application
```bash
# Stop all services
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

### Configuration Files
- `docker-compose.yml` - Main compose configuration
- `docker-compose-elk.yml` - Extended config with ELK stack
- `Dockerfile` - Container image definition

---

## 🐝 Deployment Option 2: Docker Swarm

### Initialize Swarm
```bash
# Initialize Docker Swarm
docker swarm init

# Deploy the stack
docker stack deploy -c docker-stack.yml flightapp

# Check services
docker service ls
docker service ps flightapp_flightservice
```

### Scaling Operations
```bash
# Scale to 5 replicas
docker service scale flightapp_flightservice=5

# Verify scaling
docker service ps flightapp_flightservice
```

### Rolling Updates
```bash
# Update to new version
docker service update --image YOUR_USERNAME/flightservice:v2.0 flightapp_flightservice

# Monitor update progress
docker service ps flightapp_flightservice
```

### Rollback
```bash
# Rollback to previous version
docker service rollback flightapp_flightservice

# Verify rollback
docker service ps flightapp_flightservice
```

### Cleanup
```bash
# Remove the stack
docker stack rm flightapp

# Leave swarm (if needed)
docker swarm leave --force
```

---

## ☸️ Deployment Option 3: Kubernetes

### Setup Minikube (if using)
```bash
# Start Minikube
minikube start --driver=docker --memory=4096 --cpus=2

# Enable metrics
minikube addons enable metrics-server

# Check status
kubectl cluster-info
```

### Deploy Application
```bash
# Create namespace and deploy
kubectl apply -f k8s-deployment.yml

# Check all resources
kubectl get all -n flightapp

# Check pods status
kubectl get pods -n flightapp -w

# Check services
kubectl get svc -n flightapp
```

### Access the Application
```bash
# If using LoadBalancer (Docker Desktop)
kubectl get svc flightservice-service -n flightapp

# If using Minikube
minikube service flightservice-service -n flightapp

# Port forwarding alternative
kubectl port-forward -n flightapp svc/flightservice-service 8080:80
```

### Scaling Operations
```bash
# Scale deployment to 5 replicas
kubectl scale deployment flightservice-deployment --replicas=5 -n flightapp

# Verify scaling
kubectl get pods -n flightapp

# Check horizontal pod autoscaler (if configured)
kubectl get hpa -n flightapp
```

### Rolling Updates
```bash
# Update image version
kubectl set image deployment/flightservice-deployment \
  flightservice=YOUR_USERNAME/flightservice:v2.0 -n flightapp

# Check rollout status
kubectl rollout status deployment/flightservice-deployment -n flightapp

# View rollout history
kubectl rollout history deployment/flightservice-deployment -n flightapp
```

### Rollback
```bash
# Rollback to previous version
kubectl rollout undo deployment/flightservice-deployment -n flightapp

# Rollback to specific revision
kubectl rollout undo deployment/flightservice-deployment --to-revision=2 -n flightapp

# Verify rollback
kubectl get pods -n flightapp
```

### Debugging
```bash
# View logs
kubectl logs -f deployment/flightservice-deployment -n flightapp

# Describe pod
kubectl describe pod POD_NAME -n flightapp

# Execute commands in pod
kubectl exec -it POD_NAME -n flightapp -- /bin/sh

# View events
kubectl get events -n flightapp --sort-by='.lastTimestamp'
```

### Cleanup
```bash
# Delete all resources
kubectl delete -f k8s-deployment.yml

# Or delete namespace
kubectl delete namespace flightapp
```

---

## 📊 Optional: Helm Deployment

### Create Helm Chart
```bash
# Generate chart structure
helm create flightservice-chart

# Customize values.yaml with your configurations
# Edit templates as needed
```

### Install with Helm
```bash
# Install the chart
helm install flightapp ./flightservice-chart -n flightapp --create-namespace

# Check release
helm list -n flightapp

# Get release status
helm status flightapp -n flightapp
```

### Upgrade and Rollback
```bash
# Upgrade release
helm upgrade flightapp ./flightservice-chart -n flightapp

# Rollback release
helm rollback flightapp -n flightapp

# View history
helm history flightapp -n flightapp
```

### Uninstall
```bash
# Uninstall release
helm uninstall flightapp -n flightapp
```

---

## 📈 Optional: ELK Stack Integration

### Start ELK Stack
```bash
# Start with ELK services
docker-compose -f docker-compose-elk.yml up -d

# Access Kibana
open http://localhost:5601
```

### Configure Log Forwarding
Application logs are automatically forwarded to Elasticsearch via Logstash and can be visualized in Kibana.

### Kibana Setup
1. Open http://localhost:5601
2. Navigate to **Stack Management** → **Index Patterns**
3. Create index pattern: `flightservice-logs-*`
4. Navigate to **Discover** to view logs

---

## 🧪 Testing the Application

### Health Check
```bash
curl http://localhost:8080/actuator/health
```

### Create a Flight
```bash
curl -X POST http://localhost:8080/api/flights \
  -H "Content-Type: application/json" \
  -d '{
    "flightNumber": "EI123",
    "airline": "Aer Lingus",
    "origin": "Dublin",
    "destination": "London",
    "departureTime": "2025-12-10T10:00:00",
    "arrivalTime": "2025-12-10T11:30:00",
    "price": 99.99,
    "availableSeats": 150
  }'
```

### Get All Flights
```bash
curl http://localhost:8080/api/flights
```

### Get Flight by ID
```bash
curl http://localhost:8080/api/flights/1
```

### Update Flight
```bash
curl -X PUT http://localhost:8080/api/flights/1 \
  -H "Content-Type: application/json" \
  -d '{
    "price": 89.99,
    "availableSeats": 145
  }'
```

### Delete Flight
```bash
curl -X DELETE http://localhost:8080/api/flights/1
```

---

## 📁 Project Structure

```
Flight-Service/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/flightservice/
│   │   │       ├── FlightServiceApplication.java
│   │   │       ├── controller/
│   │   │       │   └── FlightController.java
│   │   │       ├── model/
│   │   │       │   └── Flight.java
│   │   │       ├── repository/
│   │   │       │   └── FlightRepository.java
│   │   │       └── service/
│   │   │           ├── FlightService.java
│   │   │           └── FlightServiceImpl.java
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── application-docker.properties
│   │       └── application-k8s.properties
│   └── test/
├── target/
│   └── flightservice.jar
├── k8s/
│   └── k8s-deployment.yml
├── helm/
│   └── flightservice-chart/
├── logstash/
│   └── logstash.conf
├── logs/
│   └── application.log
├── .github/
│   └── workflows/
├── Dockerfile
├── docker-compose.yml
├── docker-compose-elk.yml
├── docker-stack.yml
├── pom.xml
├── .gitignore
├── .dockerignore
└── README.md
```

---

## 🔧 Configuration Files

### Environment Variables
The application supports the following environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `SPRING_DATASOURCE_URL` | Database connection URL | `jdbc:mysql://localhost:3306/flightdb` |
| `SPRING_DATASOURCE_USERNAME` | Database username | `flightuser` |
| `SPRING_DATASOURCE_PASSWORD` | Database password | `flightpass` |
| `SERVER_PORT` | Application port | `8080` |
| `SPRING_PROFILES_ACTIVE` | Active Spring profile | `default` |

### Database Schema
The application automatically creates the following table:

```sql
CREATE TABLE flights (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    flight_number VARCHAR(10) NOT NULL UNIQUE,
    airline VARCHAR(100) NOT NULL,
    origin VARCHAR(100) NOT NULL,
    destination VARCHAR(100) NOT NULL,
    departure_time DATETIME NOT NULL,
    arrival_time DATETIME NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    available_seats INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

---

## 🎬 Screencast Recording Guide

### Structure (10 minutes max)
1. **Introduction** (0:00-1:00)
   - Project overview
   - Architecture diagram
   - Assignment objectives

2. **Docker Compose Demo** (1:00-3:00)
   - Start services
   - Test API endpoints
   - Show logs
   - Demonstrate ELK integration

3. **Docker Swarm Demo** (3:00-5:00)
   - Deploy stack
   - Scale services
   - Rolling update
   - Rollback demonstration

4. **Kubernetes Demo** (5:00-7:30)
   - Deploy application
   - Scale deployment
   - Rolling update
   - Rollback demonstration
   - Show ConfigMaps/Secrets

5. **Advanced Features** (7:30-9:00)
   - Helm deployment
   - Monitoring with Kibana
   - Health checks

6. **Conclusion** (9:00-10:00)
   - Platform comparison
   - Lessons learned
   - Production recommendations

### Recording Tools
- **OBS Studio** (Free, open-source)
- **Camtasia** (Paid, professional)
- **Loom** (Web-based, easy)
- **Zoom** (Record local)

---

## 📝 Report Structure

### Section 1: Introduction (2 pages)
- Microservices architecture concepts
- Benefits of containerization
- Role of orchestration platforms
- Importance of centralized logging

### Section 2: Docker Compose (3-4 pages)
- What is Docker Compose
- Step-by-step deployment
- Service configuration
- Networking and volumes
- Testing and validation
- Screenshots and code snippets

### Section 3: Docker Swarm (3-4 pages)
- Swarm architecture overview
- Deployment process
- Scaling demonstrations
- Rolling updates and rollbacks
- Service discovery
- Advantages and limitations

### Section 4: Kubernetes (4-5 pages)
- Kubernetes architecture
- Deployment walkthrough
- ConfigMaps and Secrets
- Scaling and autoscaling
- Update strategies
- Helm integration
- Learning curve discussion

### Section 5: Evaluation & Conclusion (2-3 pages)
- Platform comparison table
- Personal reflections
- Production recommendations
- Future improvements

---

## 🎓 AI Tool Usage Attribution

This project utilized AI assistance for:
- Configuration file generation
- Troubleshooting deployment issues
- Documentation structure
- Best practices guidance

**Citation Format:**
> "ChatGPT (OpenAI, 2025). Response to user query on [specific topic]."

---

## 📊 Comparison Table

| Feature | Docker Compose | Docker Swarm | Kubernetes |
|---------|---------------|--------------|------------|
| **Ease of Setup** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Scalability** | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **High Availability** | ⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Learning Curve** | Easy | Moderate | Steep |
| **Production Ready** | Development | Small-Medium | Enterprise |
| **Auto-scaling** | No | Limited | Yes |
| **Self-healing** | No | Yes | Yes |
| **Rolling Updates** | Manual | Built-in | Advanced |
| **Multi-host** | No | Yes | Yes |
| **Community** | Large | Medium | Huge |

---

## 🐛 Troubleshooting

### Common Issues

#### Port Already in Use
```bash
# Find process using port 8080
lsof -i :8080
# Kill the process
kill -9 PID
```

#### Docker Compose Connection Issues
```bash
# Restart Docker
sudo systemctl restart docker
# Recreate containers
docker-compose up -d --force-recreate
```

#### Kubernetes Pod Not Starting
```bash
# Check pod logs
kubectl logs POD_NAME -n flightapp
# Describe pod for events
kubectl describe pod POD_NAME -n flightapp
# Check image pull
kubectl get events -n flightapp
```

#### Database Connection Errors
- Verify database service is running
- Check connection string in environment variables
- Ensure database user has correct permissions
- Wait for database initialization (may take 30-60 seconds)

---

## 📚 Additional Resources

### Documentation
- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Reference](https://docs.docker.com/compose/)
- [Docker Swarm Guide](https://docs.docker.com/engine/swarm/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Helm Documentation](https://helm.sh/docs/)

### Tutorials
- [Docker Getting Started](https://docs.docker.com/get-started/)
- [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Helm Charts Guide](https://helm.sh/docs/chart_template_guide/)

---

## 👨‍💻 Author

**Your Name**
- Email: your.email@example.com
- Student ID: YOUR_ID
- Course: Container Design & Deployment 2025/26

---

## 📜 License

This project is created for educational purposes as part of the Container Design & Deployment assignment.

---

## 🙏 Acknowledgments

- Spring Boot framework team
- Docker community
- Kubernetes community
- Course instructor and TAs
- ChatGPT (OpenAI, 2025) for deployment configuration assistance

---

## 📅 Version History

| Version | Date | Changes |
|---------|------|---------|
| v1.0 | 2025-11-07 | Initial release with Docker Compose |
| v1.1 | TBD | Added Docker Swarm support |
| v1.2 | TBD | Added Kubernetes deployment |
| v2.0 | TBD | Added ELK stack and Helm charts |

---

**Good luck with your project! 🚀**

For questions or issues, please create an issue in the GitHub repository.
