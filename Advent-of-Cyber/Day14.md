# Container Security - Thinking in Containers

## Overview
In this room, we explored the fundamentals of containerization, the architectural differences between Containers and Virtual Machines (VMs), and the security risks associated with Docker, specifically focusing on **Docker Escape** attacks via runtime sockets.

The goal was to investigate a defaced website ("Hopperoo"), identify a container with excessive privileges, and perform an escape to restore the original service ("DoorDasher").

---

## Key Concepts

### 1. Containers vs. Virtual Machines (VMs)
* **Virtual Machines**: Run on a hypervisor and include a full guest Operating System (OS). They are "heavy" and consume more resources but offer full isolation.
* **Containers**: Share the host OS kernel and isolate only the application and its dependencies. They are lightweight, portable, and start almost instantly.



### 2. Docker Architecture & Sockets
* **Docker Engine**: Software that builds and manages containers using host kernel features like namespaces and cgroups.
* **Runtime Sockets**: Docker uses a client-server setup where the CLI communicates with the Docker daemon via Unix sockets (usually `/var/run/docker.sock`).
* **The Vulnerability**: If a container is misconfigured to have access to the host's Docker socket, an attacker inside that container can send commands directly to the Docker daemon, effectively controlling the entire host or other containers.

---

## Technical Investigation (Lab)

### Step 1: Enumeration
We started by listing the running containers on the host system to identify our targets.

**Command:**
```bash
docker ps
```
*Identified containers: `uptime-checker`, `deployer`, and the main web service.*

### Step 2: Container Access & Socket Check
We accessed the `uptime-checker` container to check for sensitive mounts.
**Commands:**
```bash
docker exec -it uptime-checker sh
ls -la /var/run/docker.sock
```
**Finding**: The Docker socket was accessible from within the container, indicating a major security misconfiguration that allows direct interaction with the Docker API.

### Step 3: Docker Escape & Privilege Escalation
Using the socket access, we performed an escape by interacting with the API to enter the more privileged `deployer` container.
**Command:**
```bash
docker exec -it deployer bash
```
### Step 4: System Recovery
Inside the `deployer` container, we located and executed a recovery script with sudo privileges to restore the defaced website.
**Command:**
```bash
sudo /recovery_script.sh
```
## Answers Summary

| Question | Answer |
| :--- | :--- |
| What exact command lists running Docker containers? | `docker ps` |
| What file is used to define the instructions for building a Docker image? | `Dockerfile` |
| What is the flag ? | `THM{DOCKER_ESCAPE_SUCCESS}` |
| Bonus: Secret code/password for deployer user | `DeployMaster2025!` |

---

## Lessons Learned
* **Principle of Least Privilege**: Never mount the Docker socket inside a container unless absolutely necessary, as it bypasses isolation.
* **Container Isolation**: Use "Enhanced Container Isolation" settings to prevent containers from mounting host sockets.
* **Microservices Scaling**: Containers excel at scaling specific parts of an application (microservices) to meet demand without scaling the entire monolithic unit.
* **Escaping Hazards**: A "Docker Escape" allows an attacker to bypass all isolation layers and execute code on the host kernel.

---

> **Key Takeaway**: Containers are lightweight and efficient, but their shared-kernel nature means a single misconfigured mount can compromise the entire infrastructure.
