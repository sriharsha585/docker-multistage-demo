# Docker Multi-Stage Build Demo

## Project Overview

This project demonstrates how to build and optimize Docker images using Multi-Stage Builds.

The objective is to:

* Create a Node.js application
* Build a Docker image using a single-stage Dockerfile
* Optimize the image using a multi-stage Dockerfile
* Reduce image size
* Improve security and deployment efficiency
* Push the final image to Docker Hub

---

## Architecture

```text
Developer
    │
    ▼
Node.js Application
    │
    ▼
Docker Build
    │
    ├── Single Stage Build
    │
    └── Multi Stage Build
            │
            ▼
Optimized Docker Image
            │
            ▼
Docker Hub
```

---

## Technologies Used

* Docker
* Node.js
* Docker Hub
* Git
* Linux

---

## Project Structure

```text
docker-multistage-demo/
│
├── app.js
├── package.json
├── Dockerfile.single
├── Dockerfile
├── .dockerignore
└── README.md
```

---

## Application Code

### app.js

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.end('Hello Docker');
});

server.listen(3000);
```

---

### package.json

```json
{
  "name": "docker-demo",
  "version": "1.0.0",
  "main": "app.js"
}
```

---

# Single-Stage Docker Build

## Dockerfile.single

```dockerfile
FROM node:22

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 3000

CMD ["node","app.js"]
```

---

## Build Image

```bash
docker build \
-f Dockerfile.single \
-t node-single .
```

---

## Verify Image

```bash
docker images
```

Example:

```text
REPOSITORY     TAG       SIZE
node-single    latest    1.1GB
```

---

## Problems with Single Stage Build

* Large image size
* Includes build dependencies
* Includes package manager cache
* Increased attack surface
* Slower deployments

---

# Multi-Stage Docker Build

## Dockerfile

```dockerfile
# Build Stage

FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

# Runtime Stage

FROM node:22-alpine

WORKDIR /app

COPY --from=builder /app .

EXPOSE 3000

CMD ["node","app.js"]
```

---

## Build Optimized Image

```bash
docker build -t node-multistage .
```

---

## Verify Image Size

```bash
docker images
```

Example:

```text
REPOSITORY          TAG       SIZE
node-single         latest    1.1GB
node-multistage     latest    180MB
```

---

## Benefits of Multi-Stage Build

* Smaller image size
* Faster image downloads
* Reduced attack surface
* Faster deployments
* Lower storage consumption
* Better production readiness

---

# Docker Ignore

## .dockerignore

```text
node_modules
.git
.github
README.md
```

---

## Why Use .dockerignore?

Without .dockerignore:

```text
Docker sends:
- node_modules
- Git history
- Logs
- Temporary files
```

Result:

```text
Large build context
Slower builds
```

With .dockerignore:

```text
Only required files are sent
```

Result:

```text
Smaller context
Faster builds
```

---

# Running the Container

## Start Container

```bash
docker run -d -p 3000:3000 node-multistage
```

---

## Verify

```bash
curl localhost:3000
```

Expected:

```text
Hello Docker
```

---

# Docker Hub Integration

## Login

```bash
docker login
```

---

## Tag Image

```bash
docker tag node-multistage nandam585/node-multistage:v1
```

---

## Push Image

```bash
docker push nandam585/node-multistage:v1
```

---

## Verify

Check Docker Hub repository:

```text
nandam585/node-multistage
```

---

# Key Learnings

* Docker Image Creation
* Dockerfile Optimization
* Multi-Stage Builds
* Docker Image Size Reduction
* Docker Hub Integration
* Build Context Optimization
* .dockerignore Usage
* Container Runtime Concepts

---

# Challenges Faced

## Git Push Failure

Error:

```text
src refspec main does not match any
```

Cause:

No commits existed in the repository.

Resolution:

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

---

## Docker Build Optimization

Challenge:

Large Docker image size.

Resolution:

Implemented Multi-Stage Build and Alpine runtime image.

---

# Interview Questions

## What is a Multi-Stage Build?

A multi-stage build allows multiple FROM statements in a Dockerfile. Build tools and dependencies remain in the build stage, while only required runtime artifacts are copied to the final image.

---

## Why use Multi-Stage Builds?

* Reduce image size
* Improve security
* Improve deployment speed
* Remove unnecessary build dependencies

---

## Why use Alpine Images?

Alpine Linux images are lightweight and significantly smaller than standard Linux images, making containers faster to download and deploy.

---

## Why use .dockerignore?

.dockerignore prevents unnecessary files from being copied into the Docker build context, improving build performance and reducing image size.

---

## Difference Between Single-Stage and Multi-Stage Builds?

| Single Stage         | Multi Stage        |
| -------------------- | ------------------ |
| Large image size     | Smaller image size |
| Includes build tools | Runtime only       |
| Less secure          | More secure        |
| Slower deployments   | Faster deployments |

---

# Production Use Case

```text
Developer
    │
    ▼
GitHub
    │
    ▼
GitHub Actions
    │
    ▼
Docker Multi-Stage Build
    │
    ▼
Docker Hub / Amazon ECR
    │
    ▼
Argo CD
    │
    ▼
Kubernetes
```

This approach is commonly used in modern DevOps and Kubernetes environments.

---

## Author

Sai Sri Harsha Nandam

DevOps Engineer

Skills:
AWS | Docker | Kubernetes | GitHub Actions | Jenkins | Argo CD | Terraform | Ansible | Linux
