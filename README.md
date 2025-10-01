# Cow Wisdom Web Server (Wisecow)

## Overview

**Wisecow** is a fun web server that displays random cow wisdom using `fortune-mod` and `cowsay`. This repository now includes a **fully containerized version** deployed on **Kubernetes**, with **CI/CD automation** using GitHub Actions and **TLS-secured communication**.

---

## Prerequisites

To run locally (without Docker/Kubernetes):

```
sudo apt install fortune-mod cowsay -y
```

---

## How to Use?

### 1️⃣ Local Execution

```
./wisecow.sh
```

* Open a browser and go to `http://localhost:4499`
* You will see random cow wisdom displayed

### 2️⃣ Kubernetes Deployment

1. Clone the repo:

```
git clone https://github.com/Gayathri13k/wisecow.git
cd wisecow
```

2. Start Minikube or Kind:

```
minikube start
# OR for Kind
kind create cluster
```

3. Apply Kubernetes manifests:

```
kubectl apply -f k8s/wisecow-service.yaml
kubectl apply -f k8s/wisecow-deployment.yaml
kubectl apply -f k8s/wisecow-ingress.yaml
```

4. Verify deployment:

```
kubectl get pods
kubectl get svc
kubectl get ingress
kubectl rollout status deployment/wisecow-deployment
```

---

## Dockerization

* Dockerfile is included in the repository.
* Build the image locally:

```
docker build -t wisecow:ci .
```

* Run locally:

```
docker run -p 4499:4499 wisecow:ci
```

---

## CI/CD Pipeline

* Fully automated **GitHub Actions workflow** (`.github/workflows/ci-cd.yml`) is included.

* Workflow steps:

  1. Checkout repository
  2. Build Docker image
  3. Set up Kind cluster in CI
  4. Load Docker image into Kind
  5. Apply Kubernetes manifests
  6. Create TLS secret for HTTPS
  7. Smoke test to verify the application

* Triggered automatically on **push to `main` branch**.

---

## TLS / HTTPS

* Application supports **TLS-secured HTTPS**.
* A self-signed certificate is automatically created during the CI/CD workflow:

```
kubectl create secret tls wisecow-tls --key tls.key --cert tls.crt
```

* Ingress host: `wisecow.default.svc.cluster.local`
* For production, a real certificate can be provisioned using **cert-manager** and Let’s Encrypt.

---

## Testing the Deployment

* Run a quick smoke test inside the cluster:

```
kubectl run curlpod --image=radial/busyboxplus:curl --restart=Never -- curl -k https://wisecow.default.svc.cluster.local/
kubectl delete pod curlpod
```

* The output should display the Wisecow web page content over HTTPS.

---

## Project Structure

```
wisecow/
├── .github/                   # GitHub Actions workflows
│   └── workflows/
│       └── ci-cd.yml          # CI/CD workflow
├── k8s/                       # Kubernetes manifests
│   ├── wisecow-deployment.yaml
│   ├── wisecow-service.yaml
│   └── wisecow-ingress.yaml
├── Dockerfile                 # Docker image definition
├── wisecow.sh                 # Start script
├── .dockerignore
├── LICENSE
└── README.md
```

---

## Screenshots

![alt text](<wisecom 1.PNG>)

![alt text](<wisecow 2.PNG>)

---


