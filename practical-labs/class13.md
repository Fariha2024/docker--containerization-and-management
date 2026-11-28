# 🔷 Docker Installation, Container Management, Storage & Networking – Practical Lab

## 🔹 Overview

This practical lab covers Docker installation and configuration on Linux systems, image management, container lifecycle operations, Docker storage architecture, Overlay2 filesystem, port mapping, Nginx deployment, and Docker volumes.

The lab contains **78 hands-on practical steps** demonstrating essential Docker commands and container management operations.

### 🔹 Topics Covered

- 🔵 Docker CE Installation on Ubuntu
- 🔵 Docker Images and Container Management
- 🔵 Working with Rocky Linux, Alpine Linux and Ubuntu Containers
- 🔵 Docker Container Lifecycle Management
- 🔵 Docker Inspect and Container Metadata
- 🔵 Docker Storage Architecture
- 🔵 Overlay2 Storage Driver
- 🔵 UpperDir, LowerDir and MergedDir
- 🔵 Docker Disk Space Management
- 🔵 Nginx Container Deployment
- 🔵 Docker Port Mapping
- 🔵 Docker Volumes
- 🔵 MySQL Container Deployment

---

# 🔷 SECTION 1: Docker Installation on Ubuntu

### 🔹 STEP 1: Connect to Ubuntu Server

```bash
sudo -i
```

### 🔹 STEP 2: Check Ubuntu Version

```bash
cat /etc/os-release
```

### 🔹 STEP 3: Check Docker CE Package

```bash
apt info docker-ce
```

### 🔹 STEP 4: Update Package Index

```bash
sudo apt-get update
```

### 🔹 STEP 5: Install Required Tools

```bash
sudo apt-get install ca-certificates curl
```

### 🔹 STEP 6: Create APT Keyrings Directory

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### 🔹 STEP 7: Download Docker GPG Key

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

### 🔹 STEP 8: Set GPG Key Permissions

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

### 🔹 STEP 9: Add Docker APT Repository

```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 🔹 STEP 10: Update Package Index Again

```bash
sudo apt-get update
```

### 🔹 STEP 11: Install Docker CE

```bash
sudo apt install docker-ce
```

### 🔹 STEP 12: Check Docker Service

```bash
systemctl status docker
```

### 🔹 STEP 13: Check Docker Version

```bash
docker --version
```

---

# 🔷 SECTION 2: Docker Images and Container Creation

### 🔹 STEP 14: Check Docker Images

```bash
docker images
```

### 🔹 STEP 15: Check Containers

```bash
docker ps -a
```

### 🔹 STEP 16: Check Docker Directory

```bash
ls -l /var/lib/docker
```

### 🔹 STEP 17: Pull Alpine Image

```bash
docker pull alpine
```

### 🔹 STEP 18: Pull Rocky Linux Image

```bash
docker pull rockylinux:9.3.20231119
```

### 🔹 STEP 19: Check Images

```bash
docker images
```

### 🔹 STEP 20: Create Rocky Linux Container C1

```bash
docker run --name c1 -dit IMAGE_ID
```

### 🔹 STEP 21: Check Containers

```bash
docker ps -a
```

### 🔹 STEP 22: Create Rocky Linux Container C2

```bash
docker run --name c2 -dit IMAGE_ID
```

### 🔹 STEP 23: Enter Rocky Linux Container

```bash
docker exec -it c2 bash
```

### 🔹 STEP 24: Check Container OS

```bash
cat /etc/os-release
```

### 🔹 STEP 25: Exit Container

```bash
exit
```

### 🔹 STEP 26: Create Alpine Container C3

```bash
docker run --name c3 -dit IMAGE_ID
```

### 🔹 STEP 27: Enter Alpine Container

```bash
docker exec -it c3 sh
```

### 🔹 STEP 28: Check Alpine OS

```bash
cat /etc/os-release
```

### 🔹 STEP 29: Exit Container

```bash
exit
```

### 🔹 STEP 30: Create Alpine Containers C4, C5 and C6

```bash
docker run --name c4 -dit IMAGE_ID
docker run --name c5 -dit IMAGE_ID
docker run --name c6 -dit IMAGE_ID
```

### 🔹 STEP 31: Check All Containers

```bash
docker ps -a
```

### 🔹 STEP 32: Stop Selected Containers

```bash
docker stop c1 c2 c5
```

### 🔹 STEP 33: Check Container Status

```bash
docker ps -a
```

---

# 🔷 SECTION 3: Docker Inspect and Container Information

### 🔹 STEP 34: Inspect Rocky Linux Image

```bash
docker inspect IMAGE_ID
```

### 🔹 STEP 35: Inspect Alpine Image

```bash
docker inspect IMAGE_ID
```

### 🔹 STEP 36: Inspect Container C3

```bash
docker inspect c3
```

### 🔹 STEP 37: Inspect Container C4

```bash
docker inspect c4
```

### 🔹 STEP 38: Create Rocky Linux Container C11

```bash
docker run --name c11 -dit IMAGE_ID
```

### 🔹 STEP 39: Check Image Used by C11

```bash
docker inspect c11 --format='{{.Config.Image}}'
```

### 🔹 STEP 40: Open Docker Directory

```bash
cd /var/lib/docker
ls -ltr
```

### 🔹 STEP 41: Return to Root Directory

```bash
cd
```

### 🔹 STEP 42: Check Container Name

```bash
docker inspect --format '{{.Name}}' c11
```

### 🔹 STEP 43: Check Storage Driver

```bash
docker inspect --format '{{json .GraphDriver}}' c11
```

### 🔹 STEP 44: Check Container Details

```bash
docker inspect --format='Container: {{.Name}}
ID: {{.Id}}
Image: {{.Config.Image}}
Status: {{.State.Status}}
Created: {{.Created}}' c11
```

---

# 🔷 SECTION 4: Ubuntu Container and Docker Storage Architecture

### 🔹 STEP 45: Pull Ubuntu Image

```bash
docker pull ubuntu
```

### 🔹 STEP 46: Create Ubuntu Container C12

```bash
docker run --name c12 -dit IMAGE_ID
```

### 🔹 STEP 47: Check Container Details

```bash
docker inspect --format='Container: {{.Name}}
ID: {{.Id}}
Image: {{.Config.Image}}
Status: {{.State.Status}}
Created: {{.Created}}' c12
```

### 🔹 STEP 48: Check Upper, Lower and Merged Directories

```bash
docker inspect --format='Container: {{.Name}}
UpperDir: {{.GraphDriver.Data.UpperDir}}
LowerDir: {{.GraphDriver.Data.LowerDir}}
MergedDir: {{.GraphDriver.Data.MergedDir}}' c12
```

---

# 🔷 SECTION 5: Docker Storage on RHEL / Rocky Linux

### 🔹 STEP 49: Check Docker Version on RHEL Machine

```bash
docker --version
```

### 🔹 STEP 50: Create Rocky Linux Container C13

```bash
docker run --name c13 -dit IMAGE_ID
```

### 🔹 STEP 51: Check Containers

```bash
docker ps -a
```

### 🔹 STEP 52: Check C13 Details

```bash
docker inspect --format='Container: {{.Name}}
ID: {{.Id}}
Image: {{.Config.Image}}
Status: {{.State.Status}}
Created: {{.Created}}' c13
```

### 🔹 STEP 53: Check C13 Storage Directories

```bash
docker inspect --format='Container: {{.Name}}
UpperDir: {{.GraphDriver.Data.UpperDir}}
LowerDir: {{.GraphDriver.Data.LowerDir}}
MergedDir: {{.GraphDriver.Data.MergedDir}}' c13
```

### 🔹 STEP 54: Enter C13

```bash
docker exec -it c13 bash
```

### 🔹 STEP 55: Create Directory and Files Inside C13

```bash
mkdir tgindia
touch India
touch myinfo-devops.txt
echo "hii hello" > demo.txt
ls -ltr
```

### 🔹 STEP 56: Exit C13

```bash
exit
```

### 🔹 STEP 57: Check C13 Storage Directories Again

```bash
docker inspect --format='Container: {{.Name}}
UpperDir: {{.GraphDriver.Data.UpperDir}}
LowerDir: {{.GraphDriver.Data.LowerDir}}
MergedDir: {{.GraphDriver.Data.MergedDir}}' c13
```

### 🔹 STEP 58: Open Overlay2 Directory

```bash
cd /var/lib/docker/overlay2
ls -ltr
```

### 🔹 STEP 59: Check UpperDir, LowerDir and MergedDir

```bash
docker inspect --format='Container: {{.Name}}
UpperDir: {{.GraphDriver.Data.UpperDir}}
LowerDir: {{.GraphDriver.Data.LowerDir}}
MergedDir: {{.GraphDriver.Data.MergedDir}}' c13
```

### 🔹 STEP 60: Check Container File Content

```bash
docker exec -it c13 bash
cat demo.txt
exit
```

### 🔹 STEP 61: Create Java File Inside C13

```bash
docker exec -it c13 bash
echo "class A { p.s.v.main() {S.Op} }" >> A.java
cat A.java
exit
```

---

# 🔷 SECTION 6: Container and Image Cleanup

### 🔹 STEP 62: Remove C1 and C2

```bash
docker rm -f c1 c2
```

### 🔹 STEP 63: Remove All Containers

```bash
docker rm -f $(docker ps -aq)
```

### 🔹 STEP 64: Check Containers and Images

```bash
docker ps -a
docker images
```

### 🔹 STEP 65: Remove an Image

```bash
docker rmi -f IMAGE_ID
```

### 🔹 STEP 66: Watch Overlay2 Directory

```bash
watch -n1 ls -ltr
```

---

# 🔷 SECTION 7: Nginx Image and Disk Management

### 🔹 STEP 67: Pull Nginx Image

```bash
docker pull nginx
```

### 🔹 STEP 68: Check Disk Space

```bash
df -hT
```

### 🔹 STEP 69: Check Directory Size

```bash
cd /
du -hs *
```

### 🔹 STEP 70: Check Docker Directory Size

```bash
cd /var/lib
du -hs *
```

### 🔹 STEP 71: Create Five Rocky Linux Containers

```bash
docker run --name c1 -dit IMAGE_ID
docker run --name c2 -dit IMAGE_ID
docker run --name c3 -dit IMAGE_ID
docker run --name c4 -dit IMAGE_ID
docker run --name c5 -dit IMAGE_ID
```

### 🔹 STEP 72: Check Containers and Disk Space

```bash
docker ps -a
df -hT
```

### 🔹 STEP 73: Stop Containers and Remove Exited Containers

```bash
docker stop c1 c2
docker rm -f $(docker ps -aqf status=exited)
```

### 🔹 STEP 74: Remove All Containers and Clean Unused Data

```bash
docker rm -f $(docker ps -aq)
docker system prune
df -hT
```

---

# 🔷 SECTION 8: Nginx Deployment and Port Mapping

### 🔹 STEP 75: Create Nginx Containers with Port Mapping

**Container C11 – Automatic Port Mapping**

```bash
docker run --name c11 -dit -P nginx
```

**Container C12 – Automatic Port Mapping**

```bash
docker run --name c12 -dit -P nginx
```

**Container C13 – Manual Port Mapping**

```bash
docker run --name c13 -dit -p 32800:80 nginx
```

### 🔹 STEP 76: Check Ports and Public IP

```bash
docker ps -a
curl ifconfig.io
```

### 🔹 STEP 77: Change Nginx Default Web Pages

**Container C11**

```bash
docker exec -it c11 bash
cd /usr/share/nginx/html
echo "hii im from container-1" > index.html
exit
```

**Container C12**

```bash
docker exec -it c12 bash
cd /usr/share/nginx/html
echo "hello im from container-2" > index.html
exit
```

**Container C13**

```bash
docker exec -it c13 bash
cd /usr/share/nginx/html
echo "hello im from container-3" > index.html
exit
```

---

# 🔷 SECTION 9: Docker Volumes and MySQL Container

### 🔹 STEP 78: Create MySQL Volume and Start MySQL Container

**1. Create MySQL Volume**

```bash
docker volume create mysql.dbbackup
```

**2. List Docker Volumes**

```bash
docker volume ls
```

**3. Inspect MySQL Volume**

```bash
docker inspect mysql.dbbackup
```

**4. Pull MySQL 8 Image**

```bash
docker pull mysql:8
```

**5. Start MySQL Container C1**

```bash
docker run --name c1 -dit -P mysql:8
```

**6. Check MySQL Container Logs**

```bash
docker logs c1
```

**7. Start MySQL Container C2 with Root Password**

```bash
docker run --name c2 -dit -P -e MYSQL_ROOT_PASSWORD=redhat mysql:8
```

**8. Check Running Containers**

```bash
docker ps -a
```

**9. Enter MySQL Container C2**

```bash
docker exec -it c2 bash
```

**10. Connect to MySQL Server**

```bash
mysql -u root -p
```

---

# 🔷 Important Docker Commands – Quick Reference

| Command | Description |
|---|---|
| `docker --version` | Check Docker version |
| `docker images` | List Docker images |
| `docker pull` | Download an image |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker run` | Create and start a container |
| `docker exec -it` | Access a running container |
| `docker stop` | Stop a container |
| `docker start` | Start a stopped container |
| `docker rm` | Remove a container |
| `docker rmi` | Remove an image |
| `docker inspect` | Display detailed information |
| `docker logs` | Display container logs |
| `docker volume ls` | List Docker volumes |
| `docker volume create` | Create a volume |
| `docker system prune` | Clean unused Docker resources |

---

# 🔷 Important Notes and Lab Considerations

🔹 **IMAGE_ID:** Replace `IMAGE_ID` with the correct image ID obtained using `docker images`. Use the Rocky Linux image for Rocky containers and the Alpine image for Alpine containers.

🔹 **Docker Installation:** The installation commands in Steps 1–13 are for Ubuntu. Docker must already be installed on the RHEL/Rocky Linux machine used in Step 49.

🔹 **Container Names:** Docker container names must be unique on the same Docker host. Remove existing containers or choose different names before recreating them.

🔹 **Storage Driver:** Overlay2 uses image layers and a writable container layer. The UpperDir, LowerDir and MergedDir paths help demonstrate this architecture.

🔹 **Port Mapping:** `-P` automatically publishes exposed container ports, whereas `-p 32800:80` maps host port 32800 to container port 80.

🔹 **Volumes:** Creating a volume alone does not attach it to a container. To persist MySQL data, the volume must be mounted at `/var/lib/mysql` when creating the container.

🔹 **MySQL:** Step 78 creates a volume but does not mount it. The first MySQL container also has no root password configured. The second container sets one.

🔹 **Cleanup Warning:** `docker rm -f $(docker ps -aq)` removes all containers on the Docker host. `docker system prune` can remove unused Docker resources. Use these commands carefully.

---

# 🔷 Learning Outcomes

After completing this practical lab, the learner will have hands-on experience with:

- 🔵 Installing and configuring Docker CE on Ubuntu.
- 🔵 Pulling and managing Docker images.
- 🔵 Creating and managing multiple Linux containers.
- 🔵 Accessing containers using interactive terminals.
- 🔵 Inspecting container configuration and metadata.
- 🔵 Understanding Docker's Overlay2 storage architecture.
- 🔵 Exploring container writable and read-only layers.
- 🔵 Monitoring Docker storage and disk utilization.
- 🔵 Managing container lifecycle and cleanup.
- 🔵 Deploying Nginx containers with port mapping.
- 🔵 Modifying Nginx default web pages.
- 🔵 Creating Docker volumes and deploying MySQL containers.

---

**Practical Lab:** Docker Fundamentals and Administration  
**Platform:** Ubuntu / RHEL / Rocky Linux  
**Technology:** Docker CE, Linux Containers, Overlay2, Nginx, MySQL  
**Total Practical Steps:** 78

