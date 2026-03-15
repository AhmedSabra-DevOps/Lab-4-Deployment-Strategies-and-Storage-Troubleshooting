
# Kubernetes Lab 4 – Deployment Strategies and Storage Troubleshooting

## Lab Summary
This lab demonstrates how Kubernetes handles ephemeral storage using `emptyDir`, and how to perform a Blue‑Green deployment strategy using Deployments and Services.
The application is a simple logging container that continuously writes timestamps to a file.

---

## Storage Behavior Explanation

### Container Crash and Restart
When a container crashes inside a Pod:
- Kubernetes restarts the container automatically.
- The `emptyDir` volume remains intact.
- Data written to the volume persists after container restart.

### Pod Deletion and Recreation
When a Pod is deleted:
- The `emptyDir` volume is deleted with the Pod.
- A new Pod created by the Deployment gets a fresh empty volume.

---

## Blue‑Green Deployment Steps
1. Deploy logger v1.
2. Create a service selecting version v1.
3. Deploy logger v2.
4. Change service selector to version v2.
5. Verify traffic routes to v2 pods.

---

## Troubleshooting

Problem: Pod entered CrashLoopBackOff.

Cause:
Application writes logs to `/log/output.txt` but the volume was mounted at the wrong path.

Diagnosis commands:

kubectl get pods -n staging
kubectl logs POD_NAME -n staging
kubectl describe pod POD_NAME -n staging

Fix:
Change the volume mount path to `/log` and reapply the deployment.

---

## Commands Used

kubectl create namespace staging
kubectl apply -f manifests/v1/logger-deployment.yaml
kubectl apply -f manifests/service/logger-service.yaml
kubectl apply -f manifests/v2/logger-deployment.yaml

kubectl get pods -n staging
kubectl logs POD_NAME -n staging
kubectl describe pod POD_NAME -n staging

---

## Project Structure

k8s-lab4-blue-green
│
├── README.md
├── manifests
│   ├── v1
│   │   └── logger-deployment.yaml
│   ├── v2
│   │   └── logger-deployment.yaml
│   └── service
│       └── logger-service.yaml
└── architecture
    └── architecture.png
