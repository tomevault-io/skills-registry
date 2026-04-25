---
name: docker
description: Run Docker commands within a container environment, including starting the Docker daemon and managing containers. Use when building, running, or managing Docker containers and images. Use when this capability is needed.
metadata:
  author: openhands
---

# Docker Usage Guide

## Starting Docker in Container Environments

Please check if docker is already installed. If so, to start Docker in a container environment:

```bash
# Start Docker daemon in the background
sudo dockerd > /tmp/docker.log 2>&1 &

# Wait for Docker to initialize
sleep 5
```

## Verifying Docker Installation

To verify Docker is working correctly, run the hello-world container:

```bash
sudo docker run hello-world
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openhands) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
