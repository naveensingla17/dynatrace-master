# Dynatrace Lab 2 – Monitor easyTravel in Docker and Set Up Synthetic Monitoring

A hands-on lab that deploys the **easyTravel** sample application in Docker containers and monitors it with Dynatrace OneAgent, which also runs as a container. It then adds **synthetic monitoring** from a private location: HTTP and browser monitors that check the app on a schedule.

## Guides

Follow the guides in this order:

| # | Guide | What it covers |
|---|-------|----------------|
| 1 | [01-install-docker-and-oneagent.md](01-install-docker-and-oneagent.md) | Docker, Docker Compose, easyTravel, Grafana, OneAgent as a container, user tag |
| 2 | [02-synthetic-monitoring-concepts.md](02-synthetic-monitoring-concepts.md) | Monitor types, locations, API monitoring, HTTP status codes, authentication |
| 3 | [03-configure-synthetic-monitoring.md](03-configure-synthetic-monitoring.md) | Private Synthetic location, HTTP and browser monitors, synthetic and RUM pricing |

## Lab steps

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
| 9 | **Learn synthetic concepts** | Monitor types, public vs private locations, status code rules | [Synthetic monitoring concepts](02-synthetic-monitoring-concepts.md) |
| 10 | **Install a Synthetic ActiveGate** | Create a VM and install a Synthetic-enabled ActiveGate | [1. Configure a private Synthetic location](03-configure-synthetic-monitoring.md#1-configure-a-private-synthetic-location) |
| 11 | **Create the private location** | Create `SG-Punggol` on the Synthetic ActiveGate | [2.1 Configure the private location](03-configure-synthetic-monitoring.md#21-configure-the-private-location-prerequisite) |
| 12 | **Create an HTTP monitor** | Check easyTravel from the private location, then test it on demand | [2.2 Configure an HTTP monitor](03-configure-synthetic-monitoring.md#22-configure-an-http-monitor) |
| 13 | **Create a browser monitor** | Load easyTravel in a real browser and validate the page content | [2.3 Configure a browser monitor](03-configure-synthetic-monitoring.md#23-configure-a-browser-monitor) |
| 14 | **Estimate the cost** | Calculate synthetic and RUM cost from the rate card | [3. Pricing](03-configure-synthetic-monitoring.md#3-dynatrace-synthetic-and-rum-pricing) |

> **Note:** OneAgent only fully monitors processes that start after it is installed. If you install it after the containers are running, restart the easyTravel and Grafana containers.

## Checklist

**Docker and OneAgent**

- [x] Set up the VM and install Docker
- [x] Install Docker Compose
- [x] Deploy easyTravel on Docker
- [x] Deploy Grafana on Docker
- [x] Install OneAgent as a Docker container
- [x] Check frontend vitals in Experience Vitals
- [ ] Enable user tagging
- [ ] Create and track a conversion goal

**Synthetic monitoring**

- [ ] Install a Synthetic-enabled ActiveGate
- [ ] Create the private location `SG-Punggol`
- [ ] Create and test an HTTP monitor
- [ ] Create a browser monitor
- [ ] Estimate the synthetic and RUM cost

## Security note

Never commit Dynatrace tokens. Set them as environment variables (for example `DT_INSTALLER_TOKEN`) on the host, and use placeholders such as `<YOUR_TENANT_TOKEN>` in `connection.properties` examples, as shown in the guides.
