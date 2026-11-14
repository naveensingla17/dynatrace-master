# Dynatrace Lab 2 – Monitor the easyTravel Application Running in Docker Containers

A hands-on lab that deploys the **easyTravel** sample application in Docker containers and monitors it with Dynatrace OneAgent, which also runs as a container.

## Lab steps

The commands for each step are in [01-install-docker-and-oneagent.md](01-install-docker-and-oneagent.md).

| # | Step | Description | Guide section |
|---|------|-------------|---------------|
| 1 | **Set up the VM** | Install Git and Docker, and let `ec2-user` run Docker without `sudo` | [1. Default VM setup](01-install-docker-and-oneagent.md#1-default-vm-setup) |
| 2 | **Install Docker Compose** | Install the Compose CLI plugin | [2. Install Docker Compose](01-install-docker-and-oneagent.md#2-install-docker-compose) |
| 3 | **Deploy easyTravel** | Run easyTravel with `docker compose` | [3. Install easyTravel](01-install-docker-and-oneagent.md#3-install-easytravel) |
| 4 | **Deploy Grafana** *(extra)* | Run Grafana as a second containerized app | [4. Install Grafana](01-install-docker-and-oneagent.md#4-install-grafana) |
| 5 | **Install OneAgent** | Run OneAgent as a Docker container on the host | [6. Install OneAgent](01-install-docker-and-oneagent.md#6-install-oneagent-as-a-docker-container) |
| 6 | **Check frontend vitals** | Check the easyTravel frontend in **Experience Vitals** | – |
| 7 | **Enable user tag** | Identify users in sessions | [7. Enable user tag](01-install-docker-and-oneagent.md#7-enable-user-tag) |
| 8 | **Conversion goal** | Define a goal, such as a completed booking, and track it | – |

> **Note:** OneAgent only fully monitors processes that start after it is installed. If you install it after the containers are running, restart the easyTravel and Grafana containers.

## Checklist

- [x] Set up the VM and install Docker
- [x] Install Docker Compose
- [x] Deploy easyTravel on Docker
- [x] Deploy Grafana on Docker
- [x] Install OneAgent as a Docker container
- [x] Check frontend vitals in Experience Vitals
- [ ] Enable user tagging
- [ ] Create and track a conversion goal

## Security note

Never commit the Dynatrace token. Set it as an environment variable (`DT_INSTALLER_TOKEN`) on the host, as shown in the guide.
