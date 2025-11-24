# Zarish FHIR Infrastructure

**Part of the ZarishSphere Platform - A No-Code FHIR Healthcare Data Management System**

This repository contains the infrastructure configuration for deploying the ZarishSphere Platform, including Docker Compose files and Kubernetes manifests for orchestrating all services.

## 🚀 Technology Stack

- **Containerization**: Docker & Docker Compose
- **Orchestration**: Kubernetes
- **Services Included**:
  - Zarish FHIR Server
  - Zarish Terminology Server
  - PostgreSQL Database
  - Keycloak Authentication
  - Elasticsearch (optional)

## 📋 Prerequisites

- **Docker**: Version 20+ ([Download Docker](https://www.docker.com/))
- **Docker Compose**: Version 2+ (included with Docker Desktop)
- **Kubernetes** (optional): For production deployment
  - kubectl CLI tool
  - Minikube (for local testing) or cloud provider (AWS EKS, GKE, AKS)
- **Git**: For version control

### Checking Your Installation

```bash
docker --version          # Should show 20.x or higher
docker-compose --version  # Should show 2.x or higher
kubectl version          # Should show client and server versions
```

## 🛠️ Step-by-Step Development Setup

### Step 1: Clone the Repository

```bash
cd ~/Desktop
git clone https://github.com/ZarishSphere-Platform/zarish-fhir-infra.git
cd zarish-fhir-infra
```

### Step 2: Clone Related Repositories

The infrastructure expects the following repositories to be cloned in the parent directory:

```bash
cd ~/Desktop
git clone https://github.com/ZarishSphere-Platform/zarish-fhir-server.git
git clone https://github.com/ZarishSphere-Platform/zarish-terminology-server.git
git clone https://github.com/ZarishSphere-Platform/zarish-frontend-shell.git
```

Your directory structure should look like:

```
~/Desktop/
├── zarish-fhir-infra/
├── zarish-fhir-server/
├── zarish-terminology-server/
└── zarish-frontend-shell/
```

### Step 3: Configure Environment Variables

Create a `.env` file in the `docker` directory:

```bash
cd docker
touch .env
```

Add the following configuration:

```env
# PostgreSQL
POSTGRES_DB=zarish_fhir
POSTGRES_USER=zarish
POSTGRES_PASSWORD=your_secure_password

# Keycloak
KEYCLOAK_ADMIN=admin
KEYCLOAK_ADMIN_PASSWORD=admin_password

# Application
FHIR_SERVER_PORT=8081
TERMINOLOGY_SERVER_PORT=8082
FRONTEND_PORT=3000
```

### Step 4: Start Services with Docker Compose

```bash
# Navigate to docker directory
cd docker

# Start all services
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f
```

### Step 5: Access the Services

Once all services are running:

- **Frontend**: http://localhost:3000
- **FHIR Server**: http://localhost:8081/fhir
- **Terminology Server**: http://localhost:8082/fhir
- **Keycloak**: http://localhost:8080
- **PostgreSQL**: localhost:5432

### Step 6: Initialize Keycloak

1. Open http://localhost:8080
2. Login with admin credentials
3. Create realm: `zarish`
4. Create clients for each service
5. Configure client settings

## 📁 Project Structure

```
zarish-fhir-infra/
├── docker/
│   ├── docker-compose.yml        # Docker Compose configuration
│   ├── zarish-fhir-server/      # FHIR server Docker config
│   ├── zarish-terminology-server/ # Terminology server config
│   └── .env                      # Environment variables
├── k8s/
│   ├── namespace.yaml            # Kubernetes namespace
│   ├── postgres-deployment.yaml  # PostgreSQL deployment
│   ├── keycloak-deployment.yaml  # Keycloak deployment
│   ├── fhir-server-deployment.yaml
│   ├── terminology-server-deployment.yaml
│   ├── frontend-deployment.yaml
│   └── ingress.yaml              # Ingress configuration
└── README.md
```

## 🔧 Docker Compose Commands

| Command | Description |
|---------|-------------|
| `docker-compose up -d` | Start all services in background |
| `docker-compose down` | Stop all services |
| `docker-compose ps` | List running services |
| `docker-compose logs -f [service]` | View service logs |
| `docker-compose restart [service]` | Restart a service |
| `docker-compose build` | Rebuild images |

## ☸️ Kubernetes Deployment

### Local Deployment (Minikube)

```bash
# Start Minikube
minikube start

# Create namespace
kubectl apply -f k8s/namespace.yaml

# Deploy PostgreSQL
kubectl apply -f k8s/postgres-deployment.yaml

# Deploy Keycloak
kubectl apply -f k8s/keycloak-deployment.yaml

# Deploy FHIR Server
kubectl apply -f k8s/fhir-server-deployment.yaml

# Deploy Terminology Server
kubectl apply -f k8s/terminology-server-deployment.yaml

# Deploy Frontend
kubectl apply -f k8s/frontend-deployment.yaml

# Setup Ingress
kubectl apply -f k8s/ingress.yaml

# Check deployments
kubectl get pods -n zarish-platform
kubectl get services -n zarish-platform
```

### Production Deployment

For production deployment on cloud providers:

1. Update image references in deployment files
2. Configure secrets for sensitive data
3. Setup persistent volumes for databases
4. Configure ingress with TLS certificates
5. Setup monitoring and logging

## 🔍 Service Health Checks

```bash
# Check FHIR Server
curl http://localhost:8081/health

# Check Terminology Server
curl http://localhost:8082/health

# Check PostgreSQL
docker-compose exec postgres pg_isready

# Check Keycloak
curl http://localhost:8080/health
```

## 🐛 Troubleshooting

### Services Won't Start

```bash
# Check Docker daemon
docker info

# Check logs
docker-compose logs [service-name]

# Remove containers and restart
docker-compose down
docker-compose up -d
```

### Port Conflicts

```bash
# Check what's using a port
lsof -i :8081

# Kill the process
kill -9 <PID>

# Or change ports in .env file
```

### Database Connection Issues

```bash
# Access PostgreSQL container
docker-compose exec postgres psql -U zarish -d zarish_fhir

# Check database exists
\l

# Check tables
\dt
```

### Kubernetes Pod Issues

```bash
# Describe pod
kubectl describe pod <pod-name> -n zarish-platform

# View logs
kubectl logs <pod-name> -n zarish-platform

# Get events
kubectl get events -n zarish-platform
```

## 🔄 Updating Services

### Update Docker Images

```bash
# Pull latest images
docker-compose pull

# Rebuild and restart
docker-compose up -d --build
```

### Update Kubernetes Deployments

```bash
# Apply updated manifests
kubectl apply -f k8s/

# Rollout restart
kubectl rollout restart deployment/fhir-server -n zarish-platform
```

## 📚 Learning Resources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Keycloak Documentation](https://www.keycloak.org/documentation)

## 🤝 Contributing

1. Test changes locally with Docker Compose
2. Validate Kubernetes manifests
3. Update documentation
4. Submit pull request

## 🔗 Related Repositories

- [zarish-fhir-server](https://github.com/ZarishSphere-Platform/zarish-fhir-server)
- [zarish-terminology-server](https://github.com/ZarishSphere-Platform/zarish-terminology-server)
- [zarish-frontend-shell](https://github.com/ZarishSphere-Platform/zarish-frontend-shell)
- [zarish-fhir-data](https://github.com/ZarishSphere-Platform/zarish-fhir-data)

## 🆘 Getting Help

- Check [Issues](https://github.com/ZarishSphere-Platform/zarish-fhir-infra/issues)
- Create new issue with:
  - Service logs
  - Configuration files
  - Steps to reproduce
