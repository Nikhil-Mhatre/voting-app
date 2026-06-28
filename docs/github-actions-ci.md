# GitHub Actions - Docker CI Setup

This repository uses **GitHub Actions** to automatically build and publish Docker images for each microservice.

## CI Pipeline Overview

Whenever code is pushed to one of the service directories:

* `vote/`
* `result/`
* `worker/`

GitHub Actions will:

1. Detect which service changed.
2. Build the corresponding Docker image.
3. Push the image to Docker Hub.
4. Update the Kubernetes deployment manifest with the new image tag.
5. Commit the updated manifest back to the repository.

The deployment manifest update is intended for GitOps tools such as **Argo CD**, which automatically synchronize the latest changes to the Kubernetes cluster.

---

# Prerequisites

Before using the CI pipeline, ensure you have:

* Visual Studio Code (or your preferred editor)
* Git installed
* SSH key configured with GitHub
* Docker Hub account

---

# Clone the Repository

```bash
git clone git@github.com:<YOUR_GITHUB_USERNAME>/voting-app.git
cd voting-app
```

---

# Create a Docker Hub Personal Access Token

1. Log in to Docker Hub.
2. Navigate to:

```
https://app.docker.com/settings/personal-access-tokens
```

3. Click **Generate New Token**.
4. Give the token a meaningful name (e.g., `GitHub Actions`).
5. Copy the generated token and store it safely.

> **Note:** The token is displayed only once.

---

# Configure GitHub Repository Secrets

Open your GitHub repository.

Navigate to:

```
Settings
└── Secrets and variables
    └── Actions
```

Create the following repository secrets:

| Secret Name       | Description                           |
| ----------------- | ------------------------------------- |
| `DOCKER_USERNAME` | Your Docker Hub username              |
| `DOCKER_PASSWORD` | Your Docker Hub Personal Access Token |

These secrets are consumed by the reusable GitHub Actions workflow to authenticate with Docker Hub.

---

# Configure GitHub Actions Permissions

Navigate to:

```
Settings
└── Actions
    └── General
```

Under **Workflow permissions**, select:

✅ Read and write permissions

Also enable:

✅ Allow GitHub Actions to create and approve pull requests

Although this workflow commits directly to the repository, enabling write permissions allows the GitHub Actions bot to update Kubernetes manifests.

---

# Repository Workflow Structure

The CI pipeline uses a reusable workflow to eliminate duplicated logic.

```
.github/
└── workflows/
    ├── build-push-update.yml    # Reusable CI workflow
    ├── vote.yml                 # Vote service trigger
    ├── result.yml               # Result service trigger
    └── worker.yml               # Worker service trigger
```

* `vote.yml`
* `result.yml`
* `worker.yml`

only determine **when** the pipeline should run.

All build, push, and manifest update logic resides in:

```
.github/workflows/build-push-update.yml
```

This makes it easy to onboard additional microservices by creating a small trigger workflow without duplicating CI logic.

---

# Repository Layout

The reusable workflow expects the following project structure:

```
vote/
    Dockerfile

result/
    Dockerfile

worker/
    Dockerfile

k8s-specifications/
    vote-deployment.yaml
    result-deployment.yaml
    worker-deployment.yaml
```

Each deployment manifest must follow the naming convention:

```
<service-name>-deployment.yaml
```

---

# Adding a New Microservice

To add another service:

1. Create the service directory.

```
payment/
    Dockerfile
```

2. Create its Kubernetes deployment manifest.

```
k8s-specifications/
    payment-deployment.yaml
```

3. Add a workflow trigger:

```
.github/workflows/payment.yml
```

The trigger only needs to pass the service name to the reusable workflow.

No additional CI logic is required.

---

# Automated Git Commits

Manifest updates are committed automatically using the GitHub Actions bot:

```
github-actions[bot]
```

Example commit message:

```
chore(k8s): update vote image to 704d558
```

This keeps deployment commits separate from developer commits and provides a clear audit trail.

---

# Notes

* Docker images are tagged using the short Git commit SHA.
* Only the modified service is rebuilt.
* The corresponding Kubernetes manifest is updated automatically.
* The reusable workflow validates the repository structure before starting the build.
* Concurrent builds are supported, while manifest updates are safely committed back to the repository.

Once the repository secrets and permissions are configured, every push to a service directory automatically builds and publishes a new Docker image.
