# Dynatrace Lab 1 – Monitor a Spring PetClinic App on AWS EC2

A hands-on lab that sets up Dynatrace monitoring for the **Spring PetClinic** sample Java app on AWS EC2. It installs an **Environment ActiveGate** with a **Network Zone**, then **OneAgent** on the app host, whose traffic goes through the ActiveGate. It then explores **APM**, **Real User Monitoring (Experience Vitals)** and **Smartscape** in the Dynatrace UI.

## Architecture

```text
┌──────────────────────────┐        ┌──────────────────────────┐        ┌────────────────────┐
│ EC2: petclinic host      │        │ EC2: ActiveGate host     │        │ Dynatrace SaaS     │
│                          │        │                          │        │                    │
│  Spring PetClinic :8080  │──────▶ │  Environment ActiveGate  │──────▶ │  Tenant            │
│  OneAgent (fullstack)    │        │  group: ag01             │        │                    │
└──────────────────────────┘        └──────────────────────────┘        └────────────────────┘
              └──────────── Network Zone: petclinic ────────────┘
```

| # | Setting | Value |
|---|---------|-------|
| 1 | Network Zone | `petclinic` |
| 2 | ActiveGate group | `ag01` |
| 3 | Host name | `petclinic1` |
| 4 | Host group | `petclinic01` (set after installation, see [3.2 Update the host group](03-install-oneagent.md#32-update-the-host-group)) |
| 5 | Host property | `env=demo` |
| 6 | App port | `8080` |

## Prerequisites

| # | Prerequisite | Details |
|---|--------------|---------|
| 1 | **AWS account** | Permission to create EC2 instances and edit security groups |
| 2 | **Dynatrace tenant** | A trial tenant (a temporary email is fine for registration) |
| 3 | **Platform Token** | With the scopes listed in [1.4 Generate a token](01-install-activegate.md#14-generate-a-token) |
| 4 | **SSH key pair** | A `.pem` key for the EC2 instances |

## Guides

Follow the guides in this order:

| # | Guide | What it covers |
|---|-------|----------------|
| 1 | [01-install-activegate.md](01-install-activegate.md) | Security group rules, VMs, token creation, ActiveGate and Network Zone |
| 2 | [02-install-petclinic.md](02-install-petclinic.md) | Java 17, Maven, build PetClinic, run it as a `systemd` service |
| 3 | [03-install-oneagent.md](03-install-oneagent.md) | Install OneAgent, disable auto-updates, set the host group, verify |
| 4 | [04-explore-apm-and-rum.md](04-explore-apm-and-rum.md) | APM (services, backtrace), RUM / Experience Vitals, session replay, user types, conversion goals, Web Vitals, Apdex |

## Repository structure

```text
lab01-petclinic-ec2-apm-rum/
├── README.md
├── 01-install-activegate.md         # ActiveGate + Network Zone
├── 02-install-petclinic.md          # PetClinic app setup
├── 03-install-oneagent.md           # OneAgent install and post-installation tasks
├── 04-explore-apm-and-rum.md        # APM, RUM / Experience Vitals
├── outputs/                         # Console output captured during installs
│   ├── activegate-install-output.md
│   └── oneagent-install-output.md
└── archive/
    └── vm-installation.md           # Earlier draft notes
```

## Lab steps

| # | Step | Description | Guide section |
|---|------|-------------|---------------|
| 1 | **Prepare AWS** | Update the default security group and create the VMs | [1. Basic preparation and setup](01-install-activegate.md#1-basic-preparation-and-setup) |
| 2 | **Generate a token** | Create a Dynatrace Platform Token with the required scopes | [1.4 Generate a token](01-install-activegate.md#14-generate-a-token) |
| 3 | **Install the ActiveGate** | Install an Environment ActiveGate (group `ag01`) in the Network Zone `petclinic` | [2. Install Environment ActiveGate](01-install-activegate.md#2-install-environment-activegate-and-create-a-network-zone) |
| 4 | **Prepare the app host** | Install Git, Java 17 and Maven | [1. Basic preparation and setup](02-install-petclinic.md#1-basic-preparation-and-setup) |
| 5 | **Build PetClinic** | Clone, build the JAR and test the app on port `8080` | [2. Build and test PetClinic](02-install-petclinic.md#2-build-and-test-petclinic) |
| 6 | **Run PetClinic as a service** | Create and start the `petclinic` `systemd` service | [3. Run PetClinic as a service](02-install-petclinic.md#3-run-petclinic-as-a-service) |
| 7 | **Install OneAgent** | Install OneAgent (full-stack) on the PetClinic host | [2. Install OneAgent](03-install-oneagent.md#2-install-oneagent) |
| 8 | **Post-installation tasks** | Disable auto-updates, set host group `petclinic01`, restart the app, verify | [3. Post-installation tasks](03-install-oneagent.md#3-post-installation-tasks) |
| 9 | **Explore APM** | Service map, database queries and backtrace | [1. APM – Explore services](04-explore-apm-and-rum.md#1-apm--explore-services) |
| 10 | **Set up Experience Vitals** | Disable default RUM, create a frontend, generate traffic, view a session replay | [2. RUM – Set up Experience Vitals](04-explore-apm-and-rum.md#2-rum--set-up-experience-vitals) |
| 11 | **Explore Experience Vitals** | Overview, user experience score, user sessions dashboard | [3. RUM – Explore Experience Vitals](04-explore-apm-and-rum.md#3-rum--explore-experience-vitals) |
| 12 | **Conversion goals and error rules** | Define a conversion goal and custom error rules | [5. Conversion goals](04-explore-apm-and-rum.md#5-conversion-goals) |
| 13 | **Explore Smartscape** | Service dependencies, infrastructure overview, upstream and downstream relationships, full topology | – |

> **Note:** OneAgent only fully monitors processes that start after it is installed. After installing OneAgent or changing its configuration, restart PetClinic (`systemctl restart petclinic.service`).

## Quick navigation in Dynatrace

| # | What | Path |
|---|------|------|
| 1 | Deployment status | **Fleet Management** |
| 2 | Services | **Services → Tomcat** (or **Search → Services → Tomcat**) |
| 3 | Request outliers | **Services → Tomcat → More actions → Analyze request outliers** |
| 4 | Session replay | **Experience Vitals → petclinic → Open with Users & Sessions → session → Session Replay** |
| 5 | User sessions dashboard | **Dashboards → User Sessions Overview** |
| 6 | Conversion goals | **Settings → frontend app → Process and contextualize → Conversion goals** |
| 7 | OneAgent auto-update | **Settings → Fleet management → OneAgent version and updates** |

## Checklist

**ActiveGate and PetClinic**

- [x] Prepare AWS (security group and VMs)
- [x] Generate a Platform Token
- [x] Install the Environment ActiveGate and create the Network Zone
- [x] Install Git, Java 17 and Maven
- [x] Build and test PetClinic
- [x] Run PetClinic as a service

**OneAgent**

- [x] Install OneAgent on the PetClinic host
- [x] Disable automatic updates
- [x] Update the host group
- [x] Verify the installation

**APM and RUM**

- [x] Explore services in APM
- [x] Set up Experience Vitals
- [x] Explore Experience Vitals
- [ ] Create a conversion goal and custom error rules
- [ ] Explore Smartscape

## Security note

Never commit Dynatrace tokens. Use placeholders such as `<YOUR_DYNATRACE_PLATFORM_TOKEN>` in the guides. If you commit one by mistake, revoke it in Dynatrace and remove it from git history before you push. The security group in this lab is open to `0.0.0.0/0` for convenience, so only use it for short-lived lab instances, and delete `connection.properties` after installing the ActiveGate (`shred -u connection.properties`).

## References

- [Spring PetClinic](https://github.com/spring-projects/spring-petclinic)
- [Dynatrace ActiveGate documentation](https://docs.dynatrace.com/docs/ingest-from/dynatrace-activegate)
- [Dynatrace OneAgent documentation](https://docs.dynatrace.com/docs/ingest-from/dynatrace-oneagent)
