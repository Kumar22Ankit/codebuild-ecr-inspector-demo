
**🚀 codebuild-ecr-inspector-demo**

This project demonstrates how to **automate container image scanning** in AWS using **CodeBuild**, **Amazon ECR**, and **Amazon Inspector**.  
It shows how to integrate vulnerability scanning into a CI/CD pipeline to improve container security.

---

## 🧭 Overview

**Workflow:**
1. CodeBuild builds a Docker image.
2. The image is pushed to Amazon ECR.
3. Amazon Inspector automatically scans the image for vulnerabilities.
4. Findings are displayed in the Inspector console.

---

## 🧱 Architecture

```text
Developer → AWS CodeBuild → Amazon ECR → Amazon Inspector → Findings
````

---

## ⚙️ Prerequisites

* AWS account (Free Tier)
* AWS CLI configured
* IAM user/role with access to ECR, CodeBuild, and Inspector
* Basic understanding of Docker

---

## 🪄 Setup Steps

### 1. Create ECR Repository

```bash
aws ecr create-repository --repository-name inspector-demo-repo
```

### 2. Enable Amazon Inspector

In AWS Console → **Amazon Inspector** → **Activate** → Enable **ECR image scanning**.

### 3. Create IAM Role for CodeBuild

Attach policies:

* `AmazonEC2ContainerRegistryFullAccess`
* `AmazonInspector2FullAccess`
* `CloudWatchLogsFullAccess`

### 4. Add Files to Your Repo

* `Dockerfile`
* `app.js` (simple Node.js app)
* `package.json`
* `buildspec.yml` (see below)

### 5. Sample `buildspec.yml`

```yaml
version: 0.2
phases:
  pre_build:
    commands:
      - aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin <account-id>.dkr.ecr.$AWS_REGION.amazonaws.com
  build:
    commands:
      - docker build -t inspector-demo-repo .
      - docker tag inspector-demo-repo:latest <account-id>.dkr.ecr.$AWS_REGION.amazonaws.com/inspector-demo-repo:latest
  post_build:
    commands:
      - docker push <account-id>.dkr.ecr.$AWS_REGION.amazonaws.com/inspector-demo-repo:latest
```

### 6. Create and Run a CodeBuild Project

* Use the above buildspec file.
* Enable **Privileged mode** for Docker.
* Start the build.

### 7. View Inspector Findings

After the build completes, go to:
**Amazon Inspector → Findings**
You’ll see CVE results for your container image.

---

## 🧹 Cleanup

```bash
aws ecr delete-repository --repository-name inspector-demo-repo --force
aws codebuild delete-project --name inspector-demo-build
aws inspector2 disable --resource-types ECR
```

---

## 📘 Summary

| Component            | Purpose                          |
| -------------------- | -------------------------------- |
| **CodeBuild**        | Builds and pushes Docker images  |
| **ECR**              | Stores container images          |
| **Amazon Inspector** | Scans images for vulnerabilities |

---

**Author:** 
Ankit Kumar 

*DevOps & Cloud Engineer*
```