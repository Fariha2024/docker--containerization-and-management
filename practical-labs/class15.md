# 🔷 Docker – ENTRYPOINT, CMD, LABEL, ARG, ENV, Shell Scripting & MySQL

## 🔹 Overview

This practical lab covers important Dockerfile instructions and advanced container configuration techniques. It demonstrates the difference between ENTRYPOINT and CMD, adding image metadata using LABEL, passing build-time arguments using ARG, configuring environment variables using ENV, executing Linux shell scripts through ENTRYPOINT, and deploying a MySQL database container with an initialization SQL script.

The lab contains hands-on practical steps demonstrating Docker image creation, configuration, container execution, environment variable management, shell scripting and MySQL database operations.

### 🔹 Topics Covered

- 🔵 Difference Between ENTRYPOINT and CMD
- 🔵 Overriding ENTRYPOINT Using Command-Line Options
- 🔵 Docker LABEL Instruction
- 🔵 Docker ARG Instruction
- 🔵 Docker ENV Instruction
- 🔵 Difference Between ARG and ENV
- 🔵 Passing ARG Values into ENV
- 🔵 ENTRYPOINT with Linux Shell Scripting
- 🔵 Bash Script Execution and Special Parameters
- 🔵 Docker COPY Instruction
- 🔵 MySQL 8.0 Docker Image
- 🔵 MySQL Database Initialization Using SQL Scripts
- 🔵 MySQL User and Root Authentication
- 🔵 `.my.cnf` Client Configuration File

---

# 🔷 SECTION 1: Difference Between ENTRYPOINT and CMD

### 🔹 STEP 1: Connect to Linux Server

```bash
sudo -i
```

### 🔹 STEP 2: Check Existing Project Directories

```bash
ls -ltr
```

### 🔹 STEP 3: Create Project Directory

```bash
mkdir myproject3
```

### 🔹 STEP 4: Enter Project Directory

```bash
cd myproject3
```

### 🔹 STEP 5: Create Dockerfile

```bash
vim Dockerfile
```

Add the following content:

```dockerfile
FROM ubuntu

ENTRYPOINT ["echo","hello"]
ENTRYPOINT ["echo","bye"]

CMD ["asia"]
CMD ["india"]
```

Save the file:

```text
:wq!
```

### 🔹 STEP 6: Build Docker Image

```bash
docker build -t myimg:v1.0 --no-cache .
```

### 🔹 STEP 7: Check Docker Images

```bash
docker images
```

The image created is:

```text
myimg:v1.0
```

### 🔹 STEP 8: Create Container C1

```bash
docker run --name c1 -dit myimg:v1.0
```

### 🔹 STEP 9: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 10: Check Container Logs

```bash
docker logs c1
```

Expected output:

```text
bye india
```

### 🔹 STEP 11: Understand Multiple ENTRYPOINT and CMD Instructions

The Dockerfile contains:

```dockerfile
ENTRYPOINT ["echo","hello"]
ENTRYPOINT ["echo","bye"]

CMD ["asia"]
CMD ["india"]
```

For this example, the last ENTRYPOINT and the last CMD instruction take effect.

Effective configuration:

```dockerfile
ENTRYPOINT ["echo","bye"]
CMD ["india"]
```

### 🔹 STEP 12: Create Container C2 with Command-Line Argument

```bash
docker run --name c2 -dit myimg:v1.0 date
```

### 🔹 STEP 13: Check C2 Logs

```bash
docker logs c2
```

Expected output:

```text
bye date
```

### 🔹 STEP 14: Create Container C3 with Multiple Arguments

```bash
docker run --name c3 -dit myimg:v1.0 USA California
```

### 🔹 STEP 15: Check C3 Logs

```bash
docker logs c3
```

Expected output:

```text
bye USA California
```

### 🔹 STEP 16: Create Container C4

```bash
docker run --name c4 -dit myimg:v1.0 hahahahahaah
```

### 🔹 STEP 17: Check C4 Logs

```bash
docker logs c4
```

Expected output:

```text
bye hahahahahaah
```

### 🔹 STEP 18: Create Container C5

```bash
docker run --name c5 -dit myimg:v1.0 parag dongre
```

### 🔹 STEP 19: Check C5 Logs

```bash
docker logs c5
```

Expected output:

```text
bye parag dongre
```

### 🔹 STEP 20: Override ENTRYPOINT Using --entrypoint

The `--entrypoint` option allows us to replace the ENTRYPOINT instruction when launching a container.

```bash
docker run --rm --entrypoint ls myimg:v1.0 /etc/
```

This executes the `ls` command instead of the original ENTRYPOINT.

### 🔹 STEP 21: Override ENTRYPOINT with cat

```bash
docker run --name c51 --entrypoint cat myimg:v1.0 /etc/os-release
```

This displays the operating system information from the container image.

### 🔹 STEP 22: Override ENTRYPOINT with echo

```bash
docker run --name c52 --entrypoint echo myimg:v1.0
```

### 🔹 STEP 23: Check Containers

```bash
docker ps -a
```

### 🔹 STEP 24: Understand ENTRYPOINT vs CMD

| ENTRYPOINT | CMD |
|---|---|
| Defines the main executable or command | Provides default command or arguments |
| Normally remains active when runtime arguments are supplied | Runtime command arguments replace the default CMD |
| Can be explicitly replaced using `--entrypoint` | Can be replaced by command-line arguments |
| Commonly used to define the container's main application | Commonly used to provide default parameters |

**Important behaviour:**

When using exec-form ENTRYPOINT:

```dockerfile
ENTRYPOINT ["echo","bye"]
CMD ["india"]
```

Running:

```bash
docker run myimg:v1.0
```

Output:

```text
bye india
```

Running:

```bash
docker run myimg:v1.0 date
```

Output:

```text
bye date
```

The runtime argument replaces the default CMD arguments. It does not replace the exec-form ENTRYPOINT.

To replace ENTRYPOINT explicitly:

```bash
docker run --entrypoint cat myimg:v1.0 /etc/os-release
```

---

# 🔷 SECTION 2: Docker LABEL Instruction

### 🔹 STEP 25: Check Existing Docker Images

```bash
docker images
```

### 🔹 STEP 26: Pull Nginx Image

```bash
docker pull nginx
```

### 🔹 STEP 27: Check Docker Images Again

```bash
docker images
```

### 🔹 STEP 28: Create or Edit Dockerfile

```bash
vim Dockerfile
```

Add the following content:

```dockerfile
FROM ubuntu

LABEL maintainer="Fariha Nizam"
LABEL version="1.0"
LABEL description="Docker LABEL instruction demonstration"
LABEL gitrepo="https://github.com/Fariha2024/docker--containerization-and-management"

ENTRYPOINT ["echo","bye"]

CMD ["india"]
```

Save the file:

```text
:wq!
```

### 🔹 STEP 29: Remove Previous Containers

```bash
docker rm -f $(docker ps -aq)
```

### 🔹 STEP 30: Check Docker Volumes

```bash
docker volume ls
```

### 🔹 STEP 31: Build Image with LABEL

```bash
docker build -t mytest:v1 --no-cache .
```

### 🔹 STEP 32: Check Docker Images

```bash
docker images
```

The image created is:

```text
mytest:v1
```

### 🔹 STEP 33: Inspect Docker Image

```bash
docker inspect mytest:v1 | less
```

Press `q` to exit the output viewer.

### 🔹 STEP 34: Display Image Labels

```bash
docker image inspect mytest:v1 --format '{{json .Config.Labels}}'
```

### 🔹 STEP 35: Understand LABEL Instruction

The LABEL instruction adds metadata to a Docker image in the form of key-value pairs.

**Syntax:**

```dockerfile
LABEL key="value"
```

**Examples:**

```dockerfile
LABEL maintainer="Fariha Nizam"
LABEL version="1.0"
LABEL description="Docker application"
LABEL gitrepo="https://github.com/Fariha2024/docker--containerization-and-management"
```

**Uses of LABEL:**

- Maintainer information
- Image version information
- Image description
- Git repository location
- Build information
- Image identification and organization
- CI/CD workflows

---

# 🔷 SECTION 3: Docker ARG Instruction

### 🔹 STEP 36: Understand ARG Instruction

ARG stands for **Build Argument**.

It defines a variable that can be used during Docker image building.

**Syntax:**

```dockerfile
ARG variable_name=value
```

### 🔹 STEP 37: Create Dockerfile with ARG

```bash
vim Dockerfile
```

Add:

```dockerfile
FROM ubuntu

LABEL maintainer="Fariha Nizam"
LABEL version="1.0"

ARG myappversion="2.0"

RUN echo "Building application version $myappversion"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 38: Build Image Using ARG

```bash
docker build -t docker-class15-build-arguments:v1.0 --no-cache .

docker images
docker image inspect docker-class15-build-arguments:v1.0

```

During the build, Docker executes the RUN instruction and displays the application version.

### 🔹 STEP 39: Create Dockerfile with ARG Variable x

```bash
vim Dockerfile
```

Add:

```dockerfile
FROM ubuntu

ARG x=10

RUN echo "The value of x is $x"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 40: Build Image

```bash
docker build -t myargtest:v1.1 --no-cache .
```

Expected build output includes:

```text
The value of x is 10
```

### 🔹 STEP 41: Override ARG Value During Build

```bash
docker build --build-arg x=140 -t docker-class15-build-arg:v1.2 --no-cache .

docker images
docker image inspect docker-class15-build-arg:v1.2
```

Expected build output includes:

```text
The value of x is 140
```

### 🔹 STEP 42: Create Dockerfile with Multiple ARG Variables

```bash
vim Dockerfile
```

Add:

```dockerfile
FROM ubuntu

ARG x=10
ARG uname=Ekam
ARG gitrepo=https://github.com/example/project

RUN echo "The value of x is $x"
RUN echo "Username is $uname"
RUN echo "Git repository is $gitrepo"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 43: Build Image with Default ARG Values

```bash
docker build -t docker-class15-build-arguments:v1.2 --no-cache .
```

### 🔹 STEP 44: Override Multiple ARG Values

```bash
docker build \
--build-arg x=180 \
--build-arg uname=Harman \
--build-arg gitrepo=https://example.com/project \
-t docker-class15-build-arguments:v1.2 --no-cache .
```

The build output displays the supplied values.

### 🔹 STEP 45: Run Container and Check ARG Variables

```bash
docker run --name c61 -it docker-class15-build-arguments:v1.2 bash
```

Inside the container:

```bash
echo $x
echo $uname
echo $gitrepo
```

These variables are not automatically available as runtime environment variables because ARG is a build-time instruction.

Exit the container:

```bash
exit
```

### 🔹 STEP 46: Understand ARG Instruction

**Important points:**

- ARG defines build-time variables.
- ARG values can be supplied using `--build-arg`.
- ARG values can be changed when building an image.
- ARG values are not automatically available inside the running container.
- ARG should not be used to store passwords, tokens or other secrets.

**Example:**

```dockerfile
ARG x=10
ARG uname=Ekam
```

Build command:

```bash
docker build --build-arg x=180 -t docker-class15-build-arguments:v1.2 .
```

---

# 🔷 SECTION 4: Docker ENV Instruction

### 🔹 STEP 47: Understand ENV Instruction

ENV stands for **Environment Variable**.

It defines environment variables that are available inside the container when it runs.

**Syntax:**

```dockerfile
ENV variable_name=value
```

### 🔹 STEP 48: Create Dockerfile with ARG and ENV

```bash
vim Dockerfile
```

Add:

```dockerfile
FROM ubuntu

ARG x=10
ARG uname=Ekam
ARG gitrepo=https://example.com/project

ENV y=20
ENV mygrpname=techedu
ENV myftp=https://example.com/software

RUN echo "The value of x is $x"
RUN echo "Username is $uname"
RUN echo "Git repository is $gitrepo"

RUN echo "The value of y is $y"
RUN echo "Group name is $mygrpname"
RUN echo "FTP location is $myftp"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 49: Build Image with ENV Variables

```bash
docker build -t docker-class15-environment-variables:v1.0  --no-cache .
```

### 🔹 STEP 50: Check Docker Images

```bash
docker images
```

### 🔹 STEP 51: Create Container C31

```bash
docker run --name c31 -dit docker-class15-environment-variables:v1.0
```

### 🔹 STEP 52: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 53: Enter Container C31

```bash
docker exec -it c31 bash
```

### 🔹 STEP 54: Check Environment Variable y

```bash
echo $y
```

Expected output:

```text
20
```

### 🔹 STEP 55: Check Group Name Variable

```bash
echo $mygrpname
```

Expected output:

```text
techedu
```

### 🔹 STEP 56: Check FTP Variable

```bash
echo $myftp
```

Expected output:

```text
https://example.com/software
```

Exit the container:

```bash
exit
```

### 🔹 STEP 57: Pass ARG Value into ENV

Edit the Dockerfile:

```bash
vim Dockerfile
```

Add or update the following:

```dockerfile
FROM ubuntu

ARG x=10
ARG uname=Ekam
ARG gitrepo=https://example.com/project

ENV y=20
ENV mygrpname=techedu
ENV myftp=https://example.com/software
ENV z=$x

RUN echo "The value of x is $x"
RUN echo "Username is $uname"
RUN echo "Git repository is $gitrepo"

RUN echo "The value of y is $y"
RUN echo "Group name is $mygrpname"
RUN echo "FTP location is $myftp"
RUN echo "The value of z is $z"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 58: Build Updated ENV Image

```bash
docker build -t docker-class15-environment-variables:v1.1 --no-cache .
```

The ENV variable `z` receives the build-time value of ARG variable `x`.

Expected build output includes:

```text
The value of z is 10
```

### 🔹 STEP 59: Create Container with Runtime ENV Overrides

```bash
docker run --name c100 -dit \
-e y=900 \
-e mygrpname=india \
myenvtest:v1.2
```

### 🔹 STEP 60: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 61: Enter Container C100

```bash
docker exec -it c100 bash
```

### 🔹 STEP 62: Check Overridden ENV Variable y

```bash
echo $y
```

Expected output:

```text
900
```

### 🔹 STEP 63: Check Overridden Group Name

```bash
echo $mygrpname
```

Expected output:

```text
india
```

### 🔹 STEP 64: Check ENV Variable z

```bash
echo $z
```

Expected output:

```text
10
```

### 🔹 STEP 65: Check FTP Variable

```bash
echo $myftp
```

Expected output:

```text
https://example.com/software
```

Exit the container:

```bash
exit
```

### 🔹 STEP 66: Difference Between ARG and ENV

| ARG | ENV |
|---|---|
| Build-time variable | Environment variable |
| Used during image building | Available inside the running container |
| Can be overridden using `--build-arg` | Can be overridden using `-e` during `docker run` |
| Not automatically persisted as a runtime environment variable | Stored as an image environment default |
| Useful for build configuration | Useful for application configuration |

**ARG example:**

```dockerfile
ARG x=10
```

Override during build:

```bash
docker build --build-arg x=140 -t docker-class15-environment-variables:v1.0
```

**ENV example:**

```dockerfile
ENV y=20
```

Override during container creation:

```bash
docker run --name c100 -e y=900 docker-class15-environment-variables:v1.0
```

**Passing ARG into ENV:**

```dockerfile
ARG x=10
ENV z=$x
```

In this example, `z` receives the value of `x` during image building and is available as an environment variable when the container runs.

---

# 🔷 SECTION 5: ENTRYPOINT in Linux Shell Scripting

### 🔹 STEP 67: Return to Root Directory

```bash
cd
```

### 🔹 STEP 68: Check Current Directory

```bash
pwd
```

### 🔹 STEP 69: Create Project Directory

```bash
mkdir myproject4
```

### 🔹 STEP 70: Enter Project Directory

```bash
cd myproject4
```

### 🔹 STEP 71: Create Dockerfile

```bash
vim Dockerfile
```

Add:

```dockerfile
FROM ubuntu:22.04

ENV a="tg1"
ENV b="tg2"
ENV c="tg3"

COPY ./entrypoint.sh /entrypoint.sh

RUN chmod u+x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

Save the file:

```text
:wq!
```

### 🔹 STEP 72: Create Shell Script

```bash
vim entrypoint.sh
```

Add:

```bash
#!/bin/bash

set -eux

echo "First environment variable: $a"
echo "Second environment variable: $b"
echo "Third environment variable: $c"

echo "Creating three directories"

mkdir -p "/$a"
mkdir -p "/$b"
mkdir -p "/$c"

ls -ltr /

echo "Received arguments:"
echo "$@"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 73: Check Project Files

```bash
ls -ltr
```

Expected files:

```text
Dockerfile
entrypoint.sh
```

### 🔹 STEP 74: Display Shell Script

```bash
cat entrypoint.sh
```

### 🔹 STEP 75: Build Docker Image

```bash
docker build -t docker-class15-entrypoint-shell:v1.0 --no-cache .
```

### 🔹 STEP 76: Check Docker Images

```bash
docker images
```

### 🔹 STEP 77: Create Container C11

```bash
docker run --name c11 -dit docker-class15-entrypoint-shell:v1.0
```

### 🔹 STEP 78: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 79: Check Container Logs

```bash
docker logs c11
```

The output demonstrates the environment variable values and the shell script execution.

The script creates these directories:

```text
/tg1
/tg2
/tg3
```

### 🔹 STEP 80: Understand Docker COPY Instruction

The Dockerfile contains:

```dockerfile
COPY ./entrypoint.sh /entrypoint.sh
```

This copies `entrypoint.sh` from the build context into the container image.

**Important:** The source file must be available inside the Docker build context.

### 🔹 STEP 81: Understand Build Context

The Docker build command:

```bash
docker build -t docker-class15-entrypoint-shell:v1.1 --no-cache .
```

The dot (`.`) represents the current directory as the build context.

Docker can copy files from this context using the COPY instruction.

### 🔹 STEP 82: Update Shell Script with exec "$@"

Edit the script:

```bash
vim entrypoint.sh
```

Use the following updated script:

```bash
#!/bin/bash

set -eux

echo "First environment variable: $a"
echo "Second environment variable: $b"
echo "Third environment variable: $c"

echo "Creating three directories"

mkdir -p "/$a"
mkdir -p "/$b"
mkdir -p "/$c"

ls -ltr /

exec "$@"
```

Save the file:

```text
:wq!
```

### 🔹 STEP 83: Build Updated Image

```bash
docker build -t docker-class15-entrypoint-shell:v1.2 --no-cache .
```

### 🔹 STEP 84: Create Container with a Runtime Command

```bash
docker run --name c51 -it docker-class15-entrypoint-shell:v1.2 date
```

The entrypoint script runs first and then executes the `date` command through:

```bash
exec "$@"
```

### 🔹 STEP 85: Check Container Logs

```bash
docker logs c51
```

The logs show the environment variable values, directory creation and command execution.

### 🔹 STEP 86: Pass a Custom Command to ENTRYPOINT Script

```bash
docker run --name c52 -it docker-class15-entrypoint-shell:v1.2 sh -c 'echo "Custom command executed"'
```

The shell script receives the supplied command-line arguments and executes them.

### 🔹 STEP 87: Understand Special Shell Parameters

| Parameter | Description |
|---|---|
| `$#` | Total number of arguments passed to a script |
| `$*` | All arguments combined into a single string |
| `$@` | All arguments passed to a script |
| `$?` | Exit status of the most recently executed command |

**Important difference:**

- `"$@"` preserves arguments as separate values.
- `"$*"` combines arguments into one string when quoted.
- `exec "$@"` is generally preferred when forwarding arguments to another command.

### 🔹 STEP 88: Understand set -e, set -u and set -x

The shell script uses:

```bash
set -eux
```

| Option | Description |
|---|---|
| `-e` | Exits when a command fails |
| `-u` | Treats the use of an unset variable as an error |
| `-x` | Displays commands before executing them |

These options are useful for writing and debugging shell scripts.

### 🔹 STEP 89: Understand exec Keyword

The `exec` command replaces the current shell process with the command being executed.

Example:

```bash
exec "$@"
```

This forwards the arguments received by the entrypoint script to the requested command.

It is useful in Docker containers because the application command can run as the main container process.

---

# 🔷 SECTION 6: MySQL Docker Image and Database Initialization

### 🔹 STEP 90: Return to Root Directory

```bash
cd
```

### 🔹 STEP 91: Create MySQL Project Directory

```bash
mkdir myproject5
```

### 🔹 STEP 92: Enter MySQL Project Directory

```bash
cd myproject5
```

### 🔹 STEP 93: Create Dockerfile

```bash
vim Dockerfile
```

Add the following source-class demonstration:

```dockerfile
FROM mysql:8.0

ENV MYSQL_ROOT_PASSWORD=redhat
ENV MYSQL_DATABASE=tginida
ENV MYSQL_USER=ekam
ENV MYSQL_PASSWORD=india@123

COPY ./mydb.sql /docker-entrypoint-initdb.d/

EXPOSE 3306 33060

CMD ["mysqld"]
```

Save the file:

```text
:wq!
```

**Security note:** The credentials above are classroom demonstration values from the practical source. Do not use real passwords or bake production credentials into Docker images. A safer approach is to provide credentials when creating the container.

### 🔹 STEP 94: Create SQL Initialization File

```bash
vim mydb.sql
```

Add:

```sql
CREATE DATABASE mycollege;

CREATE TABLE mycollege.emp (
    id INT(10),
    empname VARCHAR(50)
);

INSERT INTO mycollege.emp
SET id=101, empname='Ajit Singh';

INSERT INTO mycollege.emp
SET id=102, empname='Fatheh Singh';

INSERT INTO mycollege.emp
SET id=103, empname='Jujhar Singh';

INSERT INTO mycollege.emp
SET id=104, empname='Joravar Singh';
```

Save the file:

```text
:wq!
```

### 🔹 STEP 95: Check Project Files

```bash
ls -ltr
```

Expected files:

```text
Dockerfile
mydb.sql
```

### 🔹 STEP 96: Build MySQL Docker Image

```bash
docker build -t docker-class15-mysql-database:v1.0  --no-cache .
```

### 🔹 STEP 97: Check Docker Images

```bash
docker images
```

The image created is:

```text
mydb:v1.0
```

### 🔹 STEP 98: Create MySQL Container C52

```bash
docker run --name c52 -dit -P docker-class15-mysql-database:v1.0
```

### 🔹 STEP 99: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 100: Check MySQL Container Logs

```bash
docker logs c52
```

### 🔹 STEP 101: Enter MySQL Container

```bash
docker exec -it c52 bash
```

### 🔹 STEP 102: Connect to MySQL Using Root User

```bash
mysql -u root -p
```

Enter the classroom password when prompted:

```text
redhat
```

### 🔹 STEP 103: Display Databases

Inside the MySQL prompt:

```sql
SHOW DATABASES;
```

### 🔹 STEP 104: Select Mycollege Database

```sql
USE mycollege;
```

### 🔹 STEP 105: Display Tables

```sql
SHOW TABLES;
```

### 🔹 STEP 106: Display Employee Records

```sql
SELECT * FROM emp;
```

The table displays the four records inserted through `mydb.sql`.

### 🔹 STEP 107: Exit MySQL

```sql
exit;
```

### 🔹 STEP 108: Connect Using MySQL User

From the container shell:

```bash
mysql -u ekam -p
```

Enter the classroom password when prompted:

```text
india@123
```

Exit MySQL:

```sql
exit;
```

### 🔹 STEP 109: Understand MySQL Initialization Directory

The Dockerfile contains:

```dockerfile
COPY ./mydb.sql /docker-entrypoint-initdb.d/
```

The MySQL image uses this directory for initialization scripts.

SQL files placed in this directory are processed during the initial database setup.

### 🔹 STEP 110: Understand MySQL Port Configuration

The Dockerfile contains:

```dockerfile
EXPOSE 3306 33060
```

- `3306` is the standard MySQL client connection port.
- `33060` is used by MySQL X Protocol.

`EXPOSE` documents the container ports. It does not by itself publish them to the host.

The practical uses:

```bash
docker run --name c52 -dit -P docker-class15-mysql-database:v1.0
```

The `-P` option publishes exposed ports to dynamically assigned host ports.

### 🔹 STEP 111: Understand .my.cnf File

The `.my.cnf` file is a hidden MySQL client configuration file.

It can store client connection settings.

**Example structure:**

```ini
[client]
user=root
password=YOUR_LAB_PASSWORD
```

In the classroom demonstration, this file was copied to:

```dockerfile
COPY ./.my.cnf /root/
```

The `/root/` directory is the home directory of the root user inside the container.

### 🔹 STEP 112: Create .my.cnf File

```bash
vim .my.cnf
```

Add the configuration:

```ini
[client]
user=root
password=YOUR_LAB_PASSWORD
```

Save the file:

```text
:wq!
```

### 🔹 STEP 113: Update Dockerfile for .my.cnf Demonstration

The classroom example adds:

```dockerfile
COPY ./.my.cnf /root/
```

The Dockerfile section becomes:

```dockerfile
FROM mysql:8.0

ENV MYSQL_ROOT_PASSWORD=redhat
ENV MYSQL_DATABASE=tginida
ENV MYSQL_USER=ekam
ENV MYSQL_PASSWORD=india@123

COPY ./mydb.sql /docker-entrypoint-initdb.d/
COPY ./.my.cnf /root/

EXPOSE 3306 33060

CMD ["mysqld"]
```

### 🔹 STEP 114: Build Updated MySQL Image

```bash
docker build -t docker-class15-mysql-database:v1.1  --no-cache .
```

### 🔹 STEP 115: Check Docker Images

```bash
docker images
```

The new image is:

```text
mynewdb:v1.1
```

### 🔹 STEP 116: Create Container C101

```bash
docker run --name c101 -dit -P docker-class15-mysql-database:v1.1
```

### 🔹 STEP 117: Check Container Status

```bash
docker ps -a
```

### 🔹 STEP 118: Enter Container C101

```bash
docker exec -it c101 bash
```

### 🔹 STEP 119: Check Current Directory

```bash
pwd
```

### 🔹 STEP 120: Enter Root Home Directory

```bash
cd /root/
```

### 🔹 STEP 121: Display Hidden Files

```bash
ls -la
```

The `.my.cnf` file should be visible.

### 🔹 STEP 122: Connect to MySQL Using Client Configuration

```bash
mysql
```

When the client reads a valid `.my.cnf` configuration, it can use the stored client settings without prompting for the password.

### 🔹 STEP 123: Verify MySQL Database

Inside MySQL:

```sql
SHOW DATABASES;

USE mycollege;

SHOW TABLES;

SELECT * FROM emp;
```

Exit MySQL:

```sql
exit;
```

**Security note:** Copying `.my.cnf` with a password into an image stores that credential in the image. This is for classroom demonstration only. Do not use this method for production credentials. Protect any client configuration file with restrictive permissions and use an appropriate secrets-management approach.

---

# 🔷 Important Docker Commands – Quick Reference

| Command | Description |
|---|---|
| `docker build -t name:tag .` | Build Docker image |
| `docker images` | List Docker images |
| `docker run` | Create and start a container |
| `docker ps -a` | List all containers |
| `docker logs` | Display container logs |
| `docker exec -it` | Access a running container |
| `docker inspect` | Display detailed image or container information |
| `docker image inspect` | Inspect image metadata |
| `docker rm -f` | Forcefully remove a container |
| `docker rmi` | Remove a Docker image |
| `docker pull` | Download an image |
| `docker build --build-arg` | Pass a build-time ARG value |
| `docker run -e` | Set a runtime environment variable |
| `docker run --entrypoint` | Override the image ENTRYPOINT |
| `COPY` | Copy files into the image |
| `LABEL` | Add image metadata |
| `ARG` | Define build-time variables |
| `ENV` | Define environment variables |
| `EXPOSE` | Document container ports |
| `exec "$@"` | Execute forwarded script arguments |
| `mysql -u root -p` | Connect to MySQL as root |
| `SHOW DATABASES;` | Display MySQL databases |
| `SHOW TABLES;` | Display database tables |
| `SELECT * FROM emp;` | Display employee records |

---

# 🔷 Important Notes and Lab Considerations

🔹 **ENTRYPOINT and CMD:** When multiple ENTRYPOINT or CMD instructions of the same type appear in a Dockerfile, the last one takes effect.

🔹 **Runtime Arguments:** With exec-form ENTRYPOINT, arguments supplied after the image name replace the default CMD arguments and are passed to ENTRYPOINT.

🔹 **ENTRYPOINT Override:** Use `--entrypoint` to explicitly replace the image ENTRYPOINT.

🔹 **LABEL:** LABEL stores image metadata as key-value pairs. It is useful for identifying image versions, maintainers and source repositories.

🔹 **ARG:** ARG variables are intended for image-build configuration. They are not automatically available as environment variables inside the running container.

🔹 **ENV:** ENV variables are available in the running container. Runtime values supplied with `-e` override the image's default ENV values for that container.

🔹 **ARG and ENV Together:** An ARG value can be assigned to an ENV variable, for example `ENV z=$x`.

🔹 **Docker COPY:** Files referenced by COPY must be inside the Docker build context. The `.` in `docker build` represents the current directory.

🔹 **Shell Script:** `set -e`, `set -u` and `set -x` help control script execution and debugging.

🔹 **exec "$@":** This is a common way to forward container startup arguments from an entrypoint script to the main application command.

🔹 **MySQL Initialization:** SQL scripts copied into `/docker-entrypoint-initdb.d/` are processed during initial database initialization. They are not automatically rerun every time an existing database container starts.

🔹 **MySQL Credentials:** Passwords placed in Dockerfile ENV instructions or `.my.cnf` files copied into an image can be exposed through image configuration or image contents. Use runtime secrets or another secure method for real deployments.

🔹 **MySQL Volume:** Persistent MySQL storage requires a Docker volume mounted at `/var/lib/mysql`. Creating a volume without mounting it does not persist the container's database data.

🔹 **MySQL Port:** `EXPOSE` documents container ports. Use `-p` or `-P` when publishing ports to the host.

🔹 **Container Names:** Container names must be unique on the same Docker host. Remove an existing container or use another name before creating a container with the same name.

🔹 **Cleanup Warning:** Commands such as `docker rm -f $(docker ps -aq)` and `docker rmi -f $(docker images -aq)` can remove many resources. Use them carefully, especially on shared or production servers.

---

# 🔷 Learning Outcomes

After completing this practical lab, the learner will have hands-on experience with:

- 🔵 Understanding the difference between ENTRYPOINT and CMD.
- 🔵 Overriding Docker ENTRYPOINT using command-line options.
- 🔵 Creating Docker images with LABEL metadata.
- 🔵 Using ARG variables during image building.
- 🔵 Passing build arguments using `--build-arg`.
- 🔵 Creating environment variables using ENV.
- 🔵 Overriding environment variables using `docker run -e`.
- 🔵 Understanding the difference between ARG and ENV.
- 🔵 Passing ARG values into ENV variables.
- 🔵 Integrating Linux shell scripts with Docker ENTRYPOINT.
- 🔵 Understanding shell scripting options and special parameters.
- 🔵 Using the Docker COPY instruction.
- 🔵 Building MySQL 8.0 Docker images.
- 🔵 Initializing databases using SQL scripts.
- 🔵 Connecting to MySQL using root and normal users.
- 🔵 Understanding MySQL client configuration using `.my.cnf`.
- 🔵 Understanding container port exposure and persistent storage considerations.

---

**Practical Lab:** Dockerfile Instructions and Container Configuration  
**Platform:** Linux Server / Ubuntu / Docker Host  
**Technology:** Docker, Ubuntu, Bash Shell Scripting, MySQL 8.0  
**Main Dockerfile Instructions:** ENTRYPOINT, CMD, LABEL, ARG, ENV, COPY, EXPOSE