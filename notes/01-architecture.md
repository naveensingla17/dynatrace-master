# Architecture

## Contents

1. [Lab 1 – PetClinic on EC2 (RUM)](#lab-1--petclinic-on-ec2-rum)
2. [Lab 2 – easyTravel on Docker](#lab-2--easytravel-on-docker)
3. [Concept: Environment ActiveGate group](#concept-environment-activegate-group)

---

## Lab 1 – PetClinic on EC2 (RUM)

Guide: [lab01-petclinic-ec2-apm-rum](../lab01-petclinic-ec2-apm-rum/README.md)

```text
   ┌──────────────┐
   │ User browser │  RUM JavaScript (ruxitagent) injected into pages
   └──────┬───────┘
          │ HTTP :8080
          ▼
┌──────────────────────────┐        ┌──────────────────────────┐        ┌────────────────────┐
│ EC2: petclinic host      │        │ EC2: ActiveGate host     │        │ Dynatrace SaaS     │
│                          │        │                          │        │                    │
│  Spring PetClinic :8080  │──────▶ │  Environment ActiveGate  │──────▶ │  Tenant            │
│  (systemd service)       │        │  group: ag01             │        │  Experience Vitals │
│  OneAgent (fullstack,    │        │                          │        │  Services / APM    │
│  installed on the host)  │        │                          │        │  Smartscape        │
└──────────────────────────┘        └──────────────────────────┘        └────────────────────┘
              └──────────── Network Zone: petclinic ────────────┘
```

| Component | Details |
|-----------|---------|
| App host | EC2, Spring PetClinic on port `8080`, run as `petclinic.service` |
| OneAgent | Installed on the host with the installer script, full-stack mode, host name `petclinic1`, host group `petclinic01`, property `env=demo` |
| ActiveGate | Environment ActiveGate, group `ag01` |
| Network Zone | `petclinic` |

---

## Lab 2 – easyTravel on Docker

Guide: [lab02-easytravel-docker-synthetic](../lab02-easytravel-docker-synthetic/README.md)

```text
   ┌──────────────┐
   │ User browser │  RUM JavaScript injected · user tag captured (a.greeting)
   └──────┬───────┘
          │ HTTP :32770 / :32771 (easyTravel) · :3000 (Grafana)
          ▼
┌───────────────────────────────────────────┐        ┌──────────────────────────┐        ┌────────────────────┐
│ EC2: VM-1 (Docker host)                   │        │ EC2: ActiveGate host     │        │ Dynatrace SaaS     │
│                                           │        │                          │        │                    │
│  ┌─────────────────────────────────────┐  │        │                          │        │                    │
│  │ docker compose: easyTravel          │  │        │                          │        │  Tenant            │
│  │  (frontend, backend, DB containers) │  │        │  Environment ActiveGate  │        │  Experience Vitals │
│  └─────────────────────────────────────┘  │──────▶ │  group: ag01             │──────▶ │  User tags         │
│  ┌──────────────┐ ┌──────────────────┐    │        │                          │        │  Conversion goals  │
│  │ grafana :3000│ │ petclinic :8080  │    │        │                          │        │                    │
│  └──────────────┘ │ (optional)       │    │        │                          │        │                    │
│                   └──────────────────┘    │        │                          │        │                    │
│  ┌─────────────────────────────────────┐  │        │                          │        │                    │
│  │ dynatrace-oneagent container        │  │        │                          │        │                    │
│  │ fullstack · --pid=host · --net=host │  │        │                          │        │                    │
│  │ monitors the host + all containers  │  │        │                          │        │                    │
│  └─────────────────────────────────────┘  │        │                          │        │                    │
└───────────────────────────────────────────┘        └──────────────────────────┘        └────────────────────┘
                     └──────────────── Network Zone: petclinic ────────────────┘
```

| Component | Details |
|-----------|---------|
| Docker host | EC2 VM-1 with Docker and Docker Compose |
| easyTravel | Started with `docker compose up -d`. The ports are dynamic, so check `docker ps` |
| Grafana | `grafana/grafana-oss` container on port `3000` |
| PetClinic (optional) | `petclinic-webapp:v1` container on port `8080` |
| OneAgent | Runs **as a Docker container** (`dynatrace/oneagent`), full-stack mode. `--pid=host` and `--net=host` let it see every process on the host |
| Network Zone | `petclinic` |

### Lab 1 vs Lab 2

| | Lab 1 | Lab 2 |
|---|---|---|
| Application | Spring PetClinic | easyTravel (plus Grafana and PetClinic) |
| Runs as | A `systemd` service on the host | Docker containers |
| OneAgent install | Installer script on the host | `dynatrace/oneagent` Docker container |
| Focus | APM, RUM, Smartscape | RUM on containers, user tags, conversion goals |

> In both labs, OneAgent only fully monitors processes that start **after** it is installed. Restart the app or containers afterwards (`docker compose down && docker compose up -d`).

---

## Concept: Environment ActiveGate group

An ActiveGate group gives **reliability**: if one ActiveGate goes down, another one in the group is still available.

```text
               ┌──────────────────────────────┐
               │   ActiveGate Group: AG1      │
               │                              │
               │   ┌────────┐    ┌────────┐   │
               │   │  AG-A  │    │  AG-B  │   │
               │   └────────┘    └────────┘   │
               └──────────────┬───────────────┘
                              │
                              ▼
                      Database plugin
              (runs on any available AG in the group)
```
