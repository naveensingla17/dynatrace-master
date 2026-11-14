# Configure Synthetic Monitoring

For the concepts (monitor types, locations, authentication), see [02-synthetic-monitoring-concepts.md](02-synthetic-monitoring-concepts.md).

## Checklist

1. [Configure a private Synthetic location](#1-configure-a-private-synthetic-location)
   - [ ] [1.1 Create a VM for the ActiveGate](#11-create-a-vm-for-the-activegate)
   - [ ] [1.2 Install a Synthetic-enabled ActiveGate](#12-install-a-synthetic-enabled-activegate)
2. [Post-installation tasks](#2-post-installation-tasks)
   - [ ] [2.1 Configure the private location (prerequisite)](#21-configure-the-private-location-prerequisite)
   - [ ] [2.2 Configure an HTTP monitor](#22-configure-an-http-monitor)
     - [ ] [2.2.1 Test the HTTP monitor](#221-test-the-http-monitor)
   - [ ] [2.3 Configure a browser monitor](#23-configure-a-browser-monitor)
3. [Dynatrace synthetic and RUM pricing](#3-dynatrace-synthetic-and-rum-pricing)
   - [ ] [3.1 HTTP monitor](#31-http-monitor)
   - [ ] [3.2 Browser monitor](#32-browser-monitor)
4. [References](#references)

---

## 1. Configure a private Synthetic location

A private location runs synthetic monitors from inside your own network, on a Synthetic-enabled Environment ActiveGate.

### 1.1 Create a VM for the ActiveGate

| Setting | Value |
|---------|-------|
| Instance type | `t2.medium` (2 vCPU, 4 GB RAM) |
| Storage | 60 GB |
| Image | Amazon Linux |

### 1.2 Install a Synthetic-enabled ActiveGate

In the Dynatrace console:

1. Go to **Discovery & Coverage**
2. Click **Install** (top right)
3. Select **Install ActiveGate**
4. Select **Synthetic** as the ActiveGate purpose
5. Select the Network Zone `petclinic`
6. Create an ActiveGate Group named `pvt01`

On the ActiveGate server:

7. Create `connection.properties` with the values shown in the console (never paste the tokens into this file):

   ```properties
   SERVER=https://nes80740.live.dynatrace.com/communication
   TENANT=nes80740
   TENANT_TOKEN=<YOUR_TENANT_TOKEN>
   ACTIVEGATE_AUTH_TOKEN=<YOUR_ACTIVEGATE_AUTH_TOKEN>
   ```

8. Download the installer:

   ```bash
   wget -O Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh \
     "https://nes80740.apps.dynatrace.com/platform/fleet-management/v1/activegate/installer/unix/amd64/1.349.80.20261105-202347:download" \
     --header="Authorization: Bearer <YOUR_DYNATRACE_PLATFORM_TOKEN>" \
     --header="accept: application/octet-stream"
   ```

9. Verify the script is authentic:

   ```bash
   wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null
   ```

10. Run the installer with Synthetic enabled:

    ```bash
    /bin/bash Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh \
      --connection-properties=connection.properties \
      --enable-synthetic \
      --set-network-zone=petclinic \
      --set-group=pvt01
    ```

11. Check the status:

    ```bash
    systemctl status dynatracegateway.service
    ```

---

## 2. Post-installation tasks

### 2.1 Configure the private location (prerequisite)

> **Prerequisite:** Create the private location before you configure any synthetic monitor that runs from it.

1. Go to **Synthetic** and click **New private location** (top right)
2. Select **Classic**
3. Fill in the details:

   | Field | Value |
   |-------|-------|
   | Location name | `SG-Punggol` |
   | Geographic location | Singapore, Singapore |
   | ActiveGate | Select the existing Synthetic-enabled ActiveGate (group `pvt01`) |
   | Chromium auto-update | **Leave unselected** |
   | Location outage handling | Select the options to be notified when the location has an outage |

4. Click **Save** to create the private location.

#### Why leave Chromium auto-update off

The private location uses Chromium (the open-source browser behind Chrome) to run browser monitors. With auto-update on, Dynatrace updates it automatically whenever a new version is released.

Keep it off because:

1. If an update fails, for example because of a network issue, it can break synthetic monitoring on the location and you have to re-create it.
2. Updates run without advance notice. This causes unplanned monitoring downtime and unnecessary downtime alerts.

Instead, update Chromium manually in a planned maintenance window.

### 2.2 Configure an HTTP monitor

> **Prerequisite:** [2.1 Configure the private location](#21-configure-the-private-location-prerequisite)

1. Go to **Synthetic** → **Create a synthetic monitor** → **HTTP monitor**
2. Enter the monitor name and tags, and select the existing frontend
3. Add the HTTP request:

   | Setting | Value |
   |---------|-------|
   | Request name | A name for the request |
   | URL | The URL to check, for example the easyTravel URL |
   | Authentication | Only if the URL needs a login |
   | SSL | Certificate options, for example accept any SSL certificate |
   | Response status code verification | Default: **Fail** if the status code is `>=400` |

4. Set the frequency to **5 minutes** and select the private location `SG-Punggol`
5. Choose the outage alert options and save the monitor

> **Check that the app is reachable from the private location.** The monitor runs on the Synthetic ActiveGate, so run this on the ActiveGate server:
>
> ```bash
> curl -I http://<EC2_PUBLIC_IP>:32770/
> ```
>
> You should get `HTTP/1.1 200 OK`. If the request times out, allow the port in the easyTravel VM's security group.

> For the status code rules you can enter, see [Response status code verification](02-synthetic-monitoring-concepts.md#153-response-status-code-verification). For what each code means, see [HTTP status codes](02-synthetic-monitoring-concepts.md#154-http-status-codes).

#### 2.2.1 Test the HTTP monitor

Run the monitor on demand to check that it works, instead of waiting for the next scheduled run:

1. Go to **Synthetic** and select the monitor
2. Click **View details** (top right) → **On-demand execution**
3. Select the location `SG-Punggol` and click **Trigger now**

To see the result of the last run in JSON format:

1. Go to **Synthetic** and select the monitor
2. Click **Analyze data** → **Last successful**

### 2.3 Configure a browser monitor

> **Prerequisite:** [2.1 Configure the private location](#21-configure-the-private-location-prerequisite)

1. Go to **Synthetic** → **Create a synthetic monitor** → **Browser monitor**
2. Enter the monitor name and the URL to check
3. Add the conditions and a content validation rule, for example text that must appear on the page
4. Keep the defaults for the other settings and save the monitor

> Unlike an HTTP monitor, a browser monitor captures **screenshots** of the page. See [HTTP monitor vs browser-based monitor](02-synthetic-monitoring-concepts.md#11-http-monitor-vs-browser-based-monitor).

---

## 3. Dynatrace synthetic and RUM pricing

Synthetic monitoring and RUM are billed **pay as you go**: you pay for every HTTP request or browser action a monitor runs, and for every real user session. The UI doesn't show the calculation, but you can see how much you've used:

**Account Management** (bottom left) → **Subscription**

Official rate card:

| Capability | Billed per | Price | Price per unit |
|------------|-----------|-------|----------------|
| HTTP monitor | Synthetic request | 1 USD per 1,000 requests | 0.001 USD |
| Browser monitor | Synthetic action | 4.5 USD per 1,000 actions | 0.0045 USD |
| Real User Monitoring (RUM) | Session | 2.25 USD per 1,000 sessions | 0.00225 USD |
| RUM with Session Replay | Session | 4.5 USD per 1,000 sessions | 0.0045 USD |

Source: [Dynatrace pricing](https://www.dynatrace.com/pricing/)

Cost formula:

```text
cost = URLs × locations × executions per hour × hours × price per request (or action)
```

### 3.1 HTTP monitor

Example (`UC_01`): 1 URL, 4 locations, runs every 5 minutes (12 times an hour), at **0.001 USD per request**.

| Period | Requests | Cost (USD) |
|--------|----------|------------|
| 1 hour | 1 × 4 × 12 = **48** | 48 × 0.001 = **0.048** |
| 1 day | 48 × 24 = **1,152** | **1.15** |
| 1 year | 1,152 × 365 = **420,480** | **420.48** |

> Cost grows linearly with each factor. Running every 15 minutes instead of 5, or from 2 locations instead of 4, cuts the cost to a third or a half.

### 3.2 Browser monitor

Same example (`UC_01`) as a single-URL browser monitor (1 page load = 1 action per run), at **0.0045 USD per action**.

| Period | Actions | Cost (USD) |
|--------|---------|------------|
| 1 hour | 1 × 4 × 12 = **48** | 48 × 0.0045 = **0.216** |
| 1 day | 48 × 24 = **1,152** | **5.18** |
| 1 year | 1,152 × 365 = **420,480** | **1,892.16** |

> A clickpath monitor is billed for **every action** in the recorded journey. A 5-step clickpath costs 5× the single-URL example: about **9,460.80 USD per year**.

---

## References

- [Manage private Synthetic locations – Browser autoupdate](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/private-synthetic-locations/manage-private-synthetic-locations#:~:text=Data%20Explorer%20Classic-,Browser%20autoupdate,-You%20can%20Enable)
- [Configure HTTP monitors in Classic](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/http-monitors-classic/configure-http-monitors-classic)
- [Dynatrace pricing](https://www.dynatrace.com/pricing/)
