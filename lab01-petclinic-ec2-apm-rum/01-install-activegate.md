# Lab 1 – Install Environment ActiveGate

## Checklist

1. [Basic preparation and setup](#1-basic-preparation-and-setup)
   - [x] [1.1 Update the default security group](#11-update-the-default-security-group)
   - [x] [1.2 Create VMs](#12-create-vms)
   - [x] [1.3 Create a demo instance](#13-create-a-demo-instance)
   - [x] [1.4 Generate a token](#14-generate-a-token)
2. [Install Environment ActiveGate and create a Network Zone](#2-install-environment-activegate-and-create-a-network-zone)
   - [x] [2.1 In the Dynatrace console](#21-in-the-dynatrace-console)
   - [x] [2.2 On the ActiveGate server](#22-on-the-activegate-server)

---

## 1. Basic preparation and setup

Complete these steps before you install the ActiveGate.

### 1.1 Update the default security group

1. Go to **EC2 → Security Groups**
2. Update the **default** security group as below

**Inbound rules (8):** All IPv4, source `0.0.0.0/0`

| # | Type         | Protocol | Port range | Source    |
|---|--------------|----------|------------|-----------|
| 1 | MYSQL/Aurora | TCP      | 3306       | 0.0.0.0/0 |
| 2 | HTTP         | TCP      | 80         | 0.0.0.0/0 |
| 3 | SSH          | TCP      | 22         | 0.0.0.0/0 |
| 4 | All traffic  | All      | All        | 0.0.0.0/0 |
| 5 | HTTPS        | TCP      | 443        | 0.0.0.0/0 |
| 6 | RDP          | TCP      | 3389       | 0.0.0.0/0 |
| 7 | MSSQL        | TCP      | 1433       | 0.0.0.0/0 |
| 8 | PostgreSQL   | TCP      | 5432       | 0.0.0.0/0 |

### 1.2 Create VMs

| Setting | Value |
|---------|-------|
| Instance type | `t2.medium` (2 vCPU, 4 GB RAM) |
| Storage | 60 GB |
| Image | Amazon Linux |

### 1.3 Create a demo instance

Register for a Dynatrace trial tenant. A temporary email is fine for registration.

### 1.4 Generate a token

1. **Bottom left → Profile → Account Management →** click the user profile (top right) **→ Platform Tokens → Create token →** select permissions **→ Generate token**
2. There is no PaaS token option in the new-generation Dynatrace.
3. Select one of the scope sets below.

<details>
<summary><b>Option A – Minimal scopes</b></summary>

```text
fleet-management:oneagents:download
fleet-management:oneagents:read
fleet-management:oneagents:write
fleet-management:activegates:download
fleet-management:activegates:read
storage:metrics:read
storage:metrics:write
storage:logs:read
storage:logs:write
data-acquisition:metrics:ingest
app-engine:apps:run
extensions:configurations:read
extensions:configurations:write
```

</details>

<details>
<summary><b>Option B – Full scopes</b></summary>

```text
data-acquisition:events:ingest
data-acquisition:logs:ingest
data-acquisition:metrics:ingest
extensions:configurations:read
extensions:configurations:write
extensions:definitions:read
extensions:definitions:write
fleet-management:activegate.connection-info:read
fleet-management:activegate.tokens:create
fleet-management:activegates:download
fleet-management:activegates:read
fleet-management:activegates:write
fleet-management:cluster-id:read
fleet-management:container-images:read
fleet-management:oneagent.connection-info:read
fleet-management:oneagent.tokens:read
fleet-management:oneagents:download
fleet-management:public-addresses:read
openpipeline:bizevents:ingest
openpipeline:events:ingest
openpipeline:events.custom:ingest
openpipeline:events.davis:ingest
openpipeline:events.sdlc:ingest
openpipeline:events.sdlc.custom:ingest
openpipeline:events.smartscape:ingest
openpipeline:logs:ingest
openpipeline:metrics:ingest
openpipeline:security.events:ingest
openpipeline:security.events.custom:ingest
openpipeline:traces:ingest
rum:frontends:write
rum:manual-insertion-tags:read
settings:objects:read
settings:objects:write
storage:buckets:read
storage:entities:read
storage:events:read
storage:events:write
storage:files:write
storage:logs:read
storage:logs:write
storage:metrics:read
storage:metrics:write
storage:smartscape:read
storage:spans:read
storage:system:read
```

</details>

4. To automate token generation:

   ```bash
   curl -X POST "https://nes80740.live.dynatrace.com/api/v2/apiTokens" \
     -H "accept: application/json; charset=utf-8" \
     -H "Content-Type: application/json; charset=utf-8" \
     -H "Authorization: Api-Token <YOUR_DYNATRACE_API_TOKEN>" \
     -d '{"scopes":["InstallerDownload","SupportAlert"],"name":"agent-installation"}'
   ```

---

## 2. Install Environment ActiveGate and create a Network Zone

### 2.1 In the Dynatrace console

1. Go to **Discovery & Coverage**
2. Click **Install** (right side)
3. Select **Install ActiveGate**
4. Keep the default options and click **Next**
5. Enter a Network Zone name (e.g. `petclinic`) and click **+** to create it
6. Similarly, create an ActiveGate Group named `ag01`

### 2.2 On the ActiveGate server

7. Download the installer:

   ```bash
   wget -O Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh \
     "https://nes80740.apps.dynatrace.com/platform/fleet-management/v1/activegate/installer/unix/amd64/1.349.80.20261105-202347:download" \
     --header="Authorization: Bearer <YOUR_DYNATRACE_PLATFORM_TOKEN>" \
     --header="accept: application/octet-stream"
   ```

8. Verify the script is authentic:

   ```bash
   wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null
   ```

9. Copy the connection properties from the console into a file:

   ```bash
   vi connection.properties
   ```

10. Run the installer:

    ```bash
    /bin/bash Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh \
      --connection-properties=connection.properties \
      --set-network-zone=petclinic \
      --set-group=ag01
    ```

11. Check the status:

    ```bash
    systemctl status dynatracegateway
    systemctl status extensionsmodule
    ```

12. Clean up:

    ```bash
    shred -u connection.properties
    rm -f Dynatrace-ActiveGate-Linux-x86-*.sh
    ```

13. Check the status in the console: go to **Fleet Management**, or use the classic URL
    <https://nes80740.apps.dynatrace.com/ui/apps/dynatrace.classic.deployment.status>

14. To view the Network Zone: **Settings → Fleet Management**

### 2.3 Reference

| Item | Command / Path |
|------|----------------|
| Uninstall | `/opt/dynatrace/gateway/uninstall.sh` |
| Config properties | `/var/lib/dynatrace/gateway/config/config.properties` |
| Connected cluster | `/var/lib/dynatrace/gateway/config/cluster.properties` |
| Install log | `/var/log/dynatrace/gateway/dynatracegateway-install_<timestamp>.log` |
