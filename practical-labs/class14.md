# 🔷 Docker Class 14 – Dockerfile, Custom Images, Volumes, Docker Hub & ENTRYPOINT/CMD

## 🔹 Overview

This class focuses on building custom Docker images using a `Dockerfile`, deploying an Apache web server inside a Rocky Linux container, working with Docker volumes, publishing custom images to Docker Hub, and understanding the difference between `CMD` and `ENTRYPOINT`.

The practical work also demonstrates how container data behaves when volumes are recreated or shared between containers, how to build using a non-default Dockerfile name, and how to copy files into a custom image.

## 🔹 Topics Covered

- 🔵 Docker image and container cleanup
- 🔵 Creating a project directory and preparing web content
- 🔵 Understanding Dockerfile instructions
- 🔵 Installing and configuring Apache HTTP Server (`httpd`)
- 🔵 Building custom Docker images
- 🔵 Running containers with automatic port mapping
- 🔵 Understanding Docker volumes and volume mountpoints
- 🔵 Testing a web server from a browser and with `curl`
- 🔵 Creating a Docker Hub repository
- 🔵 Logging in to Docker Hub and tagging images
- 🔵 Pushing and pulling custom images
- 🔵 Understanding `CMD` and `ENTRYPOINT`
- 🔵 Sharing a Docker volume between containers
- 🔵 Building an image with a custom Dockerfile filename
- 🔵 Copying a Dockerfile into an image
- 🔵 Checking file and directory disk usage

---

# 🔷 SECTION 1: Prepare the Docker Host

### 🔹 STEP 1: Connect to the Linux Server

```bash
sudo -i
```

Switch to the root user.

### 🔹 STEP 2: Check Existing Docker Images

```bash
docker images
```

The class begins by checking the existing Rocky Linux and MySQL images.

### 🔹 STEP 3: Remove Selected Images

```bash
docker rmi -f IMAGE_ID
```

Run the command for the Rocky Linux image ID and then for the MySQL image ID.

- `rmi` removes Docker images.
- `-f` forces image removal when Docker permits it.
- Replace `IMAGE_ID` with the actual image ID shown by `docker images`.

### 🔹 STEP 4: Check Images Again

```bash
docker images
```

### 🔹 STEP 5: Remove All Containers

```bash
docker rm -f $(docker ps -aq)
```

This force-removes all containers on the current Docker host, including running containers.

### 🔹 STEP 6: Verify Container Cleanup

```bash
docker ps -a
```

### 🔹 STEP 7: Create the Project Directory

```bash
mkdir myproject
cd myproject
pwd
ls -ltr
```

The working directory should be `/root/myproject` when following the class setup as root.

### 🔹 STEP 8: Create the Web Page

```bash
echo "hello world" > index.html
ls -ltr
cat index.html
```

This creates `index.html` in the project directory. The file will later be copied into the custom Docker image.

---

# 🔷 SECTION 2: Create the Dockerfile for an Apache Web Server

### 🔹 STEP 9: Create the Dockerfile

```bash
vim Dockerfile
```

Add the following instructions:

```dockerfile
FROM rockylinux:9.3.20231119

LABEL maintainer="ekamjeet@gmail.com"
LABEL description="This is my test webserver"

RUN dnf update -y && \
    dnf install httpd iputils vim net-tools -y

COPY ./index.html /var/www/html

RUN chmod -R 755 /var/www/html && \
    chown -R apache:apache /var/www/html

WORKDIR /var/www/html

EXPOSE 80

VOLUME /var/www/html

CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
```

Save and exit Vim:

```vim
:wq!
```

### 🔹 STEP 10: Understand the Dockerfile Instructions

| Instruction | Purpose |
|---|---|
| `FROM` | Selects the base image for the new image |
| `LABEL` | Adds metadata such as maintainer and description |
| `RUN` | Executes commands while building the image |
| `COPY` | Copies files from the build context into the image |
| `WORKDIR` | Sets the default working directory for later instructions and container processes |
| `EXPOSE` | Documents the container port the application is expected to use |
| `VOLUME` | Declares a mount point for persistent or externally managed data |
| `CMD` | Provides the default command or arguments when a container starts |

### 🔹 STEP 11: Understand the Packages Installed

The Dockerfile installs the following Rocky Linux packages:

| Package | Purpose |
|---|---|
| `httpd` | Apache HTTP Server |
| `iputils` | Provides utilities such as `ping` |
| `vim` | Text editor |
| `net-tools` | Provides legacy networking tools such as `ifconfig` and `netstat` |

### 🔹 STEP 12: Understand COPY

```dockerfile
COPY ./index.html /var/www/html
```

- `./index.html` is the source file in the Docker build context.
- `/var/www/html` is the destination directory inside the image.
- The file is copied from the Docker host's project directory into the image during the build.

### 🔹 STEP 13: Understand WORKDIR

```dockerfile
WORKDIR /var/www/html
```

When a user opens a shell in a container created from this image, this is the default working directory unless another directory is specified.

### 🔹 STEP 14: Understand VOLUME

```dockerfile
VOLUME /var/www/html
```

This declares `/var/www/html` as a volume mount point. When a container is created without an explicit mount for this path, Docker normally creates an anonymous volume for it.

Docker manages the volume data separately from the container's writable layer.

### 🔹 STEP 15: Understand the Apache Startup Command

```dockerfile
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
```

- `/usr/sbin/httpd` starts the Apache HTTP Server.
- `-D FOREGROUND` tells Apache to remain in the foreground.
- A foreground process allows the container's main process to remain active.

In a container, the main process is PID 1 inside that container. The container normally stops when its main process exits.

---

# 🔷 SECTION 3: Understand Apache HTTP Server on Rocky Linux

These commands demonstrate Apache directly on a Linux server. They are separate from building the Docker image.

### 🔹 STEP 16: Install Apache on the Linux Host

```bash
yum install httpd -y
```

### 🔹 STEP 17: Start Apache

```bash
systemctl start httpd
```

### 🔹 STEP 18: Check Apache Status

```bash
systemctl status httpd
```

The class output showed the Apache process running with a command similar to:

```text
/usr/sbin/httpd -DFOREGROUND
```

### 🔹 STEP 19: Locate the Apache Executable

```bash
cd /usr/sbin/
ls -l httpd
```

Alternative:

```bash
ls -l | grep httpd
```

### 🔹 STEP 20: Check the Current Directory

```bash
pwd
```

Expected directory:

```text
/usr/sbin
```

### 🔹 STEP 21: View Apache Configuration

```bash
vim /etc/httpd/conf/httpd.conf
```

This is the main Apache HTTP Server configuration file on Rocky Linux.

### 🔹 STEP 22: Stop and Remove Host Apache

```bash
systemctl stop httpd
yum remove httpd -y
rpm -q httpd
systemctl status httpd
```

These commands stop the host service, remove the host package, check whether the package remains installed, and check the service status.

---

# 🔷 SECTION 4: Build the Custom Docker Image

### 🔹 STEP 23: Return to the Project Directory

```bash
cd /root/myproject
ls -ltr
```

The project directory should contain:

```text
Dockerfile
index.html
```

### 🔹 STEP 24: Build the Custom Image

```bash
docker build -t mycustomweb:v0.1 --no-cache .
```

Explanation:

- `docker build` builds an image from a Dockerfile.
- `-t` assigns a name and tag to the image.
- `mycustomweb` is the repository/image name.
- `v0.1` is the tag.
- `--no-cache` tells Docker not to use cached build layers.
- `.` identifies the current directory as the build context.

### 🔹 STEP 25: Understand the Dot (`.`) in the Build Command

```bash
docker build -t mycustomweb:v0.1 --no-cache .
```

The final dot means the current directory is the build context. Docker looks for a file named `Dockerfile` in that context by default and makes the context files available to build instructions such as `COPY`.

Useful directory commands:

```bash
ls
ls -ltr
ls -a
pwd
ls -a /root/myproject
```

- `ls -a` displays hidden files and directories as well as ordinary entries.
- `.` represents the current directory.
- `..` represents the parent directory.

### 🔹 STEP 26: Verify the Custom Image

```bash
docker images
```

Look for:

```text
mycustomweb   v0.1
```

### 🔹 STEP 27: Check Docker Volumes and Networks

```bash
docker volume ls
docker network ls
```

To remove one unused volume:

```bash
docker volume rm VOLUME_NAME
```

Replace `VOLUME_NAME` with the actual volume name. A volume cannot be removed while it is in use by a container.

---

# 🔷 SECTION 5: Run the Custom Web Server Container

### 🔹 STEP 28: Create Container C1 with Automatic Port Mapping

```bash
docker run --name c1 -dit -P mycustomweb:v0.1
```

The class used the image ID; the image name and tag are shown here as an equivalent, easier-to-read reference.

- `--name c1` assigns the container name.
- `-d` runs the container in the background.
- `-i` keeps standard input open.
- `-t` allocates a terminal.
- `-P` publishes exposed container ports to dynamically assigned host ports.

### 🔹 STEP 29: Check the Container

```bash
docker ps -a
```

Confirm that container `c1` is running and inspect its `PORTS` column.

Example format:

```text
0.0.0.0:32769->80/tcp
```

The host port in this example is `32769`, and it forwards traffic to port `80` inside the container. Your assigned host port may be different.

### 🔹 STEP 30: Inspect the Docker Volume

```bash
docker volume ls
docker inspect VOLUME_NAME
```

In the volume inspection output, find the `Mountpoint` field. The class output showed a path in this format:

```text
/var/lib/docker/volumes/VOLUME_ID/_data
```

Use the actual mountpoint returned by your system.

### 🔹 STEP 31: Check the Volume's Web Page

```bash
ls -ltr "/var/lib/docker/volumes/VOLUME_ID/_data"
cat "/var/lib/docker/volumes/VOLUME_ID/_data/index.html"
```

Expected initial file content:

```text
hello world
```

### 🔹 STEP 32: Create a Docker Hub Repository

1. Open [Docker Hub](https://hub.docker.com/).
2. Sign in to your account.
3. Open **My Hub**.
4. Go to **Repositories**.
5. Select **Create repository**.
6. Enter the repository name used in the class: `tgdevops13june`.
7. Select **Public** if that is the intended visibility.
8. Create the repository.

The class used the Docker Hub namespace `paragdongre1997`. Use your own Docker Hub namespace if it differs.

### 🔹 STEP 33: Test the Web Server from the Host

Find the host's public IP address:

```bash
curl ifconfig.io
```

Check the published port:

```bash
docker ps -a
```

Then test the mapped host port locally:

```bash
curl http://localhost:32769
```

Expected response:

```text
hello world
```

To test from a browser, open:

```text
http://PUBLIC_IP_ADDRESS:32769
```

Replace the example port with the port shown for your container. The cloud server's security rules must also allow inbound traffic to that host port.

### 🔹 STEP 34: Enter Container C1

```bash
docker exec -it c1 bash
pwd
```

The expected working directory is:

```text
/var/www/html
```

This is because the Dockerfile contains:

```dockerfile
WORKDIR /var/www/html
```

Exit the container shell:

```bash
exit
```

You can also use `Ctrl + D` to exit an interactive shell.

---

# 🔷 SECTION 6: Log In to Docker Hub, Tag and Push the Image

### 🔹 STEP 35: Log In to Docker Hub

```bash
docker login
```

Enter your Docker Hub username and a Docker Hub access token when prompted.

**Security note:** Do not place a real password or access token in a GitHub repository, Markdown notes, screenshots, or shared terminal output. The credential shown in the original raw class notes has intentionally not been reproduced. If it was a real token, revoke it in Docker Hub and create a new one.

### 🔹 STEP 36: Tag the Custom Image

```bash
docker tag mycustomweb:v0.1 YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.0
```

The class used this image reference:

```text
paragdongre1997/tgdevops13june:web-v1.0
```

Explanation:

- `docker tag` creates another image reference for the same image.
- `YOUR_DOCKERHUB_USERNAME` is your Docker Hub namespace.
- `tgdevops13june` is the repository name.
- `web-v1.0` is the tag.

### 🔹 STEP 37: Verify the Image Tags

```bash
docker images
```

You should see both the local image reference and the Docker Hub repository reference. They may point to the same image ID before any new image is built.

### 🔹 STEP 38: Push the Image to Docker Hub

```bash
docker image push YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.0
```

This uploads the tagged image to Docker Hub.

---

# 🔷 SECTION 7: Understand CMD and ENTRYPOINT

This section compares different ways to start Apache as the main process inside a container.

### 🔹 STEP 39: Build the Image with Two CMD Instructions (Class Experiment)

The class changed the end of the Dockerfile to:

```dockerfile
CMD ["/usr/sbin/httpd"]
CMD ["-D", "FOREGROUND"]
```

Then built:

```bash
docker build -t YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.1 --no-cache .
```

**Important Dockerfile rule:** When multiple `CMD` instructions appear in one Dockerfile, only the last `CMD` takes effect. In this example, the effective command becomes `-D FOREGROUND`, which is not a standalone executable. The container is therefore expected to fail to start.

Check the image and container output:

```bash
docker images
docker run --name c2 -dit -P YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.1
docker ps -a
docker logs c2
```

If the container has exited, `docker exec` will not work because `docker exec` requires a running container.

### 🔹 STEP 40: Correct the Startup Command Using ENTRYPOINT and CMD

Edit the Dockerfile:

```bash
vim Dockerfile
```

Use:

```dockerfile
FROM rockylinux:9.3.20231119

LABEL maintainer="ekamjeet@gmail.com"
LABEL description="This is my test webserver"

RUN dnf update -y && \
    dnf install httpd iputils vim net-tools -y

COPY ./index.html /var/www/html

RUN chmod -R 755 /var/www/html && \
    chown -R apache:apache /var/www/html

WORKDIR /var/www/html

EXPOSE 80

VOLUME /var/www/html

ENTRYPOINT ["/usr/sbin/httpd"]
CMD ["-D", "FOREGROUND"]
```

Build the image:

```bash
docker build -t YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2 --no-cache .
```

### 🔹 STEP 41: Remove the Failed Container and Run C3

```bash
docker rm -f c2
docker run --name c3 -dit -P YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2
docker ps -a
```

Enter the container:

```bash
docker exec -it c3 bash
pwd
ls -ltr
cat index.html
```

Expected working directory:

```text
/var/www/html
```

Expected initial page content:

```text
hello world
```

Exit the container:

```bash
exit
```

### 🔹 STEP 42: Test C3 Through Its Published Port

```bash
docker ps -a
curl http://localhost:HOST_PORT
```

Replace `HOST_PORT` with the host port mapped to container port 80 in the `PORTS` column.

You can also test through a browser using:

```text
http://PUBLIC_IP_ADDRESS:HOST_PORT
```

---

# 🔷 SECTION 8: Observe How Docker Volumes Preserve Data

The Dockerfile declares:

```dockerfile
VOLUME /var/www/html
```

Docker creates an anonymous volume for this mount point when a container is created without another mount being supplied. Each new container normally receives its own anonymous volume unless an existing volume is explicitly mounted.

### 🔹 STEP 43: List and Inspect the Volumes

```bash
docker volume ls
docker inspect VOLUME_NAME
```

The class identified separate volumes for containers C2 and C3. Use the actual volume names returned by your Docker host.

### 🔹 STEP 44: Inspect C2's Volume Data

```bash
cd "/var/lib/docker/volumes/C2_VOLUME_ID/_data"
ls -l
cat index.html
```

The class observed the original content:

```text
hello world
```

### 🔹 STEP 45: Inspect C3's Volume Data

```bash
cd "/var/lib/docker/volumes/C3_VOLUME_ID/_data"
ls -l
cat index.html
```

Append text to C3's volume data:

```bash
echo "good job" >> index.html
cat index.html
```

Expected content:

```text
hello world
good job
```

### 🔹 STEP 46: Compare the Containers

The class demonstrated that:

- C2's volume retained its original `index.html` content.
- C3's separate volume contained the additional `good job` line.
- Updating one container's separate volume did not automatically update the other container's volume.

**Key concept:** Separate anonymous volumes are separate data stores. A change in one is not automatically shared with another.

---

# 🔷 SECTION 9: Push, Clean Up and Pull the Custom Image

### 🔹 STEP 47: Push the Updated Image

```bash
docker image push YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2
```

### 🔹 STEP 48: Remove Containers, Images and Selected Volumes

```bash
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)
docker volume ls
docker volume rm VOLUME_NAME
docker system prune
```

Use the actual volume name or names when removing volumes.

**Warning:** These cleanup commands can delete containers, images, and unused Docker resources. Check what is present before running them. `docker system prune` does not automatically remove every volume unless the volume-prune option is explicitly used.

### 🔹 STEP 49: Verify Cleanup

```bash
docker images
docker ps -a
docker volume ls
```

### 🔹 STEP 50: Pull the Image from Docker Hub

```bash
docker pull YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2
docker images
```

This demonstrates retrieving the published image from Docker Hub after local cleanup.

### 🔹 STEP 51: Create Container C11

```bash
docker run --name c11 -dit -P YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2
docker ps -a
```

Enter the container:

```bash
docker exec -it c11 bash
pwd
cat index.html
```

Expected working directory and initial content:

```text
/var/www/html
hello world
```

Exit:

```bash
exit
```

### 🔹 STEP 52: Inspect C11's Volume

```bash
docker volume ls
docker volume inspect VOLUME_NAME
```

Find the `Mountpoint` path and inspect the file:

```bash
ls -l "/var/lib/docker/volumes/VOLUME_ID/_data"
cat "/var/lib/docker/volumes/VOLUME_ID/_data/index.html"
```

Append a line to the volume's `index.html`:

```bash
echo "hello parag and oshin i hope you got this point" >> "/var/lib/docker/volumes/VOLUME_ID/_data/index.html"
cat "/var/lib/docker/volumes/VOLUME_ID/_data/index.html"
```

Test the web page:

```bash
curl http://localhost:HOST_PORT
```

The response should now include the original `hello world` content and the newly appended line.

### 🔹 STEP 53: Create Container C12

```bash
docker run --name c12 -dit -P YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2
docker ps -a
```

C12 receives its own anonymous volume. Its page initially contains the image's default `hello world` content.

Test it using the host port shown by `docker ps`:

```bash
curl http://localhost:C12_HOST_PORT
```

### 🔹 STEP 54: Compare C11 and C12

```bash
docker volume ls
docker ps -a
```

The class observed:

- C11 displayed its modified volume data.
- C12 displayed its own default volume data.
- The two containers had different anonymous volumes.

### 🔹 STEP 55: Create C13 Using C11's Existing Volume

To make C13 use the same web content as C11, mount C11's existing volume at `/var/www/html`:

```bash
docker run --name c13 \
  -v C11_VOLUME_NAME:/var/www/html \
  -dit -P \
  YOUR_DOCKERHUB_USERNAME/tgdevops13june:web-v1.2
```

The class used the actual volume ID/name and image ID. Replace the placeholders with the values from your own host.

### 🔹 STEP 56: Verify C13's Shared Volume Content

```bash
docker ps -a
docker volume ls
curl http://localhost:C13_HOST_PORT
```

C13 should display the data already stored in C11's volume because both containers now use the same volume at `/var/www/html`.

**Key concept:** Mounting the same Docker volume into multiple containers allows those containers to access the same stored files at the mounted path.

---

# 🔷 SECTION 10: Build with a Custom Dockerfile Filename

This section demonstrates using a Dockerfile with a different filename and the `-f` option.

### 🔹 STEP 57: Clean Up the Lab Resources

```bash
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)
docker volume ls
docker volume rm VOLUME_NAME
docker system prune
```

Only remove volumes that are no longer needed.

### 🔹 STEP 58: Create a Dockerfile with a Different Name

```bash
vim dockerfile123
```

The class used:

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install curl -y

ENTRYPOINT ["echo", "hello"]
ENTRYPOINT ["curl", "-s"]

CMD ["https://technicalguftgu.in/"]
```

**Important Dockerfile rule:** Only the last `ENTRYPOINT` instruction takes effect. In this file, the effective `ENTRYPOINT` is `["curl", "-s"]`; the earlier `echo` instruction is ignored. The `CMD` supplies the default URL argument.

### 🔹 STEP 59: Check the Project Files

```bash
ls -ltr
```

The class directory contained files such as:

```text
index.html
Dockerfile
dockerfile123
```

### 🔹 STEP 60: Move the Default Dockerfile

```bash
mv Dockerfile /mnt/
ls -ltr
```

The default file named `Dockerfile` is no longer in the current build context.

### 🔹 STEP 61: Build Without Specifying the Dockerfile Name

```bash
docker build -t myimg:v1.0 --no-cache .
```

The class received an error because Docker could not find a file named `Dockerfile` in the build context.

### 🔹 STEP 62: Build by Specifying the Dockerfile Filename

```bash
docker build -t myimg:v1.0 --no-cache -f dockerfile123 .
```

Explanation:

- `-f dockerfile123` tells Docker which file to use as the Dockerfile.
- `.` remains the build context.
- `myimg:v1.0` is the image name and tag.

### 🔹 STEP 63: Run the Image and Check Its Logs

```bash
docker images
docker run --name c15 -dit myimg:v1.0
docker ps -a
docker logs c15
```

The container may exit after the command completes. A short-lived command container can exit normally after it finishes its work.

### 🔹 STEP 64: Understand Runtime Arguments with ENTRYPOINT

The class tried supplying a URL after the image name:

```bash
docker run --name c18 -dit myimg:v1.0 https://google.com
```

For an exec-form `ENTRYPOINT`, text supplied after the image name is appended as arguments to the entrypoint command. It does not replace the `ENTRYPOINT`.

For example, with:

```dockerfile
ENTRYPOINT ["curl", "-s"]
CMD ["https://technicalguftgu.in/"]
```

the default startup command is equivalent to:

```bash
curl -s https://technicalguftgu.in/
```

Supplying another argument at runtime replaces the default `CMD` arguments:

```bash
docker run --rm myimg:v1.0 https://google.com
```

This is equivalent to:

```bash
curl -s https://google.com
```

### 🔹 STEP 65: Test an Invalid ENTRYPOINT Experiment

The raw class notes then changed the Dockerfile to:

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install curl -y

ENTRYPOINT ["echo", "hello"]
ENTRYPOINT ["curl", "-s"]
ENTRYPOINT ["https://technicalguftgu.in/"]
```

Build:

```bash
docker build -t myimg:v1.1 --no-cache -f dockerfile123 .
```

**Important:** The last `ENTRYPOINT` is treated as the executable. A URL is not an executable command, so a container created from this image is expected to fail at startup. The image build itself can still complete successfully because Docker does not normally run the entrypoint during image building.

The class also tried:

```bash
docker run --name c20 -dit myimg:v1.1 https://yahoo.com
docker ps -a
```

The runtime argument does not replace the invalid `ENTRYPOINT`.

---

# 🔷 SECTION 11: Copy a Dockerfile into the Custom Image

### 🔹 STEP 66: Create a Second Project Directory

```bash
mkdir myproject2
cd myproject2
```

### 🔹 STEP 67: Copy the Dockerfile into the New Directory

```bash
cp /mnt/Dockerfile .
ls -ltr
```

### 🔹 STEP 68: Copy the Web Page into the New Directory

```bash
cp /root/myproject/index.html /root/myproject2/
ls -ltr
```

The directory should now contain:

```text
Dockerfile
index.html
```

### 🔹 STEP 69: Edit the Dockerfile

```bash
vim Dockerfile
```

Add the following instruction to the Dockerfile:

```dockerfile
COPY ./Dockerfile /mnt
```

This copies the Dockerfile from the build context into `/mnt` inside the image. It is a separate copy operation from the host-side commands used to prepare the build directory.

Save and exit:

```vim
:wq!
```

### 🔹 STEP 70: Build the Final Custom Image

```bash
docker build -t YOUR_DOCKERHUB_USERNAME/tgdevops13june:webfinal-v1.3 --no-cache .
```

### 🔹 STEP 71: Push the Final Image to Docker Hub

```bash
docker image push YOUR_DOCKERHUB_USERNAME/tgdevops13june:webfinal-v1.3
```

### 🔹 STEP 72: Verify the Image Tags

```bash
docker images
```

The class listed image versions including:

```text
webfinal-v1.3
web-v1.1
web-v1.0
```

Your local image list may differ depending on which cleanup commands you have run.

### 🔹 STEP 73: Run the Final Container

```bash
docker run --name c31 -dit -P YOUR_DOCKERHUB_USERNAME/tgdevops13june:webfinal-v1.3
docker ps -a
```

### 🔹 STEP 74: Verify the Copied Dockerfile Inside the Container

```bash
docker exec -it c31 bash
ls -ltr
ls -l /mnt/
```

The class expected to find `Dockerfile` under `/mnt/`.

### 🔹 STEP 75: Check the File's Disk Usage

```bash
du -hs /mnt/Dockerfile
```

`du -hs` displays a human-readable summary of the disk space used by the specified file or directory.

The class output showed:

```text
4.0K    /mnt/Dockerfile
```

Exit the container:

```bash
exit
```

---

# 🔷 Important Docker Commands – Quick Reference

| Command | Description |
|---|---|
| `docker images` | List local images |
| `docker rmi -f IMAGE_ID` | Force-remove an image |
| `docker ps -a` | List all containers |
| `docker rm -f $(docker ps -aq)` | Force-remove all containers |
| `docker build -t name:tag .` | Build an image using the default Dockerfile |
| `docker build -f FILE -t name:tag .` | Build using a specified Dockerfile |
| `docker run --name c1 -dit -P IMAGE` | Create and start a container with automatic port publishing |
| `docker exec -it c1 bash` | Open Bash in a running container |
| `docker logs c1` | Display container logs |
| `docker volume ls` | List volumes |
| `docker volume inspect NAME` | Display volume details |
| `docker volume rm NAME` | Remove a volume |
| `docker tag SOURCE TARGET` | Add another tag/reference to an image |
| `docker login` | Authenticate to a container registry |
| `docker image push IMAGE` | Push an image to a registry |
| `docker pull IMAGE` | Pull an image from a registry |
| `docker system prune` | Remove unused Docker data covered by the prune operation |
| `curl http://localhost:PORT` | Test a service through a host port |
| `du -hs PATH` | Display summarized disk usage |

---

# 🔷 Important Notes and Lab Considerations

🔹 **Image IDs:** Replace `IMAGE_ID` with the actual ID or use the image name and tag. Image IDs can change when a new image is built.

🔹 **Build context:** The final `.` in `docker build` means the current directory is sent as the build context. Files referenced by `COPY` must be available inside that context.

🔹 **Dockerfile filename:** Docker looks for a file named `Dockerfile` by default. Use `-f filename` when the file has another name, such as `dockerfile123`.

🔹 **CMD:** Only the last `CMD` instruction in a Dockerfile takes effect. Use one `CMD` instruction for the default command or default arguments.

🔹 **ENTRYPOINT:** Only the last `ENTRYPOINT` instruction takes effect. With exec form, runtime arguments are appended to `ENTRYPOINT` and replace the default `CMD` arguments.

🔹 **Apache foreground process:** The container's main service should remain in the foreground. The class's working form is:

```dockerfile
ENTRYPOINT ["/usr/sbin/httpd"]
CMD ["-D", "FOREGROUND"]
```

🔹 **EXPOSE vs port publishing:** `EXPOSE 80` documents the container port. It does not publish the port by itself. `-P` publishes exposed ports to dynamically assigned host ports.

🔹 **Docker volumes:** `VOLUME /var/www/html` declares a mount point. Docker normally creates an anonymous volume for each new container unless a volume or bind mount is explicitly supplied.

🔹 **Volume persistence:** A volume is managed separately from a container's writable layer. Removing a container does not necessarily remove its volume.

🔹 **Sharing a volume:** To share the same data between containers, mount the same volume name into each container at the required path, for example:

```bash
docker run --name c13 -v C11_VOLUME_NAME:/var/www/html -dit -P IMAGE
```

🔹 **Docker Hub credentials:** Never save passwords or access tokens in source files or GitHub. Use `docker login` interactively and protect access tokens. Revoke any real token that has been exposed.

🔹 **Cleanup warning:** Commands such as `docker rm -f $(docker ps -aq)` and `docker rmi -f $(docker images -aq)` affect all containers or images on the current Docker host. Review the resources first.

🔹 **Host port and cloud firewall:** A published Docker port must also be allowed by the cloud server's security rules before it can be reached from an external browser.

🔹 **CMD/ENTRYPOINT troubleshooting:** An image can build successfully even if its startup command is invalid. Use `docker logs CONTAINER_NAME` and `docker inspect` to investigate a container that exits immediately.

---

# 🔷 Learning Outcomes

After completing this practical lab, the learner will have hands-on experience with:

- 🔵 Preparing a Linux project directory and web page.
- 🔵 Writing a Dockerfile for a Rocky Linux-based Apache web server.
- 🔵 Understanding `FROM`, `LABEL`, `RUN`, `COPY`, `WORKDIR`, `EXPOSE`, `VOLUME`, `CMD`, and `ENTRYPOINT`.
- 🔵 Building and tagging custom Docker images.
- 🔵 Running containers with automatic port mapping.
- 🔵 Testing an Apache web server using `curl` and a browser.
- 🔵 Inspecting Docker volumes and their mountpoints.
- 🔵 Understanding anonymous volumes and shared volumes.
- 🔵 Publishing and pulling custom images through Docker Hub.
- 🔵 Understanding how `CMD` and `ENTRYPOINT` behave.
- 🔵 Building images with a non-default Dockerfile filename.
- 🔵 Copying files into an image and checking disk usage.

---

**Practical Lab:** Dockerfile, Custom Images, Volumes & Docker Hub  
**Platform:** Linux / Rocky Linux / Ubuntu containers  
**Technology:** Docker, Apache HTTP Server, Docker Hub, Docker Volumes  
**Class:** 14

---

## 🔹 Before Publishing to GitHub

- [ ] Replace `YOUR_DOCKERHUB_USERNAME` with your own Docker Hub username where appropriate.
- [ ] Replace sample volume names, image IDs, and host ports with values from your own lab.
- [ ] Do not add passwords, access tokens, or other credentials to this Markdown file.
- [ ] Keep the failed `CMD` and `ENTRYPOINT` experiments labelled as learning experiments, not recommended production configurations.
