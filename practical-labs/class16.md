# 🔷 DOCKER CLASS 16 – MULTI-APPLICATION CONTAINERIZATION

## 🔹 Class Overview

**Class:** 16  
**Technology:** Docker  
**Operating System:** Rocky Linux 9.3 (Dockerfile base image)  
**Working Directory:** `/root/assignment`

### 🔷 Topics Covered

1. Downloading and extracting website templates
2. Building an Nginx web server image
3. Deploying a static website using Nginx
4. Docker volumes and persistent website data
5. Accessing Nginx logs
6. Building an Apache Tomcat image
7. Understanding Java, Java Development Kit, and Tomcat
8. Extracting Tomcat using Dockerfile `ADD`
9. Building a Node.js application image
10. Running an Express.js application inside a container
11. Publishing Docker images to Docker Hub

---

# 🔷 SECTION 1: PROJECT DIRECTORY SETUP

### 🔹 STEP 1: Connect to the Linux Server

```bash
sudo -i
```

Check the current location:

```bash
pwd
```

Expected:

```text
/root
```

### 🔹 STEP 2: Review Existing Project Directories

```bash
ls -ltr
```

Create the Class 16 assignment directory:

```bash
mkdir -p /root/assignment
cd /root/assignment
```

Verify:

```bash
pwd
```

Expected:

```text
/root/assignment
```

### 🔹 STEP 3: Create the Nginx Project Directory

```bash
mkdir nginx
cd nginx
```

Verify:

```bash
pwd
```

Expected:

```text
/root/assignment/nginx
```

---

# 🔷 SECTION 2: DOWNLOAD AND PREPARE WEBSITE FILES

In this practical, we download two sample website templates and prepare the ZIP files for the Docker build.

### 🔹 STEP 4: Download the First Website Template

```bash
wget -O website.zip https://templatemo.com/download/templatemo_516_known
```

Verify:

```bash
ls -lh
```

The downloaded file is:

```text
website.zip
```

### 🔹 STEP 5: Extract the First Website Template

```bash
unzip website.zip
```

Check the extracted directory:

```bash
ls -ltr
```

The extracted folder should be:

```text
templatemo_516_known
```

Inspect its contents:

```bash
ls -ltr templatemo_516_known
```

Expected website files and directories include:

```text
fonts
js
images
css
index.html
```

### 🔹 STEP 6: Remove the Extracted Folder and Keep the ZIP

For this practical, we will use the ZIP file as a Docker build input.

```bash
rm -rf templatemo_516_known
```

Rename the ZIP file if necessary:

```bash
mv website.zip website.zip
```

The rename command above is unnecessary if the file is already named `website.zip`.

Verify:

```bash
ls -lh
```

### 🔹 STEP 7: Download the Second Website Template

```bash
wget -O mysite.zip https://templatemo.com/download/templatemo_620_compression
```

Verify:

```bash
ls -lh
```

You should now have:

```text
website.zip
mysite.zip
```

### 🔹 STEP 8: Extract the Second Website Template

```bash
unzip mysite.zip
```

Check the extracted folder:

```bash
ls -ltr
```

Expected:

```text
templatemo_620_compression
```

Inspect its contents:

```bash
ls -ltr templatemo_620_compression/
```

Expected:

```text
images
templatemo-620-compression-style.css
templatemo-620-compression-script.js
index.html
timer.html
```

### 🔹 STEP 9: Understand the ZIP Files

| ZIP file | Extracted directory |
|---|---|
| `website.zip` | `templatemo_516_known` |
| `mysite.zip` | `templatemo_620_compression` |

**Important:** The extracted folder name is preserved from the original archive. The ZIP filename does not determine the extracted folder name.

### 🔹 STEP 10: Remove Unneeded Extracted Folders

We will use `mysite.zip` for the Nginx Docker image.

Remove only the two extracted folders created for this exercise:

```bash
rm -rf templatemo_516_known templatemo_620_compression
```

Verify:

```bash
ls -lh
```

Expected:

```text
website.zip
mysite.zip
```

---

# 🔷 SECTION 3: DOCKER RESOURCE REVIEW

### 🔹 STEP 11: Check Existing Docker Images

```bash
docker images
```

### 🔹 STEP 12: Check Existing Containers

```bash
docker ps -a
```

### 🔹 STEP 13: Check Existing Volumes

```bash
docker volume ls
```

**Important safety note:** Do not delete all Docker images, containers, or volumes before starting this class. Previous class images may be needed in Class 16 or later classes.

Avoid using these broad deletion commands:

```bash
docker rmi -f $(docker images -aq)
docker rm -f $(docker ps -aq)
docker volume prune
docker system prune -a --volumes
```

Remove only resources that you have identified and no longer need.

### 🔹 STEP 14: Docker Hub Login

```bash
docker login
```

Enter your Docker Hub username and access token when prompted.

**Security:** Never put your Docker Hub password or access token in your Dockerfile, GitHub notes, screenshots, or terminal command history.

---

# 🔷 SECTION 4: DOCKERFILE FOR NGINX WEB SERVER

### 🔹 STEP 15: Create the Nginx Dockerfile

Check your directory:

```bash
pwd
```

Expected:

```text
/root/assignment/nginx
```

Create the Dockerfile:

```bash
vim Dockerfile
```

### 🔹 STEP 16: Nginx Dockerfile

```dockerfile
# Base Image
FROM rockylinux:9.3.20231119

# Image Metadata
LABEL maintainer="Fariha Nizam"
LABEL osinfo="Rocky Linux"
LABEL author="Fariha Nizam"
LABEL project="Docker Class 16 Nginx Website"
LABEL github="https://github.com/Fariha2024/docker--containerization-and-management"

# Install Nginx and Unzip
RUN dnf install nginx unzip -y && \
    dnf clean all

# Remove Default Nginx Website Files
RUN rm -rf /usr/share/nginx/html/*

# Copy Website ZIP File
COPY ./mysite.zip /tmp/

# Copy Dockerfile into the Image
COPY ./Dockerfile /root/

# Extract Website into Nginx Web Root
RUN unzip /tmp/mysite.zip -d /usr/share/nginx/html

# Move Website Files to the Nginx Root Directory
RUN cp -a /usr/share/nginx/html/templatemo_620_compression/. /usr/share/nginx/html/ && \
    rm -rf /usr/share/nginx/html/templatemo_620_compression

# Set Working Directory
WORKDIR /usr/share/nginx/html

# Create a Volume
VOLUME /usr/share/nginx/html

# Expose Web Server Ports
EXPOSE 80 443

# Start Nginx in the Foreground
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
```

Save and exit:

```text
:wq!
```

### 🔹 STEP 17: Understand the Nginx Dockerfile Instructions

**1. Base image**

```dockerfile
FROM rockylinux:9.3.20231119
```

Uses Rocky Linux as the base operating system.

**2. LABEL**

```dockerfile
LABEL maintainer="Fariha Nizam"
LABEL osinfo="Rocky Linux"
LABEL author="Fariha Nizam"
```

Adds descriptive metadata to the Docker image.

**3. Install Nginx and Unzip**

```dockerfile
RUN dnf install nginx unzip -y
```

- `nginx` installs the web server.
- `unzip` extracts the website ZIP file.
- `-y` automatically confirms package installation.

**4. Remove default website files**

```dockerfile
RUN rm -rf /usr/share/nginx/html/*
```

Removes the default Nginx website content.

**5. Copy the ZIP file**

```dockerfile
COPY ./mysite.zip /tmp/
```

Copies the complete website archive into the container image's `/tmp/` directory.

**6. Copy Dockerfile**

```dockerfile
COPY ./Dockerfile /root/
```

Copies the Dockerfile into `/root/` inside the image. This is included for the classroom demonstration; it is not required for Nginx to work.

**7. Extract website**

```dockerfile
RUN unzip /tmp/mysite.zip -d /usr/share/nginx/html
```

The `-d` option specifies the destination directory.

**8. Move website files**

```dockerfile
RUN cp -a /usr/share/nginx/html/templatemo_620_compression/. /usr/share/nginx/html/
```

Copies the contents of the extracted website directory directly into the Nginx document root.

The purpose is to make the website available at the root URL instead of requiring the visitor to enter the template folder name.

**9. WORKDIR**

```dockerfile
WORKDIR /usr/share/nginx/html
```

Sets the default working directory for subsequent Dockerfile instructions and the container's default working directory.

**10. VOLUME**

```dockerfile
VOLUME /usr/share/nginx/html
```

Declares the Nginx website directory as a Docker volume mount point.

Docker can create an anonymous volume for this directory when a container is created.

**11. EXPOSE**

```dockerfile
EXPOSE 80 443
```

Documents the container ports intended for HTTP and HTTPS.

`EXPOSE` does not publish ports automatically. Also, HTTPS on port 443 requires an appropriate Nginx TLS configuration and certificate.

**12. CMD**

```dockerfile
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
```

Starts Nginx in the foreground.

- `/usr/sbin/nginx` starts Nginx.
- `-g` passes a global Nginx directive.
- `daemon off;` keeps Nginx in the foreground.

Docker containers normally stay running while their main process remains active.

### 🔹 STEP 18: Verify Nginx Project Files

```bash
ls -lh
```

Expected:

```text
Dockerfile
mysite.zip
website.zip
```

The Nginx Dockerfile uses `mysite.zip`.

### 🔹 STEP 19: Build the Nginx Image

Recommended professional Docker Hub image tag:

```text
fariha2026/docker-class16-nginx-website:nginxweb-v1.0
```

Build:

```bash
docker build --no-cache -t fariha2026/docker-class16-nginx-website:nginxweb-v1.0 .
```

### 🔹 STEP 20: Verify the Nginx Image

```bash
docker images
```

Inspect:

```bash
docker image inspect fariha2026/docker-class16-nginx-website:nginxweb-v1.0
```

### 🔹 STEP 21: Run the Nginx Container

```bash
docker run --name c16-nginx -dit -P fariha2026/docker-class16-nginx-website:nginxweb-v1.0
```

Explanation:

| Option | Meaning |
|---|---|
| `docker run` | Creates and starts a container |
| `--name c16-nginx` | Assigns a container name |
| `-d` | Runs in detached mode |
| `-i` | Keeps STDIN open |
| `-t` | Allocates a terminal |
| `-P` | Publishes all exposed ports to available host ports |
| Image tag | Specifies the image to run |

### 🔹 STEP 22: Verify the Container

```bash
docker ps -a
```

Check the published ports:

```bash
docker port c16-nginx
```

Example output:

```text
80/tcp -> 0.0.0.0:32769
```

The host port may differ on your server.

### 🔹 STEP 23: Check the Website from the Linux Server

```bash
curl http://localhost:32769
```

Replace `32769` with the actual host port shown by:

```bash
docker port c16-nginx
```

If the website HTML is returned, Nginx is responding.

### 🔹 STEP 24: Access the Website from a Browser

Find your server's public IP address:

```bash
curl -s https://checkip.amazonaws.com
```

Open the website using:

```text
http://PUBLIC-IP:HOST-PORT
```

Replace the placeholders with your actual public IP and mapped port.

For AWS EC2, ensure the Security Group permits inbound TCP traffic to the mapped host port from your trusted IP address.

### 🔹 STEP 25: Enter the Nginx Container

```bash
docker exec -it c16-nginx bash
```

Check the working directory:

```bash
pwd
```

Expected:

```text
/usr/share/nginx/html
```

List website files:

```bash
ls -ltr
```

You should see the website's HTML, CSS, JavaScript, and image files.

Exit:

```bash
exit
```

### 🔹 STEP 26: Understand Docker Volume Creation

Check Docker volumes:

```bash
docker volume ls
```

Inspect the container's mounts:

```bash
docker inspect c16-nginx --format '{{json .Mounts}}'
```

The Nginx document root is declared as a volume in the Dockerfile.

Docker may create an anonymous volume for it when the container starts.

### 🔹 STEP 27: Locate Docker Volume Storage

```bash
ls -ltr /var/lib/docker/volumes/
```

Docker's default local volume data is generally stored under:

```text
/var/lib/docker/volumes/
```

A volume directory typically contains:

```text
_data
```

The `_data` directory holds the volume's actual data.

**Important:** Do not manually delete Docker's internal volume directories. Use Docker volume commands to manage volumes.

### 🔹 STEP 28: Check Nginx Logs

Enter the container:

```bash
docker exec -it c16-nginx bash
```

Go to the Nginx log directory:

```bash
cd /var/log/nginx/
```

List files:

```bash
ls -ltr
```

Common log files:

```text
access.log
error.log
```

Read access logs:

```bash
cat access.log
```

Follow the access log in real time:

```bash
tail -f access.log
```

Press `Ctrl + C` to stop following the log.

Exit:

```bash
exit
```

You can also read the container's standard output:

```bash
docker logs c16-nginx
```

---

# 🔷 SECTION 5: PUBLISH NGINX IMAGE TO DOCKER HUB

### 🔹 STEP 29: Verify the Image Tag

```bash
docker images
```

Look for:

```text
fariha2026/docker-class16-nginx-website
```

### 🔹 STEP 30: Push the Nginx Image

```bash
docker push fariha2026/docker-class16-nginx-website:nginxweb-v1.0
```

Verify the repository and tag in your Docker Hub account.

---

# 🔷 SECTION 6: APACHE TOMCAT CONTAINERIZATION

Tomcat is a Java-based application server used to run Java web applications.

### 🔹 STEP 31: Create the Tomcat Project Directory

Go to the assignment directory:

```bash
cd /root/assignment
```

Create the Tomcat directory:

```bash
mkdir -p tomcat
cd tomcat
```

Verify:

```bash
pwd
```

Expected:

```text
/root/assignment/tomcat
```

### 🔹 STEP 32: Download Apache Tomcat

Download the Tomcat 9 archive used in the classroom practical:

```bash
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.121/bin/apache-tomcat-9.0.121.tar.gz
```

Verify:

```bash
ls -lh
```

Expected archive:

```text
apache-tomcat-9.0.121.tar.gz
```

### 🔹 STEP 33: Understand Tomcat

- **Apache Tomcat:** A Java servlet container and web server.
- **Catalina:** Tomcat's servlet container and core component.
- **Java:** Required to run Tomcat.
- **Java Development Tools:** Useful when compiling Java applications.

### 🔹 STEP 34: Understand the TAR Archive

The downloaded file ends in:

```text
.tar.gz
```

This is a compressed TAR archive.

Extract it on the Linux server if you want to inspect its contents:

```bash
tar -xzf apache-tomcat-9.0.121.tar.gz
```

For the Dockerfile practical below, the archive will be added to the image using Dockerfile `ADD`, which automatically extracts a local TAR archive.

### 🔹 STEP 35: Create the Tomcat Dockerfile

```bash
vim Dockerfile
```

### 🔹 STEP 36: Tomcat Dockerfile

```dockerfile
# Base Image
FROM rockylinux:9.3.20231119

# Image Metadata
LABEL maintainer="Fariha Nizam"
LABEL osinfo="Rocky Linux"
LABEL author="Fariha Nizam"
LABEL project="Docker Class 16 Tomcat Application"
LABEL github="https://github.com/Fariha2024/docker--containerization-and-management"

# Copy Dockerfile into the Image
COPY ./Dockerfile /root/

# Add and Automatically Extract Tomcat Archive
ADD ./apache-tomcat-9.0.121.tar.gz /opt/

# Install Java and Supporting Packages
RUN dnf install java java-devel net-tools vim -y && \
    dnf clean all

# Create a Volume
VOLUME /opt/apache-tomcat-9.0.121

# Set Working Directory
WORKDIR /opt/apache-tomcat-9.0.121/bin/

# Expose Tomcat Web Application Port
EXPOSE 8080

# Start Tomcat in the Foreground
CMD ["/opt/apache-tomcat-9.0.121/bin/catalina.sh", "run"]
```

Save and exit:

```text
:wq!
```

### 🔹 STEP 37: Understand the Tomcat Dockerfile

**1. Base image**

```dockerfile
FROM rockylinux:9.3.20231119
```

Uses Rocky Linux as the base operating system.

**2. ADD instruction**

```dockerfile
ADD ./apache-tomcat-9.0.121.tar.gz /opt/
```

`ADD` copies the local archive into the image and automatically extracts a local TAR archive.

The extracted directory is:

```text
/opt/apache-tomcat-9.0.121
```

**3. Install Java**

```dockerfile
RUN dnf install java java-devel net-tools vim -y
```

| Package | Purpose |
|---|---|
| `java` | Runs Java applications |
| `java-devel` | Provides Java development tools, including the compiler |
| `net-tools` | Provides tools such as `ifconfig` and `netstat` |
| `vim` | Text editor |

Check Java versions inside the container:

```bash
java -version
javac -version
```

**4. VOLUME**

```dockerfile
VOLUME /opt/apache-tomcat-9.0.121
```

Declares the Tomcat installation directory as a volume mount point.

A new anonymous volume is normally created when the container starts. Docker initializes a new empty volume with the image's existing directory contents.

**5. WORKDIR**

```dockerfile
WORKDIR /opt/apache-tomcat-9.0.121/bin/
```

Sets the default working directory to Tomcat's `bin` directory.

This directory contains scripts such as:

```text
catalina.sh
startup.sh
shutdown.sh
```

**6. EXPOSE**

```dockerfile
EXPOSE 8080
```

Documents Tomcat's HTTP application port.

Tomcat's AJP and shutdown ports should not be published publicly unless a specific, secured configuration requires them.

**7. CMD**

```dockerfile
CMD ["/opt/apache-tomcat-9.0.121/bin/catalina.sh", "run"]
```

Runs Tomcat in the foreground.

The `catalina.sh run` command keeps Tomcat attached to the container's main process.

### 🔹 STEP 38: Verify Tomcat Project Files

```bash
ls -lh
```

Expected:

```text
Dockerfile
apache-tomcat-9.0.121.tar.gz
```

### 🔹 STEP 39: Build the Tomcat Image

Recommended professional Docker Hub image tag:

```text
fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0
```

Build:

```bash
docker build --no-cache -t fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0 .
```

### 🔹 STEP 40: Verify the Tomcat Image

```bash
docker images
```

Inspect:

```bash
docker image inspect fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0
```

### 🔹 STEP 41: Run the Tomcat Container

```bash
docker run --name c16-tomcat -dit -P fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0
```

### 🔹 STEP 42: Verify the Tomcat Container

```bash
docker ps -a
```

Check published ports:

```bash
docker port c16-tomcat
```

Example:

```text
8080/tcp -> 0.0.0.0:32771
```

The host port may differ.

### 🔹 STEP 43: Test Tomcat

From the Linux server:

```bash
curl http://localhost:32771
```

Replace `32771` with your actual mapped host port.

If Tomcat is running successfully, it should return the Tomcat response page.

For browser access:

```text
http://PUBLIC-IP:HOST-PORT
```

Allow the mapped TCP port in the AWS Security Group, preferably only from your trusted IP address.

### 🔹 STEP 44: Inspect the Tomcat Container

```bash
docker exec -it c16-tomcat bash
```

Check the working directory:

```bash
pwd
```

Expected:

```text
/opt/apache-tomcat-9.0.121/bin
```

List files:

```bash
ls -ltr
```

Exit:

```bash
exit
```

### 🔹 STEP 45: Check Tomcat Volume

```bash
docker volume ls
```

Inspect the container mounts:

```bash
docker inspect c16-tomcat --format '{{json .Mounts}}'
```

Find Docker's volume directory:

```bash
ls -ltr /var/lib/docker/volumes/
```

### 🔹 STEP 46: Check Tomcat Logs

Tomcat stores its logs inside:

```text
/opt/apache-tomcat-9.0.121/logs/
```

The directory can be inspected from the container:

```bash
docker exec -it c16-tomcat bash
```

```bash
cd /opt/apache-tomcat-9.0.121/logs/
ls -ltr
```

Follow the latest Catalina log:

```bash
tail -f catalina.out
```

If `catalina.out` is not present, inspect the dated Catalina log files:

```bash
ls -ltr catalina*
```

Press `Ctrl + C` to stop following the log.

Exit:

```bash
exit
```

### 🔹 STEP 47: Push the Tomcat Image to Docker Hub

```bash
docker push fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0
```

Verify the repository and tag in Docker Hub.

---

# 🔷 SECTION 7: NODE.JS AND EXPRESS.JS CONTAINERIZATION

Node.js allows JavaScript to run outside a web browser.

Express.js is a lightweight web application framework built on Node.js.

### 🔹 STEP 48: Create the Node.js Project Directory

```bash
cd /root/assignment
```

Create the directory:

```bash
mkdir -p nodejs
cd nodejs
```

Verify:

```bash
pwd
```

Expected:

```text
/root/assignment/nodejs
```

### 🔹 STEP 49: Create the Node.js Dockerfile

```bash
vim Dockerfile
```

### 🔹 STEP 50: Node.js Dockerfile

```dockerfile
# Base Image
FROM rockylinux:9.3.20231119

# Image Metadata
LABEL maintainer="Fariha Nizam"
LABEL osinfo="Rocky Linux"
LABEL author="Fariha Nizam"
LABEL project="Docker Class 16 Node.js Application"
LABEL github="https://github.com/Fariha2024/docker--containerization-and-management"

# Install Node.js and NPM
RUN dnf install nodejs npm -y && \
    dnf clean all

# Copy Dockerfile into the Image
COPY ./Dockerfile /root/

# Set Application Working Directory
WORKDIR /mytg

# Copy Node.js Application
COPY ./myapp1.js /mytg/

# Initialize NPM and Install Express
RUN npm init -y && npm install express

# Create a Volume
VOLUME /mytg

# Expose Application Port
EXPOSE 8080

# Start Node.js Application
CMD ["node", "myapp1.js"]
```

Save and exit:

```text
:wq!
```

### 🔹 STEP 51: Understand the Node.js Dockerfile

**1. Install Node.js and NPM**

```dockerfile
RUN dnf install nodejs npm -y
```

- Node.js runs JavaScript outside a web browser.
- NPM is the Node Package Manager.
- NPM manages project dependencies.

**2. WORKDIR**

```dockerfile
WORKDIR /mytg
```

Sets `/mytg` as the application directory.

Docker automatically creates the directory if it does not exist.

**3. COPY**

```dockerfile
COPY ./myapp1.js /mytg/
```

Copies the JavaScript application into the container.

**4. Install Express**

```dockerfile
RUN npm init -y && npm install express
```

- `npm init -y` creates a default `package.json`.
- `npm install express` installs the Express framework.

**5. VOLUME**

```dockerfile
VOLUME /mytg
```

Declares the application directory as a volume mount point.

**6. EXPOSE**

```dockerfile
EXPOSE 8080
```

Documents the application port used by Express.

**7. CMD**

```dockerfile
CMD ["node", "myapp1.js"]
```

Starts the Node.js application.

### 🔹 STEP 52: Create the Node.js Application

```bash
vim myapp1.js
```

Paste the following code:

```javascript
'use strict';

const express = require('express');

const host = '0.0.0.0';
const port = 8080;

const app = express();

app.get('/', (req, res) => {
    res.send('Hello World! I AM WITH Fariha Nizam');
});

app.listen(port, host, () => {
    console.log(`Express app listening at http://${host}:${port}`);
});
```

Save and exit:

```text
:wq!
```

### 🔹 STEP 53: Understand the Node.js Application

| Code | Explanation |
|---|---|
| `require('express')` | Loads Express |
| `express()` | Creates an Express application |
| `host = '0.0.0.0'` | Listens on all container network interfaces |
| `port = 8080` | Application listens on port 8080 |
| `app.get('/')` | Handles HTTP GET requests to the root path |
| `res.send()` | Sends the response to the client |
| `app.listen()` | Starts the web server |

**Important:** Using `0.0.0.0` allows the application to accept connections through the container's network interface. Using `localhost` inside the container would limit it to the container's loopback interface.

### 🔹 STEP 54: Verify Node.js Project Files

```bash
ls -lh
```

Expected:

```text
Dockerfile
myapp1.js
```

### 🔹 STEP 55: Build the Node.js Image

Recommended professional Docker Hub image tag:

```text
fariha2026/docker-class16-nodejs-app:nodejs-v1.0
```

Build:

```bash
docker build --no-cache -t fariha2026/docker-class16-nodejs-app:nodejs-v1.0 .
```

### 🔹 STEP 56: Verify the Node.js Image

```bash
docker images
```

Inspect:

```bash
docker image inspect fariha2026/docker-class16-nodejs-app:nodejs-v1.0
```

### 🔹 STEP 57: Run the Node.js Container

```bash
docker run --name c16-nodejs -dit -P fariha2026/docker-class16-nodejs-app:nodejs-v1.0
```

### 🔹 STEP 58: Verify the Node.js Container

```bash
docker ps -a
```

Check port mapping:

```bash
docker port c16-nodejs
```

Expected mapping example:

```text
8080/tcp -> 0.0.0.0:32776
```

The assigned host port may differ.

### 🔹 STEP 59: Test the Node.js Application

Use the actual mapped port:

```bash
curl http://localhost:32776
```

Expected response:

```text
Hello World! I AM WITH Fariha Nizam
```

You can also access the application through a browser:

```text
http://PUBLIC-IP:HOST-PORT
```

Ensure the AWS Security Group allows the mapped TCP port from your trusted IP address.

### 🔹 STEP 60: Inspect the Node.js Container

```bash
docker exec -it c16-nodejs bash
```

Check the working directory:

```bash
pwd
```

Expected:

```text
/mytg
```

List the application files:

```bash
ls -ltr
```

Expected files include:

```text
myapp1.js
package.json
package-lock.json
node_modules
```

Exit:

```bash
exit
```

### 🔹 STEP 61: Check Node.js Container Logs

```bash
docker logs c16-nodejs
```

Follow logs:

```bash
docker logs -f c16-nodejs
```

Press `Ctrl + C` to stop following the logs.

### 🔹 STEP 62: Push the Node.js Image to Docker Hub

```bash
docker push fariha2026/docker-class16-nodejs-app:nodejs-v1.0
```

Verify the image in Docker Hub.

---

# 🔷 SECTION 8: DOCKER PORTS – QUICK COMPARISON

| Application | Container port | Purpose |
|---|---:|---|
| Nginx | 80 | HTTP website |
| Nginx | 443 | HTTPS, when configured |
| Tomcat | 8080 | Java web applications |
| Node.js / Express | 8080 | Express web application |

### 🔹 STEP 63: Understand `-P` and `-p`

**Publish all exposed ports automatically:**

```bash
docker run -dit -P IMAGE
```

Docker assigns available host ports to the image's exposed ports.

**Publish a specific host port:**

```bash
docker run -dit -p 8081:80 IMAGE
```

In this example:

- `8081` is the host port.
- `80` is the container port.

The application can be reached through:

```text
http://PUBLIC-IP:8081
```

---

# 🔷 SECTION 9: DOCKER COMMAND QUICK REFERENCE

| Command | Purpose |
|---|---|
| `docker images` | List Docker images |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker build -t NAME:TAG .` | Build an image |
| `docker run -dit IMAGE` | Run a container in detached mode |
| `docker exec -it NAME bash` | Open a Bash shell |
| `docker logs NAME` | Display container logs |
| `docker port NAME` | Display published ports |
| `docker volume ls` | List Docker volumes |
| `docker inspect NAME` | Inspect an image or container |
| `docker push NAME:TAG` | Push an image to Docker Hub |
| `docker pull NAME:TAG` | Pull an image from Docker Hub |
| `docker stop NAME` | Stop a container |
| `docker rm NAME` | Remove a container |
| `docker rmi NAME:TAG` | Remove an image tag |

---

# 🔷 SECTION 10: IMPORTANT DOCKER CONCEPTS

### 🔹 1. Difference Between COPY and ADD

| COPY | ADD |
|---|---|
| Copies files and directories | Copies files and directories |
| Does not automatically extract local TAR archives | Automatically extracts local TAR archives |
| Preferred for ordinary file copying | Useful when archive extraction is required |

### 🔹 2. Difference Between WORKDIR and VOLUME

| WORKDIR | VOLUME |
|---|---|
| Sets the working directory | Declares a mount point |
| Affects subsequent Dockerfile instructions | Supports persistent data storage |
| Creates the directory if necessary | Docker can create an anonymous volume |

### 🔹 3. Difference Between EXPOSE and Port Publishing

- `EXPOSE` documents the container port.
- `-p` publishes a selected container port to a host port.
- `-P` publishes all exposed ports to automatically assigned host ports.

### 🔹 4. Why Run Nginx and Tomcat in the Foreground?

Docker monitors the main process inside the container.

For Nginx:

```dockerfile
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
```

For Tomcat:

```dockerfile
CMD ["/opt/apache-tomcat-9.0.121/bin/catalina.sh", "run"]
```

These commands keep the main application process in the foreground so the container remains running.

### 🔹 5. What Is a Docker Volume?

A Docker volume provides storage that exists separately from a container's writable layer.

Benefits:

- Data can persist beyond a container's lifecycle.
- Data can be shared between containers.
- Data can be backed up independently.
- Applications can use persistent storage.

### 🔹 6. What Is a Web Server?

A web server accepts HTTP requests and returns web content to clients.

Examples from this class:

- Nginx
- Apache Tomcat
- Node.js with Express

---

# 🔷 SECTION 11: SAFE CLEANUP AFTER CLASS 16

### 🔹 STEP 64: Check Containers

```bash
docker ps -a
```

### 🔹 STEP 65: Stop Only Class 16 Containers

```bash
docker stop c16-nginx c16-tomcat c16-nodejs
```

If a container was not created or is already stopped, Docker may report that it cannot be stopped.

### 🔹 STEP 66: Remove Only Class 16 Containers

```bash
docker rm c16-nginx c16-tomcat c16-nodejs
```

### 🔹 STEP 67: Preserve Class 16 Images

Check:

```bash
docker images
```

Keep the three images if they may be needed in the next class.

Do not run global image or volume cleanup commands.

### 🔹 STEP 68: Final Verification

```bash
docker ps -a
docker images
docker volume ls
docker system df
```

**Important:** The Nginx and Tomcat Dockerfiles declare volumes. Removing their containers does not automatically remove their associated volumes. Keep those volumes unless you have verified that their data is no longer needed.

---

# 🔷 SECTION 12: CLASS 16 LEARNING OUTCOMES

After completing Class 16, the following practical skills have been covered:

- Created Linux project directories for multiple applications.
- Downloaded and extracted website templates.
- Prepared ZIP files for Docker image builds.
- Created an Nginx Dockerfile.
- Installed Nginx inside a Rocky Linux-based image.
- Deployed a static website using Nginx.
- Inspected container ports and Docker volumes.
- Accessed Nginx access and error logs.
- Created an Apache Tomcat Dockerfile.
- Used Dockerfile `ADD` to extract a Tomcat TAR archive.
- Installed Java and Java development packages.
- Started Tomcat using `catalina.sh`.
- Created a Node.js application using Express.
- Built and ran a Node.js Docker image.
- Tested web applications using `curl`.
- Published Docker images to Docker Hub.
- Practised safe container cleanup while preserving images and volumes.

---

# 🔷 CLASS 16 – PROFESSIONAL IMAGE TAG SUMMARY

The following naming convention can be used for your Docker Hub repositories:

| Application | Docker Hub image tag |
|---|---|
| Nginx website | `fariha2026/docker-class16-nginx-website:nginxweb-v1.0` |
| Tomcat application | `fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0` |
| Node.js application | `fariha2026/docker-class16-nodejs-app:nodejs-v1.0` |

### 🔹 Docker Hub Push Commands

```bash
docker push fariha2026/docker-class16-nginx-website:nginxweb-v1.0

docker push fariha2026/docker-class16-tomcat-app:tomcat-app-v1.0

docker push fariha2026/docker-class16-nodejs-app:nodejs-v1.0
```

---

# 🔷 CLASS 16 – PROJECT STRUCTURE

Recommended structure for your GitHub repository:

```text
docker-containerization-and-management/
│
├── 02-class-14/
│   ├── notes/
│   └── practical-labs/
│
├── 03-class-15/
│   ├── notes/
│   └── practical-labs/
│
├── 04-class-16/
│   ├── notes/
│   │   └── class-16-docker-notes.md
│   │
│   └── practical-labs/
│       ├── nginx/
│       │   ├── Dockerfile
│       │   └── mysite.zip
│       │
│       ├── tomcat/
│       │   ├── Dockerfile
│       │   └── apache-tomcat-9.0.121.tar.gz
│       │
│       └── nodejs/
│           ├── Dockerfile
│           └── myapp1.js
│
└── README.md
```

**Repository:** https://github.com/Fariha2024/docker--containerization-and-management

**Author:** Fariha Nizam

**Technology:** Docker | Linux | Nginx | Apache Tomcat | Node.js | Express.js

---

# 🔷 FINAL CLASS 16 SUMMARY

Class 16 demonstrates how to containerize three different types of applications using Docker:

1. **Nginx:** Static website hosting.
2. **Tomcat:** Java application server.
3. **Node.js:** JavaScript application using Express.js.

The practical demonstrates Dockerfile instructions such as `FROM`, `LABEL`, `RUN`, `COPY`, `ADD`, `WORKDIR`, `VOLUME`, `EXPOSE`, and `CMD`, along with image building, container execution, port mapping, volume inspection, log monitoring, and Docker Hub publishing.

**End of Docker Class 16 Notes**