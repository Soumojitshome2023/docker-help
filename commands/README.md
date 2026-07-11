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
7. [Advanced & Expert Operations](#7-advanced--expert-operations)
8. [Docker Compose Operations](#8-docker-compose-operations)

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
| **Registry Login** | `docker login` | `cat pass.txt | docker login --username user --password-stdin registry.gitlab.com` | Logs into custom registry securely using stdin to read the password (`--password-stdin`) preventing CLI history leaks. |
| **Tag Image** | `docker tag app myuser/app:v1` | `docker tag app registry.gitlab.com/group/proj/app:v1` | Applies a target tag configuration matching the exact path requirements for GitLab container registries. |
| **Push Image** | `docker push myuser/app:v1` | `docker push --all-tags registry.gitlab.com/group/proj/app` | Uploads all tagged versions of the repository to the registry in one step (`--all-tags`). |

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
| **Remove Container**| `docker rm web-server` | `docker rm -f -v web-server` | Forcefully stops and deletes a running container (`-f`) and removes its associated anonymous volumes (`-v`) to prevent volume accumulation. |
| **Remove Image** | `docker rmi my-app:v1` | `docker rmi -f my-app:v1` | Force-deletes an image (`-f`), resolving conflicts if the image tag is pointing to multiple repositories or has container associations. |
| **Remove Volume** | `docker volume rm my-vol` | `docker volume rm $(docker volume ls -q -f dangling=true)` | Bulk deletes all orphaned (dangling) volumes using sub-command expansion to query only volume IDs (`-q`) matching the dangling filter (`-f`). |

---

## 6. Debugging & Inspection

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Exec Session** | `docker exec web-server ls` | `docker exec -it --user root --workdir /var/log web-server bash` | Starts an interactive TTY session (`-it`), executing the command as root user (`--user`), and sets the starting directory inside the container to `/var/log` (`--workdir`). |
| **Inspect Stats** | `docker stats` | `docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"` | Displays resource statistics, but prints a single snapshot instead of continuous streaming (`--no-stream`), and custom-formats columns to show Name, CPU, and Memory usage (`--format`). |
| **Inspect Configuration**| `docker inspect web-server`| `docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web-server` | Inspects the container configuration and runs a query string expression to extract only the internal IP address of the container (`--format`). |
| **Container Processes** | `docker top web-server` | `docker top web-server -eo pid,ppid,comm` | Lists running processes inside the container, utilizing format flags (`-eo`) to display parent IDs and short commands. |
| **File Diffs** | `docker diff web-server` | `docker diff web-server` | Inspects changes (Additions `A`, Deletions `D`, Modifications `C`) made to the container's read-write layer filesystem compared to the base image. |
| **Disk Usage** | `docker system df` | `docker system df -v` | Analyzes storage space consumption by Docker elements, adding detailed breakdown of individual image/container sizes (`-v`). |

---

## 7. Advanced & Expert Operations

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Multi-Arch Build** | `docker buildx build .` | `docker buildx build --platform linux/amd64,linux/arm64 --push -t myuser/app:v1.0 .` | Builds images for multiple hardware architectures (AMD64 & ARM64) simultaneously (`--platform`), builds and pushes directly to a registry (`--push`), and tags it (`-t`). |
| **Limit Resources** | `docker run nginx` | `docker run -d --name limited-web --memory="512m" --cpus="1.5" --memory-swap="1g" nginx` | Runs container with strict resource limits restricting it to maximum 512MB RAM (`--memory`), 1.5 CPU cores (`--cpus`), and a maximum of 1GB total RAM + swap combined (`--memory-swap`). |
| **Custom Logging** | `docker run nginx` | `docker run -d --log-driver json-file --log-opt max-size=10m --log-opt max-file=3 nginx` | Configures logging driver (`--log-driver`) and applies log rotation settings restricting log files to maximum 10MB (`--log-opt max-size`) keeping only 3 rotated files (`--log-opt max-file`). |
| **Stream Events** | `docker events` | `docker events --filter 'event=die' --filter 'type=container'` | Listens to daemon events in real-time, filtering only container-specific events (`--filter type=container`) where a container terminates/dies (`--filter event=die`). |
| **Volume Backup** | `docker run -v my-vol:/data alpine` | `docker run --rm --volumes-from web-server -v $(pwd):/backup alpine tar cvf /backup/backup.tar /usr/share/nginx/html` | Spins up a temporary self-destructing container (`--rm`), copies all volumes mounted on `web-server` (`--volumes-from`), mounts the host workspace directory to `/backup` (`-v`), and compresses the data into a tar archive. |

---

## 8. Docker Compose Operations

| Action | Simple Command | Complex / Advanced Command | What the Complex Flags Do |
| :--- | :--- | :--- | :--- |
| **Start Services** | `docker compose up` | `docker compose up -d --build --force-recreate --remove-orphans` | Runs services in background detached mode (`-d`), forces rebuild of custom images (`--build`), recreates containers even if configuration did not change (`--force-recreate`), and removes old service containers no longer defined (`--remove-orphans`). |
| **Stop Services** | `docker compose down` | `docker compose down -v --rmi all --remove-orphans` | Stops and deletes containers, plus deletes all named volumes declared in the Compose file (`-v`), removes all images built/used by the services (`--rmi all`), and clears orphan containers. |
| **View Logs** | `docker compose logs` | `docker compose logs -f --tail 50 --timestamps web` | Streams logs live (`-f`), showing only the last 50 entries (`--tail 50`) with timestamp info (`--timestamps`) for the specific `web` service container. |
| **Run Executions** | `docker compose exec web sh` | `docker compose exec --user root --env DEBUG=true web npm run dev` | Executes commands inside a Compose container, executing it as the root user (`--user`) and passing local environment overrides (`--env`). |
| **Validate Config** | `docker compose config` | `docker compose config --profiles --resolve-image-digests` | Validates YAML syntax, checking active environment variables, listing active container profiles (`--profiles`), and pinning images to precise content digests (`--resolve-image-digests`). |

---

⬅️ **[Back to Home](../README.md)** | ➡️ **[Go to Learning Guide](../learning/README.md)**
