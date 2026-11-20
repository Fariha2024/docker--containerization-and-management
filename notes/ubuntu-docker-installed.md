# Class 13: Docker Installation on Ubuntu Server

## 1. Introduction

Docker is a containerization platform that allows developers and system administrators to build, run, and manage applications inside lightweight, isolated environments called **containers**.

In this practical lab, we will install **Docker Community Edition (Docker CE)** on an Ubuntu Server using the APT package manager.

### Learning Objectives

By completing this practical, you will learn how to:

- Access an Ubuntu server with root privileges.
- Identify the installed Ubuntu version.
- Check Docker package availability.
- Update the APT package index.
- Install required dependencies.
- Configure Docker's official GPG signing key.
- Add the official Docker APT repository.
- Install Docker CE.
- Verify Docker installation and service status.

---

# SECTION 1: Docker Installation on Ubuntu

## STEP 1: Connect to Ubuntu Server

```bash
sudo -i
```

**Explanation:**

This command switches the current user to the root user.

- `sudo` – Executes a command with elevated privileges.
- `-i` – Opens an interactive root login shell.

**Why do we use it?**

Installing Docker and modifying system configuration files require administrative privileges. Switching to root allows us to perform these operations.

**Expected result:**

Your terminal prompt may change from:

```bash
ubuntu@ip-172-31-32-177:~$
```

To:

```bash
root@ip-172-31-32-177:~#
```

The `#` symbol indicates that you are operating as root.

**Important:** Once you have switched to root, using `sudo` before every command is unnecessary.

---

## STEP 2: Check Ubuntu Version

```bash
cat /etc/os-release
```

**Explanation:**

This command displays information about the Linux operating system installed on the server.

- `cat` – Displays the contents of a file.
- `/etc/os-release` – Contains operating system identification information.

**Why do we use it?**

Docker installation instructions and repositories depend on the Linux distribution and its version.

We need to identify whether our server is running Ubuntu and determine its release version.

**Expected output (example):**

```bash
NAME="Ubuntu"
VERSION="24.04.3 LTS (Noble Numbat)"
ID=ubuntu
VERSION_ID="24.04"
VERSION_CODENAME=noble
```

**Important terms:**

- `NAME` – Operating system name.
- `VERSION_ID` – Ubuntu version number.
- `VERSION_CODENAME` – Ubuntu release codename.

For example, Ubuntu 24.04 uses the codename `noble`.

---

## STEP 3: Check Docker CE Package

```bash
apt info docker-ce
```

**Explanation:**

This command displays information about the Docker Community Edition package available through the configured APT repositories.

- `apt` – Ubuntu's package management tool.
- `info` – Displays package information.
- `docker-ce` – Docker Community Edition package.

**Why do we use it?**

To check whether information about the Docker CE package is available through our configured package repositories.

**Possible result:**

If the Docker repository has not been configured, you may see:

```bash
E: No packages found
```

This is not necessarily an error with Ubuntu. It can happen because the official Docker repository has not yet been added.

We will configure that repository in the upcoming steps.

---

## STEP 4: Update Package Index

```bash
sudo apt-get update
```

**Explanation:**

This command refreshes the local APT package index by downloading the latest package information from configured repositories.

- `sudo` – Executes the command with administrative privileges.
- `apt-get` – APT command-line package management tool.
- `update` – Refreshes package information.

**Why do we use it?**

Before installing software, we should update the package index so Ubuntu knows about the latest available package versions.

**Important difference:**

`apt-get update` does not upgrade installed software. It only refreshes the package information.

---

## STEP 5: Install Required Tools

```bash
sudo apt-get install ca-certificates curl
```

**Explanation:**

This command installs two required utilities.

### 1. ca-certificates

Provides trusted Certificate Authority (CA) certificates.

**Purpose:**

These certificates allow the system to verify the authenticity of secure HTTPS connections.

### 2. curl

A command-line utility used to transfer data from or to a server.

**Purpose:**

We will use `curl` to download Docker's official GPG signing key.

**Why do we need these tools?**

- `ca-certificates` helps establish trusted HTTPS connections.
- `curl` downloads files from the internet.

---

## STEP 6: Create APT Keyrings Directory

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

**Explanation:**

This command creates a directory where APT repository signing keys can be stored.

Let's understand its options:

- `install` – Copies files or creates directories while setting permissions.
- `-m 0755` – Sets directory permissions.
- `-d` – Creates a directory instead of copying a file.
- `/etc/apt/keyrings` – Destination directory.

**What does 0755 mean?**

Linux permissions are represented by numbers.

| Permission | Meaning |
|---|---|
| 7 | Read + Write + Execute |
| 5 | Read + Execute |
| 5 | Read + Execute |

Therefore, `0755` means:

- Owner: Read, write and execute.
- Group: Read and execute.
- Others: Read and execute.

**Why do we use it?**

APT needs a dedicated location to store repository signing keys securely.

---

## STEP 7: Download Docker GPG Key

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

**Explanation:**

This command downloads Docker's official GPG signing key.

Let's understand the options:

| Option | Meaning |
|---|---|
| `curl` | Downloads data from a URL |
| `-f` | Fails on HTTP errors |
| `-s` | Silent mode |
| `-S` | Displays errors even in silent mode |
| `-L` | Follows redirects |
| `-o` | Saves output to a specified file |

**Source URL:**

```text
https://download.docker.com/linux/ubuntu/gpg
```

**Destination:**

```text
/etc/apt/keyrings/docker.asc
```

**What is a GPG key?**

GPG stands for GNU Privacy Guard.

A repository signing key helps APT verify that packages come from an authenticated software repository and have not been modified.

**Why do we use it?**

We need Docker's official signing key so Ubuntu can verify packages downloaded from Docker's repository.

---

## STEP 8: Set GPG Key Permissions

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

**Explanation:**

This command gives all users read permission for the Docker GPG key.

Let's understand the syntax:

- `chmod` – Changes file permissions.
- `a` – All users (owner, group and others).
- `+r` – Adds read permission.
- `/etc/apt/keyrings/docker.asc` – Target file.

**Why do we use it?**

APT needs permission to read the Docker signing key when verifying packages from the Docker repository.

**Important:**

This command does not change the contents of the GPG key. It only changes its permissions.

---

## STEP 9: Add Docker APT Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**Explanation:**

This is one of the most important steps in Docker installation.

We are adding Docker's official APT repository to Ubuntu so that the package manager can find and install Docker CE.

Let's break it down.

### 1. echo

```bash
echo "..."
```

Displays the repository configuration text.

### 2. dpkg --print-architecture

```bash
$(dpkg --print-architecture)
```

Identifies the system's processor architecture.

Examples:

- `amd64` – 64-bit x86 architecture.
- `arm64` – 64-bit ARM architecture.

### 3. signed-by

```bash
signed-by=/etc/apt/keyrings/docker.asc
```

Specifies which GPG key APT should use to verify packages from this repository.

### 4. Docker Repository URL

```text
https://download.docker.com/linux/ubuntu
```

This is Docker's official Ubuntu package repository.

### 5. Ubuntu Codename

```bash
$(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
```

This dynamically identifies the Ubuntu release codename.

For example:

- Ubuntu 22.04 = jammy
- Ubuntu 24.04 = noble

This ensures that the repository configuration matches the Ubuntu version.

### 6. stable

```text
stable
```

Specifies the stable release channel of Docker packages.

### 7. Pipe Operator ( | )

The pipe operator passes the output of one command as input to another command.

Here, it passes the repository configuration generated by `echo` to `tee`.

### 8. tee

```bash
sudo tee /etc/apt/sources.list.d/docker.list
```

Writes the repository configuration into the specified file.

### 9. > /dev/null

```bash
> /dev/null
```

Redirects the normal output to `/dev/null`, preventing the repository configuration from being printed on the terminal.

**Why do we use this command?**

Ubuntu needs to know where Docker packages are hosted. This command registers Docker's official repository with APT.

**Verify the repository configuration:**

```bash
cat /etc/apt/sources.list.d/docker.list
```

---

## STEP 10: Update Package Index Again

```bash
sudo apt-get update
```

**Explanation:**

We run this command again because we have added a new repository in Step 9.

This time, APT downloads package information from Docker's official repository as well as the other configured repositories.

**Why do we use it?**

Without updating the package index after adding the Docker repository, APT may not recognize the newly available Docker packages.

**Expected result:**

You should see APT retrieving package information from:

```text
https://download.docker.com/linux/ubuntu
```

---

## STEP 11: Install Docker CE

```bash
sudo apt install docker-ce
```

**Explanation:**

This command installs Docker Community Edition on the Ubuntu server.

- `apt` – Ubuntu package management tool.
- `install` – Installs software packages.
- `docker-ce` – Docker Community Edition.

**Why do we use it?**

After configuring the official Docker repository, we can install Docker Engine using APT.

APT automatically resolves and installs required dependencies.

**Additional recommendation:**

For a complete Docker installation following Docker's official instructions, you can install the following packages:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

| Package | Purpose |
|---|---|
| docker-ce | Docker Engine |
| docker-ce-cli | Docker command-line interface |
| containerd.io | Container runtime |
| docker-buildx-plugin | Advanced image building |
| docker-compose-plugin | Multi-container application management |

---

## STEP 12: Check Docker Service

```bash
systemctl status docker
```

**Explanation:**

This command displays the current status of the Docker service.

- `systemctl` – Manages system services using systemd.
- `status` – Displays service information.
- `docker` – Docker service name.

**Why do we use it?**

Installing Docker does not automatically tell us whether the Docker service is currently running.

This command helps us verify its status.

**Expected output:**

```text
● docker.service - Docker Application Container Engine
     Loaded: loaded
     Active: active (running)
```

**Important service statuses:**

| Status | Meaning |
|---|---|
| active (running) | Docker service is running |
| inactive (dead) | Docker service is stopped |
| failed | Docker service encountered an error |

To start Docker manually:

```bash
sudo systemctl start docker
```

To enable Docker at system startup:

```bash
sudo systemctl enable docker
```

---

## STEP 13: Check Docker Version

```bash
docker --version
```

**Explanation:**

This command displays the installed Docker CLI version.

**Why do we use it?**

It provides a quick way to confirm that the Docker command-line tool is installed and accessible.

**Expected output (example):**

```text
Docker version 28.x.x, build xxxxxxx
```

The actual version and build number will depend on the installed release.

---

# SECTION 2: Verify Docker Installation

After completing the installation, run:

```bash
docker run hello-world
```

**Explanation:**

This command runs a test container to verify that Docker Engine can successfully download an image and execute a container.

What happens internally?

1. Docker checks whether the `hello-world` image exists locally.
2. If it does not exist, Docker downloads it from Docker Hub.
3. Docker creates and starts a container using that image.
4. The container displays a confirmation message.
5. The container exits.

**Expected output:**

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

If you receive this message, your Docker installation is working successfully.

---

# SECTION 3: Important Concepts Learned

| Concept | Explanation |
|---|---|
| APT | Ubuntu package management system |
| Docker CE | Docker Community Edition |
| GPG Key | Verifies repository package authenticity |
| APT Repository | Location where software packages are hosted |
| curl | Downloads files from URLs |
| chmod | Changes file permissions |
| dpkg | Debian package management utility |
| systemctl | Manages Linux system services |
| containerd | Container runtime |
| Docker Hub | Online registry for Docker images |

---

# SECTION 4: Important Commands Summary

| Command | Purpose |
|---|---|
| `sudo -i` | Switch to root user |
| `cat /etc/os-release` | Check Ubuntu version |
| `apt info docker-ce` | Check Docker package information |
| `apt-get update` | Refresh package index |
| `apt-get install ca-certificates curl` | Install required tools |
| `install -m 0755 -d /etc/apt/keyrings` | Create keyrings directory |
| `curl` | Download Docker GPG key |
| `chmod a+r` | Set GPG key read permissions |
| `tee` | Save repository configuration |
| `apt install docker-ce` | Install Docker CE |
| `systemctl status docker` | Check Docker service |
| `docker --version` | Check Docker version |
| `docker run hello-world` | Test Docker installation |

---

# SECTION 5: Key Takeaways

By completing this practical lab, we have learned how to install Docker Engine on Ubuntu using Docker's official APT repository.

The installation process follows this sequence:

1. Connect to Ubuntu Server.
2. Identify the Ubuntu version.
3. Update the package index.
4. Install required dependencies.
5. Configure Docker's GPG signing key.
6. Add Docker's official APT repository.
7. Refresh the package index.
8. Install Docker Engine.
9. Verify the Docker service.
10. Test Docker using the `hello-world` container.

**Final Outcome:** Docker Engine is installed and verified on the Ubuntu Server, providing an environment for running and managing containers.

---

**Practical Lab Completed: Docker Installation on Ubuntu Server**

**Technology:** Docker | Ubuntu Linux | APT Package Management | Linux System Administration