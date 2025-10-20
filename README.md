# 🚀 Kaiburr Task-2 — Spring Boot + Docker + Kubernetes

**Author:** Ashwanth GPN  

Implements **Task-2**: Spring Boot + MongoDB app deployed inside Kubernetes using Docker & Minikube with NodePort exposure, persistent PVC, and dynamic BusyBox executions.

---

## 🧰 Prerequisites

Install and verify:

- Java 17  
- Maven 3.9+  
- Docker Desktop  
- Minikube (Docker driver)  
- kubectl CLI  

```powershell
docker --version
kubectl version --client
minikube version

⚙️ Step 1 — Start Minikube and Cluster Setup
minikube start --driver=docker
minikube ip


🧱 Step 2 — Deploy MongoDB and Application
kubectl apply -f k8s/00-namespace.yaml
kubectl apply -f k8s/10-mongo-pvc.yaml
kubectl apply -f k8s/11-mongo-deploy.yaml
kubectl apply -f k8s/20-app-deploy.yaml
kubectl -n kaiburr get pods,svc
kubectl -n kaiburr get pvc





🐳 Step 3 — Build Docker Image and Load into Minikube
docker build -t ashwanthgpn/kaiburr-task2:latest .
minikube image load ashwanthgpn/kaiburr-task2:latest

🌐 Step 4 — Expose the Service and Get URL
minikube service -n kaiburr kaiburr-app --url


Example: http://127.0.0.1:55029

🧪 Step 5 — Test the APIs

Set base URL:

$BASE = "http://127.0.0.1:55029"

✅ Health Check
curl.exe "$BASE/api/health"


📝 Create / Update a Task
$r = Invoke-RestMethod -Method Put -Uri "$BASE/api/tasks" `
  -ContentType "application/json" `
  -Body '{"name":"k8s-task","owner":"Ashwanth","command":"echo Hi from K8s pod"}'
$ID = $r.id
$r


⚡ Run Execution (BusyBox Pod)
Invoke-RestMethod -Method Put -Uri "$BASE/api/tasks/$ID/executions" `
  -ContentType "application/json" `
  -Body '{"command":"echo Hello from busybox"}'


🔎 Verify Task and Execution History
Invoke-RestMethod -Uri "$BASE/api/tasks?id=$ID" | ConvertTo-Json -Depth 6


Expected output:

{
  "id": "68f64473d9b41f7531c6bf96",
  "name": "k8s-task",
  "owner": "Ashwanth",
  "command": "echo Hi from K8s pod",
  "taskExecutions": [
    {
      "startTime": "2025-10-20T14:17:23.940+00:00",
      "endTime": "2025-10-20T14:17:23.955+00:00",
      "output": "Hello from busybox\n"
    }
  ]
}


🧩 Architecture Overview
Component	Type	Description
Spring Boot App	Deployment	REST API for task management
MongoDB	Deployment + PVC	Persistent task storage
BusyBox Pod	Ephemeral	Executes commands securely
NodePort Service	Service	Exposes API to localhost
🧠 Command Policy (Safety)

Allowed commands only:

echo ...

java -version

mvn -v

Others return {"error":"Command not allowed by policy"}.

✅ Validation Checklist

✔ /api/health OK
✔ /api/tasks creates task
✔ /api/tasks/{id}/executions runs BusyBox pod
✔ /api/tasks?id={id} shows execution history

All verified in Minikube Kubernetes cluster.

🏁 Conclusion

Task-2 Successfully Completed

✅ Spring Boot App containerized

✅ MongoDB PVC bound

✅ K8s deployment + NodePort service

✅ BusyBox execution stored in MongoDB

✅ Full E2E validation done from PowerShell