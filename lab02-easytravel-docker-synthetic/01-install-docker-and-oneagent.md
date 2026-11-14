# Install Docker, easyTravel, Grafana & OneAgent (Container)

## Checklist

1. [Default VM setup](#1-default-vm-setup)
   - [x] [1.1 Create two VMs](#11-create-two-vms)
   - [x] [1.2 Log in and switch to root](#12-log-in-and-switch-to-root)
   - [x] [1.3 Update the system and install Git](#13-update-the-system-and-install-git)
   - [x] [1.4 Install Docker on VM-1](#14-install-docker-on-vm-1)
   - [x] [1.5 Enable, start and check the Docker service](#15-enable-start-and-check-the-docker-service)
   - [x] [1.6 Allow `ec2-user` to run Docker commands without `sudo`](#16-allow-ec2-user-to-run-docker-commands-without-sudo)
   - [x] [1.7 Verify Docker works as `ec2-user`](#17-verify-docker-works-as-ec2-user)
2. [Install Docker Compose](#2-install-docker-compose)
   - [x] [2.1 Install the Docker Compose CLI plugin](#21-install-the-docker-compose-cli-plugin)
3. [Install easyTravel](#3-install-easytravel)
   - [x] [3.1 Clone the repository](#31-clone-the-repository)
   - [x] [3.2 Pull the images and start the containers](#32-pull-the-images-and-start-the-containers)
   - [x] [3.3 Open easyTravel in a browser](#33-open-easytravel-in-a-browser)
4. [Install Grafana](#4-install-grafana)
   - [x] [4.1 Create a working directory](#41-create-a-working-directory)
   - [x] [4.2 Pull the image](#42-pull-the-image)
   - [x] [4.3 Run the container](#43-run-the-container)
   - [x] [4.4 Open Grafana in a browser](#44-open-grafana-in-a-browser)
   - [ ] [4.5 Stop and start the Grafana container](#45-stop-and-start-the-grafana-container)
5. [Run PetClinic in Docker (optional)](#5-run-petclinic-in-docker-optional)
   - [ ] [5.1 Build and run the PetClinic image](#51-build-and-run-the-petclinic-image)
6. [Install OneAgent as a Docker container](#6-install-oneagent-as-a-docker-container)
   - [x] [6.1 Create a working directory](#61-create-a-working-directory)
   - [ ] [6.2 Set the token as an environment variable](#62-set-the-token-as-an-environment-variable)
   - [x] [6.3 Run the OneAgent container (without AppArmor)](#63-run-the-oneagent-container-without-apparmor)
   - [x] [6.4 Verify the OneAgent container is running](#64-verify-the-oneagent-container-is-running)
   - [x] [6.5 Restart the applications so OneAgent can monitor them](#65-restart-the-applications-so-oneagent-can-monitor-them)
7. [Enable user tag](#7-enable-user-tag)
   - [ ] [7.1 Capture the user name](#71-capture-the-user-name)
   - [ ] [7.2 Add a cleanup rule (regex) to format the user data](#72-add-a-cleanup-rule-regex-to-format-the-user-data)
8. [References](#references)

---

## 1. Default VM setup

### 1.1 Create two VMs

### 1.2 Log in and switch to root

```bash
sudo su -
```

### 1.3 Update the system and install Git

```bash
yum update -y
yum install git -y
git --version
```

### 1.4 Install Docker on VM-1

```bash
sudo dnf install docker -y
docker --version
```

### 1.5 Enable, start and check the Docker service

```bash
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

### 1.6 Allow `ec2-user` to run Docker commands without `sudo`

```bash
sudo usermod -aG docker ec2-user
```

> Log out and log back in for the group change to take effect.

### 1.7 Verify Docker works as `ec2-user`

```bash
docker ps
```

---

## 2. Install Docker Compose

### 2.1 Install the Docker Compose CLI plugin

```bash
sudo mkdir -p /usr/local/lib/docker/cli-plugins

COMPOSE_VERSION=v5.6.0
echo $COMPOSE_VERSION

sudo curl -fSL \
  "https://github.com/docker/compose/releases/download/${COMPOSE_VERSION}/docker-compose-linux-x86_64" \
  -o /usr/local/lib/docker/cli-plugins/docker-compose

sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
docker compose version
```

---

## 3. Install easyTravel

### 3.1 Clone the repository

```bash
git clone https://github.com/Dynatrace/easyTravel-Docker.git
cd easyTravel-Docker
```

### 3.2 Pull the images and start the containers

```bash
docker compose up -d     # pulls all images in docker-compose.yml and starts them
docker ps                # shows the running application containers
```

### 3.3 Open easyTravel in a browser

| App | URL |
|-----|-----|
| easyTravel | `http://<EC2_PUBLIC_IP>:32770` |
| easyTravel | `http://<EC2_PUBLIC_IP>:32771` |

> Check the actual ports in the `PORTS` column of `docker ps`.

---

## 4. Install Grafana

Docker Hub: <https://hub.docker.com/r/grafana/grafana-oss>

### 4.1 Create a working directory

```bash
mkdir /home/ec2-user/grafana
cd /home/ec2-user/grafana
```

### 4.2 Pull the image

```bash
docker pull grafana/grafana-oss
docker images
```

### 4.3 Run the container

```bash
docker run -d --name=grafana -p 3000:3000 grafana/grafana-oss
docker ps
```

### 4.4 Open Grafana in a browser

- URL: `http://<EC2_PUBLIC_IP>:3000/`
- Login: `admin` / `admin`. You're asked to change the password on first login.

### 4.5 Stop and start the Grafana container

Use the container **name** (`grafana`) or its **container ID** from `docker ps`.

```bash
docker stop grafana      # or: docker stop <container-id>
docker ps -a             # verify it shows "Exited"

docker start grafana     # start it again
docker ps                # verify it is running
```

---

## 5. Run PetClinic in Docker (optional)

### 5.1 Build and run the PetClinic image

Run these from the `spring-petclinic` folder that contains the `Dockerfile`:

```bash
docker build -t petclinic-webapp:v1 .
docker run -d --name petclinic -p 8080:8080 petclinic-webapp:v1
```

---

## 6. Install OneAgent as a Docker container

### 6.1 Create a working directory

```bash
mkdir /home/ec2-user/oneagent
cd /home/ec2-user/oneagent
```

### 6.2 Set the token as an environment variable

Never paste the token into this file.

```bash
export DT_INSTALLER_TOKEN='<YOUR_DYNATRACE_PLATFORM_TOKEN>'
```

### 6.3 Run the OneAgent container (without AppArmor)

```bash
docker run -d \
  --name dynatrace-oneagent \
  --restart=on-failure:5 \
  --read-only=true \
  --pid=host \
  --net=host \
  --cap-drop ALL \
  --cap-add CHOWN \
  --cap-add DAC_OVERRIDE \
  --cap-add DAC_READ_SEARCH \
  --cap-add FOWNER \
  --cap-add FSETID \
  --cap-add KILL \
  --cap-add NET_ADMIN \
  --cap-add NET_RAW \
  --cap-add SETFCAP \
  --cap-add SETGID \
  --cap-add SETUID \
  --cap-add SYS_ADMIN \
  --cap-add SYS_CHROOT \
  --cap-add SYS_PTRACE \
  --cap-add SYS_RESOURCE \
  --security-opt apparmor:unconfined \
  -v /:/mnt/root \
  -v oneagent-volume:/mnt/volume_storage_mount \
  -e ONEAGENT_ENABLE_VOLUME_STORAGE=true \
  -e 'ONEAGENT_INSTALLER_SCRIPT_URL=https://nes80740.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest?arch=x86' \
  -e ONEAGENT_INSTALLER_DOWNLOAD_TOKEN="$DT_INSTALLER_TOKEN" \
  dynatrace/oneagent \
  --set-monitoring-mode=fullstack \
  --set-app-log-content-access=true \
  --set-network-zone=petclinic
```

### 6.4 Verify the OneAgent container is running

```bash
docker ps
```

### 6.5 Restart the applications so OneAgent can monitor them

OneAgent only fully monitors processes that start after it is installed.

```bash
cd ~/easyTravel-Docker
docker compose down
docker compose up -d

docker restart grafana

docker ps
```

> The easyTravel and Angular frontend ports are dynamic. After a restart, check the new ports in `docker ps` and open the URLs again.

<details>
<summary>Generic template from the Dynatrace docs</summary>

```bash
docker run -d \
  --restart=on-failure:5 \
  --read-only=true \
  --pid=host \
  --net=host \
  --cap-drop ALL \
  --cap-add CHOWN \
  --cap-add DAC_OVERRIDE \
  --cap-add DAC_READ_SEARCH \
  --cap-add FOWNER \
  --cap-add FSETID \
  --cap-add KILL \
  --cap-add NET_ADMIN \
  --cap-add NET_RAW \
  --cap-add SETFCAP \
  --cap-add SETGID \
  --cap-add SETUID \
  --cap-add SYS_ADMIN \
  --cap-add SYS_CHROOT \
  --cap-add SYS_PTRACE \
  --cap-add SYS_RESOURCE \
  --security-opt apparmor:unconfined \
  -v /:/mnt/root \
  -v <volume name>:/mnt/volume_storage_mount \
  -e ONEAGENT_ENABLE_VOLUME_STORAGE=true \
  -e ONEAGENT_INSTALLER_SCRIPT_URL=<REPLACE_WITH_YOUR_URL> \
  -e ONEAGENT_INSTALLER_DOWNLOAD_TOKEN=<Api-Token> \
  dynatrace/oneagent <INSTALLER_PARAMETERS>
```

</details>

---

## 7. Enable user tag

### 7.1 Capture the user name

**Settings → select the application → User tag (Classic) → Capture properties → Capture event properties**

| Field | Value |
|-------|-------|
| Key | `username` |
| CSS selector | `a.greeting` |

### 7.2 Add a cleanup rule (regex) to format the user data

```regex
^.*?\s([A-Za-z]++)$
```

This keeps only the last word of the captured text. For example, `Welcome Maria` becomes `Maria`.

---

## References

- [Set up Dynatrace OneAgent as a Docker container](https://docs.dynatrace.com/docs/ingest-from/setup-on-container-platforms/docker/set-up-dynatrace-oneagent-as-docker-container)
- [Grafana OSS on Docker Hub](https://hub.docker.com/r/grafana/grafana-oss)
- [easyTravel-Docker](https://github.com/Dynatrace/easyTravel-Docker)
