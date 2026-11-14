# Lab 1 – Install OneAgent

Install OneAgent in **full-stack** mode on the PetClinic host. Its traffic goes to Dynatrace through the ActiveGate in the `petclinic` Network Zone.

## Checklist

1. [Basic preparation and setup](#1-basic-preparation-and-setup)
   - [x] [1.1 Check the prerequisites](#11-check-the-prerequisites)
   - [x] [1.2 Generate a token](#12-generate-a-token)
2. [Install OneAgent](#2-install-oneagent)
   - [x] [2.1 In the Dynatrace console](#21-in-the-dynatrace-console)
   - [x] [2.2 On the PetClinic host](#22-on-the-petclinic-host)
3. [Post-installation tasks](#3-post-installation-tasks)
   - [x] [3.1 Disable automatic updates](#31-disable-automatic-updates)
   - [x] [3.2 Update the host group](#32-update-the-host-group)
   - [x] [3.3 Restart the application](#33-restart-the-application)
   - [x] [3.4 Verify the installation](#34-verify-the-installation)
   - [x] [3.5 Reference](#35-reference)

---

## 1. Basic preparation and setup

Complete these steps before you install OneAgent.

### 1.1 Check the prerequisites

| Item | Value |
|------|-------|
| Host | PetClinic VM from [02. Install_petclinic_application.md](02.%20Install_petclinic_application.md#11-create-a-vm) |
| ActiveGate | Running in Network Zone `petclinic` ([01. Install_ActiveGate.md](01.%20Install_ActiveGate.md#2-install-environment-activegate-and-create-a-network-zone)) |
| User | `root` (the installer needs root privileges) |
| Free space | At least 2 GB in `/opt` (the installer is ~270 MB) |

### 1.2 Generate a token

**Bottom left → Profile → Account Management →** click the user profile **→ Platform Tokens → Create token →** select permissions **→ Generate token**

> Use the scopes from [ActiveGate guide 1.4](01.%20Install_ActiveGate.md#14-generate-a-token). The token needs at least `fleet-management:oneagents:download`.

---

## 2. Install OneAgent

### 2.1 In the Dynatrace console

1. Go to **Infrastructure & Operations**
2. Click **Add hosts**
3. Provide the token
4. Select the Network Zone (`petclinic`)
5. Set the host name: `petclinic1`
6. Set the host group: `petclinic`
7. Add the host property: `env=demo`
8. Copy the generated commands

### 2.2 On the PetClinic host

9. Log in, switch to root and create a working directory:

   ```bash
   sudo su -
   mkdir -p /root/oneagent
   cd /root/oneagent
   ```

10. Set the token as an environment variable (never paste the token into this file):

    ```bash
    export DT_PLATFORM_TOKEN='<YOUR_DYNATRACE_PLATFORM_TOKEN>'
    ```

11. Download the installer (the console generates this command):

    ```bash
    wget -O Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh \
      "https://nes80740.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/version/1.349.109.20261102-164952?arch=x86&networkZone=petclinic" \
      --header="Authorization: Bearer $DT_PLATFORM_TOKEN"
    ```

12. Verify the signature:

    ```bash
    wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null
    ```

    Expected output: `CMS Verification successful`

13. Install OneAgent as a privileged user:

    ```bash
    /bin/sh Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh \
      --set-monitoring-mode=fullstack \
      --set-app-log-content-access=true \
      --set-network-zone=petclinic \
      --set-host-name=petclinic1 \
      --set-host-property=env=demo
    ```

    | Parameter | Purpose |
    |-----------|---------|
    | `--set-monitoring-mode=fullstack` | Full monitoring: infrastructure, processes, services and code-level traces |
    | `--set-app-log-content-access=true` | Lets OneAgent read application log content |
    | `--set-network-zone=petclinic` | Sends traffic through the ActiveGate in this Network Zone |
    | `--set-host-name=petclinic1` | Host name shown in Dynatrace |
    | `--set-host-property=env=demo` | Tag-like key/value property on the host |

    Expected last line: `Dynatrace OneAgent has successfully connected to Dynatrace Cluster Node. You are all set.` The full output is in [98. outputs/oneagent_installation_output.md](98.%20outputs/oneagent_installation_output.md).

14. Enable, start and check the service:

    ```bash
    sudo systemctl enable oneagent.service
    sudo systemctl start oneagent.service
    sudo systemctl status oneagent.service
    ```

15. Clean up:

    ```bash
    rm -f Dynatrace-OneAgent-Linux-*.sh dt-root.cert.pem
    unset DT_PLATFORM_TOKEN
    ```

---

## 3. Post-installation tasks

### 3.1 Disable automatic updates

Always disable OneAgent automatic updates in the lab, so the version doesn't change between sessions.

1. Go to **Settings → Fleet Management → OneAgent version update**
2. Select **Auto-update disabled**
3. Click **Save**

### 3.2 Update the host group

In Gen3 you can't change the host group in the UI. Use `oneagentctl` on the host:

```bash
cd /opt/dynatrace/oneagent/agent/tools/

systemctl stop oneagent.service
./oneagentctl --set-host-group=petclinic01
systemctl start oneagent.service

systemctl status oneagent.service
```

### 3.3 Restart the application

OneAgent only fully monitors processes that start after it is installed. Restart PetClinic:

```bash
sudo systemctl restart petclinic.service
sudo systemctl status petclinic.service
```

### 3.4 Verify the installation

1. Check the settings on the host:

   ```bash
   cd /opt/dynatrace/oneagent/agent/tools/
   ./oneagentctl --get-host-name
   ./oneagentctl --get-host-group
   ./oneagentctl --get-network-zone
   ./oneagentctl --get-host-properties
   ```

2. Check the status in the console: go to **Fleet Management**, or use the classic URL
   <https://nes80740.apps.dynatrace.com/ui/apps/dynatrace.classic.deployment.status>

3. Open **Infrastructure & Operations → Hosts → petclinic1** and check that the Java process and the Tomcat service are detected.

### 3.5 Reference

| Item | Command / Path |
|------|----------------|
| Service status | `systemctl status oneagent.service` |
| Command-line tool | `/opt/dynatrace/oneagent/agent/tools/oneagentctl` |
| Config directory | `/var/lib/dynatrace/oneagent/agent/config/` |
| Install log | `/var/log/dynatrace/oneagent/installer/installation_<id>.log` |
| Agent logs | `/var/log/dynatrace/oneagent/` |
| Uninstall script | `/opt/dynatrace/oneagent/agent/uninstall.sh` |
