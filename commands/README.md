# Docker Helper Commands Cheat-Sheet

A comprehensive guide showing Docker helper commands starting from their **simplest forms** and evolving into **complex/production-ready forms**.

---

## 📋 Table of Contents
1. [Container Lifecycle & Operations](#1-container-lifecycle--operations)
2. [Image Management](#2-image-management)
3. [Volumes & Storage](#3-volumes--storage)
4. [Networking](#4-networking)
5. [System Cleanup](#5-system-cleanup)
6. [Debugging & Inspection](#6-debugging--inspection)

---

## 1. Container Lifecycle & Operations

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Run Container** | `docker run nginx` | `docker run -d --name web-server -p 8080:80 -v html-vol:/usr/share/nginx/html --restart unless-stopped nginx` | Runs in background (`-d`), assigns a custom name (`--name`), routes ports host:container (`-p`), mounts a volume (`-v`), and auto-restarts on failure (`--restart`). |
| **List Containers** | `docker ps` | `docker ps -a --filter "status=exited" --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"` | Lists all containers (`-a`), filters only the exited ones (`--filter`), and outputs a custom formatted table (`--format`). |
| **View Logs** | `docker logs web-server` | `docker logs -f --tail 100 --timestamps web-server` | Streams logs in real-time (`-f`), limits output to the last 100 lines (`--tail`), and prints UTC timestamps on every log entry (`--timestamps`). |
| **Stop Containers** | `docker stop web-server` | `docker stop -t 30 web-server` | Stops the container but extends the graceful shutdown timeout period from the default 10 seconds to 30 seconds before sending SIGKILL (`-t`). |

---

## 2. Image Management

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Build Image** | `docker build .` | `docker build --no-cache --build-arg VERSION=1.2 -t app:v1.2 -f Dockerfile.prod .` | Bypasses cached layers (`--no-cache`), injects an environment build variable (`--build-arg`), tags the output image name:tag (`-t`), and uses a custom file (`-f`). |
| **List Images** | `docker images` | `docker images --filter "dangling=false" --format "{{.Repository}}:{{.Tag}}"` | Lists local images, filters out dangling/unnamed intermediate build layers (`--filter`), and outputs clean `name:tag` entries (`--format`). |
| **Pull Image** | `docker pull node` | `docker pull node:18-alpine` | Pulls the lightweight Alpine Linux distribution of Node version 18 instead of the default heavy generic `node:latest` image. |

---

## 3. Volumes & Storage

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Create Volume** | `docker volume create my-vol` | `docker volume create --driver local --opt type=none --opt device=/host/data --opt o=bind my-vol` | Creates a volume using a specific driver (`--driver`), pointing to a specific path on the host filesystem via bind options (`--opt`). |
| **Inspect Storage** | `docker volume inspect my-vol` | `docker volume inspect --format '{{ .Mountpoint }}' my-vol` | Inspects details but uses a Go template formatting flag to output only the absolute local host folder path where files are stored (`--format`). |

---

## 4. Networking

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Create Network** | `docker network create my-net` | `docker network create --driver bridge --subnet 192.168.1.0/24 --gateway 192.168.1.1 my-net` | Creates a network utilizing a driver (`--driver`), allocating a specific private subnet block (`--subnet`), and defining the master gateway IP (`--gateway`). |
| **Connect Container** | `docker network connect my-net web` | `docker network connect --ip 192.168.1.50 --alias web-service my-net web` | Connects a container to a network while forcing a static internal IP address (`--ip`) and defining a network alias for internal DNS resolution (`--alias`). |

---

## 5. System Cleanup

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **System Prune** | `docker system prune` | `docker system prune -a --volumes --force` | Removes all stopped containers and build cache, plus unused images that are not referenced (`-a`), all unused persistent volumes (`--volumes`), and skips the verification prompt (`--force`). |
| **Image Prune** | `docker image prune` | `docker image prune -a --filter "until=24h"` | Cleans up dangling image layers, plus unused images (`-a`), while filtering out and saving images created in the last 24 hours (`--filter`). |

---

## 6. Debugging & Inspection

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Exec Session** | `docker exec web-server ls` | `docker exec -it --user root --workdir /var/log web-server bash` | Starts an interactive TTY session (`-it`), executing the command as root user (`--user`), and sets the starting directory inside the container to `/var/log` (`--workdir`). |
| **Inspect Stats** | `docker stats` | `docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"` | Displays resource statistics, but prints a single snapshot instead of continuous streaming (`--no-stream`), and custom-formats columns to show Name, CPU, and Memory usage (`--format`). |
| **Inspect Configuration**| `docker inspect web-server`| `docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web-server` | Inspects the container configuration and runs a query string expression to extract only the internal IP address of the container (`--format`). |

---

⬅️ **[Back to Home](../README.md)** | ➡️ **[Go to Learning Guide](../learning/README.md)**
