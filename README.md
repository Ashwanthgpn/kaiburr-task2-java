# 🚀 Kaiburr Task-2 — Spring Boot + Docker + Kubernetes

**Author:** Ashwanth GPN  

Implements **Task-2**: deploys the Spring Boot + MongoDB application inside Kubernetes using Docker, Minikube, and NodePort exposure.  
Includes containerization, persistent MongoDB PVC, K8s manifests, and dynamic execution via BusyBox pods.

---

## 🧰 Prerequisites

Before starting, make sure these are installed and running locally:

- **Java 17**
- **Maven 3.9+**
- **Docker Desktop**
- **Minikube** (using Docker driver)
- **kubectl CLI**

Verify installation:

```powershell
docker --version
kubectl version --client
minikube version
📸 Screenshot 1 — Versions:
![Versions](screens/01-versions.png)



⚙️ Step 1 — Start Minikube and Cluster Setup
minikube start --driver=docker
minikube ip


📸 Screenshot 2 — Start Minikube


🧱 Step 2 — Deploy MongoDB and Application

From the project root (E:\Kaiburr\kaiburr-task2-java):

kubectl apply -f k8s/00-namespace.yaml
kubectl apply -f k8s/10-mongo-pvc.yaml
kubectl apply -f k8s/11-mongo-deploy.yaml
kubectl apply -f k8s/20-app-deploy.yaml


Check resources:

kubectl -n kaiburr get pods,svc
kubectl -n kaiburr get pvc


📸 Screenshot 3 — Pods and Services


📸 Screenshot 4 — PVC


🐳 Step 3 — Build Docker Image and Load into Minikube
docker build -t ashwanthgpn/kaiburr-task2:latest .
minikube image load ashwanthgpn/kaiburr-task2:latest

🌐 Step 4 — Expose the Service and Get URL
minikube service -n kaiburr kaiburr-app --url


Example output:

http://127.0.0.1:55029


📸 Screenshot 5 — Service URL


🧪 Step 5 — Test the APIs

Set your base URL (replace with the one shown by Minikube):

$BASE = "http://127.0.0.1:55029"

✅ Health Check
curl.exe "$BASE/api/health"


📸 Screenshot 6 — Health Endpoint


📝 Create / Update a Task
$r = Invoke-RestMethod -Method Put -Uri "$BASE/api/tasks" `
  -ContentType "application/json" `
  -Body '{"name":"k8s-task","owner":"Ashwanth","command":"echo Hi from K8s pod"}'
$ID = $r.id
$r


📸 Screenshot 7 — PUT Task


⚡ Run Execution (BusyBox Pod)

Runs the command inside a temporary BusyBox pod and stores output in MongoDB.

Invoke-RestMethod -Method Put -Uri "$BASE/api/tasks/$ID/executions" `
  -ContentType "application/json" `
  -Body '{"command":"echo Hello from busybox"}'


📸 Screenshot 8 — PUT Execution (K8s)


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


📸 Screenshot 9 — Task with Executions


🧩 Architecture Overview
Component	Type	Description
Spring Boot App	Deployment	REST API for managing tasks and executions
MongoDB	Deployment + PVC	Persistent task storage
BusyBox Pod	Ephemeral	Executes commands securely in K8s
NodePort Service	Service	Exposes API to localhost via Minikube
🧠 Command Policy (Safety)

Allowed commands only:

echo ...

java -version

mvn -v

Others return:

{"error":"Command not allowed by policy"}

✅ Validation Checklist

✔ /api/health → OK
✔ /api/tasks → Creates/updates task
✔ /api/tasks/{id}/executions → Runs BusyBox pod and stores output
✔ /api/tasks?id={id} → Shows execution history from MongoDB

All components verified within Kubernetes (Minikube) environment.

🏁 Conclusion

Task-2 Successfully Completed

Spring Boot App containerized ✅

MongoDB persistent storage ✅

Kubernetes deployment and NodePort service ✅

BusyBox pod execution and MongoDB log storage ✅

End-to-end tests from PowerShell ✅

