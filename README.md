# Sample Voting App - DevOps CI/CD Demo

A distributed microservices application demonstrating a complete cloud-native CI/CD workflow using **GitHub Actions**, **Docker**, **Kubernetes**, and **GitOps**.

---

## Project Overview

This project is based on Docker's Sample Voting App and has been enhanced to showcase modern DevOps practices.

The repository demonstrates:

- Docker image creation
- Reusable GitHub Actions workflows
- Docker Hub image publishing
- Automated Kubernetes manifest updates
- GitOps-ready deployment workflow
- Kubernetes deployments

Future enhancements include:

- Argo CD Continuous Deployment
- Azure Kubernetes Service (AKS)
- Infrastructure provisioning with Terraform

---

## Architecture

![Architecture diagram](architecture.excalidraw.png)

The application consists of five services:

| Service  | Technology | Purpose                                       |
| -------- | ---------- | --------------------------------------------- |
| Vote     | Python     | Accepts user votes                            |
| Redis    | Redis      | Temporary message queue                       |
| Worker   | .NET       | Processes votes and stores them in PostgreSQL |
| Postgres | PostgreSQL | Persistent storage                            |
| Result   | Node.js    | Displays live voting results                  |

---

## Repository Structure

```text
.
├── vote/
├── worker/
├── result/
├── k8s-specifications/
├── .github/
│   └── workflows/
│       ├── build-push-update.yml
│       ├── vote.yml
│       ├── worker.yml
│       └── result.yml
└── docker-compose.yml
```

---

# Running the Application Locally

## Using Docker Compose

Build and start all services:

```bash
docker compose up
```

Access the application:

| Service | URL                   |
| ------- | --------------------- |
| Vote    | http://localhost:8080 |
| Result  | http://localhost:8081 |

---

## Running on Kubernetes

Deploy all Kubernetes resources:

```bash
kubectl apply -f k8s-specifications/
```

Application endpoints:

| Service | NodePort |
| ------- | -------- |
| Vote    | 31000    |
| Result  | 31001    |

To remove the application:

```bash
kubectl delete -f k8s-specifications/
```

---

# GitHub Actions CI Pipeline

This repository uses **reusable GitHub Actions workflows** to automate Docker image builds.

Whenever changes are pushed to one of the service folders:

- `vote/`
- `worker/`
- `result/`

GitHub Actions automatically:

1. Detects the modified service.
2. Builds the Docker image.
3. Pushes the image to Docker Hub.
4. Updates the corresponding Kubernetes deployment manifest.
5. Commits the updated manifest back to the repository using the **GitHub Actions bot**.

The workflow is designed around a reusable template so that adding new microservices requires minimal configuration.

---

# CI Workflow Structure

```text
.github/
└── workflows/
    ├── build-push-update.yml
    ├── vote.yml
    ├── worker.yml
    └── result.yml
```

- **build-push-update.yml** contains the reusable CI logic.
- **vote.yml**, **worker.yml**, and **result.yml** are lightweight trigger workflows for each service.

---

# Setting Up GitHub Actions

To use the CI pipeline in your own fork or cloned repository, configure:

- Docker Hub Personal Access Token
- GitHub Repository Secrets
- GitHub Actions permissions

📖 See the detailed setup guide:

**[GitHub Actions CI Guide](docs/github-actions-ci.md)**

This guide explains:

- Creating Docker Hub credentials
- Configuring GitHub Secrets
- Enabling workflow permissions
- Repository structure requirements
- Adding new microservices

---

# Technologies Used

- Python
- Node.js
- .NET
- Redis
- PostgreSQL
- Docker
- Docker Compose
- GitHub Actions
- Docker Hub
- Kubernetes

---

# Acknowledgements

This project is based on the original **Docker Sample Voting App** and has been extended to demonstrate modern DevOps workflows, CI/CD automation, and GitOps practices.
