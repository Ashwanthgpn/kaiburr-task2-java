Kaiburr Task 2 — Containerized + Kubernetes

*Author:* Ashwanth GPN

Task-2 runs the Task-1 Spring Boot app in Docker and deploys it to Kubernetes (minikube/kind).
MongoDB runs as a Kubernetes Deployment + PVC. App connects via env var.

## Prerequisites
- Docker Desktop (daemon running)
- kubectl
- minikube (or kind)
- Java 17, Maven 3.9+ (to build locally if needed)

## Build Docker image (local)
```powershell
mvn clean package -DskipTests
docker build -t ashwanthgpn/kaiburr-task2:latest .