# Dynatrace Lab 2 – Monitor easyTravel in Docker and Set Up Synthetic Monitoring

A hands-on lab that deploys the **easyTravel** sample application in Docker containers and monitors it with Dynatrace OneAgent, which also runs as a container. It then adds **synthetic monitoring** from a private location: HTTP and browser monitors that check the app on a schedule.

## Architecture

```text
┌────────────────────────────────┐                     ┌────────────────────┐
│ EC2 VM-1: Docker host          │   OneAgent data     │ Dynatrace SaaS     │
│                                │───────────────────▶ │                    │
│ easyTravel   :32770/32771      │                     │  Tenant            │
│ Grafana      :3000             │                     │                    │
│ OneAgent (container)           │                     │                    │
└────────────────────────────────┘                     └────────────────────┘
                 ▲                                                ▲
                 │ HTTP and browser checks                        │ monitor results
┌────────────────┴───────────────┐                                │
│ EC2 VM-2: Synthetic ActiveGate │────────────────────────────────┘
│ group: pvt01                   │
│ private location: SG-Punggol   │
└────────────────────────────────┘
          └──────────── Network Zone: petclinic ────────────┘
```

| # | Setting | Value |
|---|---------|-------|
| 1 | Network Zone | `petclinic` |
| 2 | Synthetic ActiveGate group | `pvt01` |
| 3 | Synthetic ActiveGate VM | `t2.medium` (2 vCPU, 4 GB RAM), 60 GB, Amazon Linux |
| 4 | Private location | `SG-Punggol` (Singapore) |
| 5 | easyTravel ports | `32770`, `32771` (dynamic, check `docker ps`) |
| 6 | Grafana port | `3000` |
| 7 | Monitor frequency | Every 5 minutes |

## Guides

Follow the guides in this order:

| # | Guide | What it covers |
|---|-------|----------------|
| 1 | [01-install-docker-and-oneagent.md](01-install-docker-and-oneagent.md) | Docker, Docker Compose, easyTravel, Grafana, OneAgent as a container, user tag |
| 2 | [02-synthetic-monitoring-concepts.md](02-synthetic-monitoring-concepts.md) | Monitor types, locations, API monitoring, HTTP status codes, authentication |
| 3 | [03-configure-synthetic-monitoring.md](03-configure-synthetic-monitoring.md) | Private Synthetic location, HTTP and browser monitors, synthetic and RUM pricing |

## Repository structure

```text
lab02-easytravel-docker-synthetic/
├── README.md
├── 01-install-docker-and-oneagent.md      # Docker, easyTravel, Grafana, OneAgent container, user tag
├── 02-synthetic-monitoring-concepts.md    # Monitor types, locations, status codes, authentication
└── 03-configure-synthetic-monitoring.md   # Private location, HTTP and browser monitors, pricing
```

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
| 10 | **Install a Synthetic ActiveGate** | Create a VM and install a Synthetic-enabled ActiveGate | [1. Install a Synthetic-enabled ActiveGate](03-configure-synthetic-monitoring.md#1-install-a-synthetic-enabled-activegate) |
| 11 | **Create the private location** | Create `SG-Punggol` on the Synthetic ActiveGate | [2. Configure the private location](03-configure-synthetic-monitoring.md#2-configure-the-private-location) |
| 12 | **Create an HTTP monitor** | Check easyTravel from the private location, then test it on demand | [3. Configure an HTTP monitor](03-configure-synthetic-monitoring.md#3-configure-an-http-monitor) |
| 13 | **Monitor a POST API** | Send a `POST` with a JSON body from an HTTP monitor | [3.4 Monitor a POST API](03-configure-synthetic-monitoring.md#34-monitor-a-post-api) |
| 14 | **Create a browser monitor** | Load easyTravel in a real browser and validate the page content | [4. Configure a browser monitor](03-configure-synthetic-monitoring.md#4-configure-a-browser-monitor) |
| 15 | **Record a clickpath** | Record a user journey with the Synthetic Recorder and run it as a browser monitor | [4.2 Clickpath with the Synthetic Recorder](03-configure-synthetic-monitoring.md#42-clickpath-with-the-synthetic-recorder) |
| 16 | **Estimate the cost** | Calculate synthetic and RUM cost from the rate card | [5. Pricing](03-configure-synthetic-monitoring.md#5-synthetic-and-rum-pricing) |

> **Note:** OneAgent only fully monitors processes that start after it is installed. If you install it after the containers are running, restart the easyTravel and Grafana containers.

## Quick navigation in Dynatrace

| # | What | Path |
|---|------|------|
| 1 | Frontend vitals | **Experience Vitals** |
| 2 | User tag | **Settings → application → User tag (Classic) → Capture properties → Capture event properties** |
| 3 | Install a Synthetic ActiveGate | **Discovery & Coverage → Install → Install ActiveGate → Synthetic** |
| 4 | Private location | **Synthetic → New private location → Classic** |
| 5 | Create a monitor | **Synthetic → Create a synthetic monitor → HTTP monitor / Browser monitor** |
| 6 | Run a monitor on demand | **Synthetic → monitor → View details → On-demand execution → Trigger now** |
| 7 | Last result (JSON) | **Synthetic → monitor → Analyze data → Last successful** |
| 8 | Usage and cost | **Account Management → Subscription** |

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

- [x] Install a Synthetic-enabled ActiveGate
- [x] Create the private location `SG-Punggol`
- [x] Create and test an HTTP monitor
- [x] Monitor a POST API
- [x] Create a browser monitor
- [x] Record a clickpath with the Synthetic Recorder
- [x] Estimate the synthetic and RUM cost

## Security note

Never commit Dynatrace tokens. Set them as environment variables (for example `DT_INSTALLER_TOKEN`) on the host, and use placeholders such as `<YOUR_TENANT_TOKEN>` in `connection.properties` examples, as shown in the guides.

## References

- [easyTravel-Docker](https://github.com/Dynatrace/easyTravel-Docker)
- [Grafana OSS on Docker Hub](https://hub.docker.com/r/grafana/grafana-oss)
- [Set up Dynatrace OneAgent as a Docker container](https://docs.dynatrace.com/docs/ingest-from/setup-on-container-platforms/docker/set-up-dynatrace-oneagent-as-docker-container)
- [Create a private Synthetic location](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/private-synthetic-locations/create-a-private-synthetic-location)
- [Manage private Synthetic locations – Browser autoupdate](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/private-synthetic-locations/manage-private-synthetic-locations)
- [Configure HTTP monitors in Classic](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/http-monitors-classic/configure-http-monitors-classic)
- [Synthetic monitor authentication](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/general-information/synthetic-authentication)
- [Dynatrace pricing](https://www.dynatrace.com/pricing/)
