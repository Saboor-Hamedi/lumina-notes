# Lesson 1: Running Your First Containers

**Lesson 1** · ~20 min · Hands-on

> [!NOTE] Mission tie-in
> Containers package your app with everything it needs — code, runtime, dependencies. No more "it works on my machine." This is the foundation of modern deployment.

## What You'll Learn

- What Docker is and how to install it
- Run your first container
- Understand images vs containers
- Basic container lifecycle

## Install Docker

Download Docker Desktop for Windows from [docker.com](https://www.docker.com/products/docker-desktop/). Verify:

```bash
docker --version
docker info
```

## Your First Container

```bash
docker run hello-world
```

This downloads a tiny test image and runs it. You'll see:
- Docker couldn't find `hello-world` locally
- It pulled from Docker Hub
- It created a container, ran it, and it exited

## Images vs Containers

| Concept | Analogy | Docker |
|---------|---------|--------|
| **Image** | A recipe / class | Read-only template with instructions |
| **Container** | A cooked meal / instance | Running instance of an image |
| **Docker Hub** | A cookbook library | Public registry of images |
| **Dockerfile** | The recipe book | Instructions to build an image |

## Run Interactive Containers

```bash
# Run Ubuntu interactively
docker run -it --rm ubuntu:latest bash

# Inside the container — you're root!
root@abc123:/# cat /etc/os-release
root@abc123:/# exit
```

The `-it` makes it interactive with a terminal. `--rm` deletes the container when it exits.

## Run a Web Server

```bash
# Run nginx on port 8080
docker run -d --name my-nginx -p 8080:80 nginx:latest

# Visit http://localhost:8080 in your browser

# See it running
docker ps

# Check logs
docker logs my-nginx

# Stop and remove
docker stop my-nginx
docker rm my-nginx
```

> [!TIP] `-d` runs in detached mode (background). `-p 8080:80` maps host port 8080 to container port 80.

## Basic Container Lifecycle

```bash
docker pull nginx:latest       # Download image
docker run -d --name web nginx # Create and start container
docker ps                      # List running containers
docker stop web                # Stop gracefully
docker start web               # Restart stopped container
docker ps -a                   # List ALL containers (including stopped)
docker rm web                  # Remove container
docker rmi nginx:latest        # Remove image
```

## Hands-On

```bash
# 1. Test your install
docker run hello-world

# 2. Run Ubuntu and explore
docker run -it --rm ubuntu:latest bash
# Inside: whoami, ls, cat /etc/os-release
# Type exit to leave

# 3. Run a web server
docker run -d --name nginx-test -p 8080:80 nginx:latest
curl http://localhost:8080

# 4. See what's running
docker ps
docker stats --no-stream

# 5. Cleanup
docker stop nginx-test && docker rm nginx-test
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What's the difference between a Docker image and a container?</summary>

An image is a read-only template (the recipe). A container is a running instance of that image (the cooked meal). You can run multiple containers from the same image.
</details>

<details>
<summary><strong>2.</strong> What does <code>-p 8080:80</code> do?</summary>

Maps port 8080 on your host to port 80 in the container. You access the service at `localhost:8080`.
</details>

<details>
<summary><strong>3.</strong> What does <code>docker ps -a</code> show that <code>docker ps</code> doesn't?</summary>

`docker ps` shows only running containers. `docker ps -a` shows all containers including stopped ones.
</details>

## Next Steps

- Experiment with different images: `docker run -it python:3.12 bash`
- Run multiple containers and check they're isolated
- Move to Lesson 2: Dockerfiles & Building Images (coming next)

> [!NOTE] Ask followup questions!
> Container won't start? Port already in use? Ask your teacher.

---

*Reference: [Docker CLI cheatsheet](https://dockerlabs.collabnix.com/docker/cheatsheet/)*
