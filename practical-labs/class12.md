# 🔷 Docker Installation and Container Lifecycle Management – Class 12 Practical Lab

## 🔹 Overview

This practical lab covers Docker installation and configuration on Amazon Linux 2023, Linux system administration, Docker package management, Docker service management, image operations, container creation, interactive container access, process management, and container restart policies.

The lab contains **34 hands-on practical steps** demonstrating essential Docker installation and container lifecycle management operations.

### 🔹 Topics Covered

- 🔵 Linux Server Access and Root Privileges
- 🔵 Linux OS and Kernel Information
- 🔵 YUM and DNF Package Management
- 🔵 Docker Package Installation
- 🔵 RPM Package Verification
- 🔵 Docker Service Management
- 🔵 Linux Process Management
- 🔵 Docker Version and System Information
- 🔵 Docker Default Storage Directory
- 🔵 Docker Image Pulling and Management
- 🔵 Container Creation and Execution
- 🔵 Interactive and Detached Container Modes
- 🔵 Process Management Inside Containers
- 🔵 Docker Service Restart
- 🔵 Container Restart Policies
- 🔵 Container Lifecycle and Status Management

---

# 🔷 SECTION 1: Linux Server Access and System Information

### 🔹 STEP 1: Connect to Linux Server

```bash
sudo -i
```

### 🔹 STEP 2: Check Linux OS and Kernel

**Check Linux operating system:**

```bash
cat /etc/os-release
```

**Check kernel version:**

```bash
uname -r
```

**Open YUM configuration file:**

```bash
vim /etc/yum.conf


		[main]
        exclude = 6.1,6.2,6.3
```

---

# 🔷 SECTION 2: System Updates and Docker Installation

### 🔹 STEP 3: Update System

**Update packages using DNF:**

```bash
dnf update
```

**Upgrade installed packages:**

```bash
dnf upgrade
```

**Check kernel version again:**

```bash
uname -r
```

### 🔹 STEP 4: Check Docker Package

```bash
dnf info docker
```

### 🔹 STEP 5: Install Docker

```bash
dnf install docker
```

### 🔹 STEP 6: Check Docker Packages

```bash
rpm -qa | grep docker
```

### 🔹 STEP 7: Start and Enable Docker

**Start Docker service:**

```bash
systemctl start docker
```

**Enable Docker service at system startup:**

```bash
systemctl enable docker
```

### 🔹 STEP 8: Check Docker Service

```bash
systemctl status docker
```

---

# 🔷 SECTION 3: Linux Process and Docker System Information

### 🔹 STEP 9: Check Running Processes

**Display first five processes:**

```bash
ps aux | head -5
```

**View processes using less:**

```bash
ps aux | less
```

### 🔹 STEP 10: Check Docker Version and Information

**Check Docker version:**

```bash
docker --version
```

**Display Docker client and server versions:**

```bash
docker version
```

**Display Docker system information:**

```bash
docker info
```

### 🔹 STEP 11: Check Docker Default Directory

**Navigate to Docker directory:**

```bash
cd /var/lib/docker
```

**List directory contents:**

```bash
ls -ltr
```

---

# 🔷 SECTION 4: Docker Image Management and Container Creation

### 🔹 STEP 12: Pull Docker Image

Pull Rocky Linux 9.3 image:

```bash
docker pull rockylinux:9.3.20231119
```

### 🔹 STEP 13: Check Docker Images

```bash
docker images
```

### 🔹 STEP 14: Create Container in Background

Create Rocky Linux container C1 in detached mode:

```bash
docker run --name c1 -dit rockylinux:9.3.20231119
```

### 🔹 STEP 15: Check All Containers

```bash
docker ps -a
```

### 🔹 STEP 16: Create Container and Login Directly

Create Rocky Linux container C2 in interactive mode:

```bash
docker run --name c2 -it rockylinux:9.3.20231119
```

---

# 🔷 SECTION 5: Process Management Inside Docker Containers

### 🔹 STEP 17: Check OS Inside Container

```bash
cat /etc/os-release
```

### 🔹 STEP 18: Install Process Package

```bash
dnf install procps-ng
```

### 🔹 STEP 19: Check Processes Inside Container

```bash
ps aux
```

### 🔹 STEP 20: Exit Container

```bash
exit
```

### 🔹 STEP 21: Check All Containers

```bash
docker ps -a
```

### 🔹 STEP 22: Create Another Container

Create Rocky Linux container C3 in detached mode:

```bash
docker run --name c3 -dit rockylinux:9.3.20231119
```

### 🔹 STEP 23: Login to Running Container

```bash
docker exec -it c3 bash
```

### 🔹 STEP 24: Check Processes

```bash
ps aux
```

### 🔹 STEP 25: Exit Container

```bash
exit
```

---

# 🔷 SECTION 6: Docker Service and Container Restart Management

### 🔹 STEP 26: Restart Docker Service

```bash
systemctl restart docker
```

### 🔹 STEP 27: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 28: Create Container with Restart Policy

Create Rocky Linux container C4 with an always restart policy:

```bash
docker run --name c4 --restart always -dit rockylinux:9.3.20231119
```

### 🔹 STEP 29: Restart Docker Service

```bash
systemctl restart docker
```

### 🔹 STEP 30: Check Container Status

```bash
docker ps -a
```

---

# 🔷 SECTION 7: Container Execution and Lifecycle Verification

### 🔹 STEP 31: Create Container Without Interactive Mode

```bash
docker run --name c5 rockylinux:9.3.20231119
```

### 🔹 STEP 32: Check All Containers

```bash
docker ps -a
```

### 🔹 STEP 33: Check Docker Images

```bash
docker images
```

### 🔹 STEP 34: Check Docker Service

```bash
systemctl status docker
```

---

# 🔷 Important Docker Commands – Quick Reference

| Command | Description |
|---|---|
| `sudo -i` | Switch to root user |
| `cat /etc/os-release` | Check Linux distribution |
| `uname -r` | Check kernel version |
| `dnf update` | Update system packages |
| `dnf upgrade` | Upgrade installed packages |
| `dnf info docker` | Check Docker package information |
| `dnf install docker` | Install Docker |
| `rpm -qa` | List installed RPM packages |
| `systemctl start docker` | Start Docker service |
| `systemctl enable docker` | Enable Docker at startup |
| `systemctl status docker` | Check Docker service status |
| `systemctl restart docker` | Restart Docker service |
| `ps aux` | Display running processes |
| `docker --version` | Check Docker version |
| `docker version` | Check client and server versions |
| `docker info` | Display Docker system information |
| `docker pull` | Download Docker image |
| `docker images` | List Docker images |
| `docker run` | Create and start container |
| `docker ps -a` | List all containers |
| `docker exec -it` | Access running container |
| `docker run --restart always` | Apply always restart policy |

---

# 🔷 Important Notes and Lab Considerations

🔹 **Operating System:** These practical commands use DNF and RPM package management and are intended for Amazon Linux 2023.

🔹 **Root Access:** `sudo -i` switches the current user to the root account.

🔹 **YUM and DNF:** Amazon Linux 2023 uses DNF as its package manager. The `yum` command may also be available as a compatibility interface.

🔹 **Docker Installation:** The Docker package is installed through the configured Amazon Linux repositories.

🔹 **Detached Mode:** The `-d` option runs a container in the background.

🔹 **Interactive Mode:** The `-it` options allocate a terminal and keep the session interactive.

🔹 **Container Restart Policy:** `--restart always` configures Docker to restart the container automatically under the applicable restart conditions.

🔹 **Container Without Interactive Mode:** Step 31 runs the container without detached or interactive options. The container normally exits when its main process finishes.

🔹 **Process Management:** The `procps-ng` package provides Linux process utilities such as `ps`.

🔹 **Container Names:** Container names must be unique on the same Docker host.

---

# 🔷 Learning Outcomes

After completing this Class 12 practical lab, the learner will have hands-on experience with:

- 🔵 Accessing Linux servers and managing root privileges.
- 🔵 Identifying Linux operating system and kernel versions.
- 🔵 Updating packages using DNF.
- 🔵 Installing and verifying Docker packages.
- 🔵 Managing Docker services through systemctl.
- 🔵 Monitoring Linux processes.
- 🔵 Understanding Docker client and server information.
- 🔵 Exploring the Docker default storage directory.
- 🔵 Pulling and managing Rocky Linux images.
- 🔵 Creating containers in detached and interactive modes.
- 🔵 Executing commands inside running containers.
- 🔵 Managing container restart policies.
- 🔵 Understanding container execution and lifecycle states.

---

**Class:** 12  
**Practical:** Docker Installation and Container Lifecycle Management  
**Operating System:** Amazon Linux 2023  
**Container Image:** Rocky Linux 9.3  
**Technology:** Docker, Linux, DNF, RPM, systemd  
**Total Practical Steps:** 34