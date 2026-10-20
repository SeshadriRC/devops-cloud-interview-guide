# Docker & Containerization Interview Preparation Guide

This guide covers Docker core concepts, image optimization, multi-stage builds, security best practices, and frequently asked interview scenarios.

---

## 📑 Table of Contents
1. [Core Docker Concepts & Architecture](#1-core-docker-concepts--architecture)
2. [Single-Stage vs. Multi-Stage Docker Builds](#2-single-stage-vs-multi-stage-docker-builds)
3. [Top Docker Interview Questions](#3-top-docker-interview-questions)
   - [CMD vs. ENTRYPOINT](#cmd-vs-entrypoint)
   - [Docker Layer Caching & Image Optimization](#docker-layer-caching--image-optimization)
   - [Container Security: Running as Non-Root](#container-security-running-as-non-root)
   - [Difference Between Docker Image and Container](#difference-between-docker-image-and-container)
4. [Study Guides & Reference Files](#4-study-guides--reference-files)

---

## 1. Core Docker Concepts & Architecture

Docker is an open-source containerization platform that packages applications and their dependencies into standardized, lightweight containers.

```text
  [ Dockerfile ]
        │
        ▼ (docker build)
  [ Docker Image ] (Read-only layered template)
        │
        ▼ (docker run)
  [ Running Container ] (Isolated process with read-write layer)
```

* **Detailed Guide**: [What is Docker and How it Works](my-preparation/docker.md)

---

## 2. Single-Stage vs. Multi-Stage Docker Builds

* **Deep Dive Guide**: [Single vs Multi-Stage Java Dockerfiles](my-preparation/dockerfiles.md)

### Why Use Multi-Stage Builds?
* In a single-stage build, the final image contains the build toolchain (Maven, JDK, compilers, caches), resulting in large images (e.g., 600MB–1GB) with expanded security attack surfaces.
* In a multi-stage build:
  - **Stage 1 (Builder)**: Uses a full SDK image (e.g. `maven:3.9-eclipse-temurin-17`) to compile code and build the artifact.
  - **Stage 2 (Runtime)**: Copies **only the built artifact** (e.g. `app.jar`) into a minimal JRE/distroless/Alpine image (e.g. `eclipse-temurin:17-jre-alpine`).
  - **Result**: Drastically smaller image size (~150MB), faster deployment pulls, and zero build tool vulnerabilities in production.

---

## 3. Top Docker Interview Questions

### CMD vs. ENTRYPOINT
| Instruction | Purpose | Override via CLI |
| :--- | :--- | :--- |
| **`ENTRYPOINT`** | Defines the exact binary/command that will always execute | Overridden via `--entrypoint` |
| **`CMD`** | Defines default arguments passed to `ENTRYPOINT`, or fallback default command | Overridden simply by appending arguments to `docker run` |

**Best Practice Pattern**:
```dockerfile
ENTRYPOINT ["java", "-jar"]
CMD ["/app/application.jar"]
```
* Running `docker run myimage` runs `java -jar /app/application.jar`.
* Running `docker run myimage /app/other.jar` runs `java -jar /app/other.jar`.

### Docker Layer Caching & Image Optimization
* **Question**: How do you optimize Docker image build time and image size?
* **Answers**:
  1. **Order layers from least frequent to most frequent change**: Copy dependency descriptors (`pom.xml`, `package.json`, `requirements.txt`) and install dependencies *before* copying source code (`COPY . .`).
  2. **Use minimal base images**: Prefer `alpine`, `slim`, or `distroless` over full OS images (`ubuntu`, `debian`).
  3. **Combine `RUN` statements**: Chain commands with `&&` and clean package manager caches in the same layer:
     ```dockerfile
     RUN apt-get update && apt-get install -y --no-install-recommends curl \
         && rm -rf /var/lib/apt/lists/*
     ```
  4. **Use `.dockerignore`**: Exclude `.git`, `node_modules`, `target`, local logs, and temporary files from the build context.

### Container Security: Running as Non-Root
* By default, Docker containers run as `root` (UID 0), posing container-breakout security risks.
* Always define an unprivileged user in your Dockerfile:
  ```dockerfile
  RUN addgroup -S appgroup && adduser -S appuser -G appgroup
  USER appuser
  ```

---

## 4. Study Guides & Reference Files
* 📦 [Docker Overview & Lifecycle](my-preparation/docker.md)
* 📜 [Production Single and Multi-Stage Dockerfile Examples](my-preparation/dockerfiles.md)
* 🛠️ [Hands-on: Containerize Flask App Practical](../Practicals/02-Deploy-containerize-Flask-app.md)
