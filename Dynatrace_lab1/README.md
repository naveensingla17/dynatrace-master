# Dynatrace Lab 1 – Monitor a Spring PetClinic App on AWS EC2

A hands-on lab that sets up Dynatrace monitoring for a sample Java app running on AWS EC2:

1. Install an **Environment ActiveGate** and create a **Network Zone**
2. Deploy the **Spring PetClinic** application
3. Install **OneAgent** on the app host. Its traffic goes through the ActiveGate.

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

| Setting | Value |
|---|---|
| Network Zone | `petclinic` |
| ActiveGate Group | `ag01` |
| Host name | `petclinic1` |
| Host property | `env=demo` |
| App port | `8080` |

## Prerequisites

- An AWS account with permission to create EC2 instances and edit security groups
- A Dynatrace trial tenant (a temporary email is fine for registration)
- A Dynatrace **Platform Token** with the scopes listed in [01. Install_agent_gateway.md](01.%20Install_agent_gateway.md#4-generate-token)
- An SSH key pair (`.pem`) for the EC2 instances

## Lab steps

Follow the guides in this order:

| # | Guide | What it covers |
|---|---|---|
| 1 | [01. Install_agent_gateway.md](01.%20Install_agent_gateway.md) | Security group rules, VMs, token creation, ActiveGate and Network Zone |
| 2 | [02. install_petclinic_application.md](02.%20install_petclinic_application.md) | Java 17, Maven, build PetClinic, run it as a `systemd` service |
| 3 | [03. installOneagent.md](03.%20installOneagent.md) | Install OneAgent on the app host and disable auto-updates |

## Repository structure

```text
Dynatrace_lab1/
├── README.md
├── 01. Install_agent_gateway.md          # ActiveGate + Network Zone
├── 02. install_petclinic_application.md  # PetClinic app setup
├── 03. installOneagent.md                # OneAgent install
├── outputs/                              # Console output captured during installs
│   ├── activegateway_installation_output.md
│   └── oneagent_installation_output.md
└── archive/
    └── vm-installation.md                # Earlier draft notes
```

## Verify the setup

```bash
# ActiveGate host
systemctl status dynatracegateway

# PetClinic host
systemctl status petclinic.service
systemctl status oneagent.service
curl -I http://localhost:8080
```

In Dynatrace, go to **Fleet Management** (or **Deployment Status**). The ActiveGate and the `petclinic1` host should both be listed as connected.

## Security notes

- **Never commit tokens.** The guides use placeholders such as `<YOUR_DYNATRACE_PLATFORM_TOKEN>`. GitHub push protection blocks commits that contain Dynatrace tokens. If you commit one by mistake, revoke it in Dynatrace and remove it from git history before you push.
- The security group in this lab is open to `0.0.0.0/0` for convenience. Only use it for short-lived lab instances. Restrict SSH and RDP to your own IP, and remove the *All traffic* rule, for anything longer-lived.
- Delete `connection.properties` after installing the ActiveGate (`shred -u connection.properties`).

## Cleanup

```bash
/opt/dynatrace/oneagent/agent/uninstall.sh      # PetClinic host
/opt/dynatrace/gateway/uninstall.sh             # ActiveGate host
```

Then terminate the EC2 instances in AWS.

## References

- [Spring PetClinic](https://github.com/spring-projects/spring-petclinic)
- [Dynatrace ActiveGate documentation](https://docs.dynatrace.com/docs/ingest-from/dynatrace-activegate)
- [Dynatrace OneAgent documentation](https://docs.dynatrace.com/docs/ingest-from/dynatrace-oneagent)
