# Lab 2 – Configure Synthetic Monitoring

Set up a **private Synthetic location** on a Synthetic-enabled ActiveGate, then create an **HTTP monitor** (including a `POST` API check), a single-URL **browser monitor** and a **clickpath** monitor that check easyTravel from it. Finally, estimate what the monitors cost.

For the concepts (monitor types, locations, authentication), see [02-synthetic-monitoring-concepts.md](02-synthetic-monitoring-concepts.md).

## Checklist

1. [Install a Synthetic-enabled ActiveGate](#1-install-a-synthetic-enabled-activegate)
   - [x] [1.1 Create a VM](#11-create-a-vm)
   - [x] [1.2 In the Dynatrace console](#12-in-the-dynatrace-console)
   - [x] [1.3 On the ActiveGate server](#13-on-the-activegate-server)
2. [Configure the private location](#2-configure-the-private-location)
   - [x] [2.1 Create the private location](#21-create-the-private-location)
   - [x] [2.2 Why leave Chromium auto-update off](#22-why-leave-chromium-auto-update-off)
3. [Configure an HTTP monitor](#3-configure-an-http-monitor)
   - [x] [3.1 Check that the app is reachable](#31-check-that-the-app-is-reachable)
   - [x] [3.2 Create the monitor](#32-create-the-monitor)
   - [x] [3.3 Test the monitor](#33-test-the-monitor)
   - [x] [3.4 Monitor a POST API](#34-monitor-a-post-api)
4. [Configure a browser monitor](#4-configure-a-browser-monitor)
   - [x] [4.1 Single-URL browser monitor](#41-single-url-browser-monitor)
   - [x] [4.2 Clickpath with the Synthetic Recorder](#42-clickpath-with-the-synthetic-recorder)
   - [x] [4.3 Browser monitor settings](#43-browser-monitor-settings)
5. [Synthetic and RUM pricing](#5-synthetic-and-rum-pricing)
   - [x] [5.1 Rate card](#51-rate-card)
   - [x] [5.2 HTTP monitor cost](#52-http-monitor-cost)
   - [x] [5.3 Browser monitor cost](#53-browser-monitor-cost)
6. [Cleanup](#6-cleanup)
7. [References](#references)

---

## 1. Install a Synthetic-enabled ActiveGate

A private location runs synthetic monitors from inside your own network, on a Synthetic-enabled Environment ActiveGate.

### 1.1 Create a VM

| # | Setting | Value |
|---|---------|-------|
| 1 | Instance type | `t2.medium` (2 vCPU, 4 GB RAM) |
| 2 | Storage | 60 GB |
| 3 | Image | Amazon Linux |

### 1.2 In the Dynatrace console

1. Go to **Discovery & Coverage**
2. Click **Install** (top right)
3. Select **Install ActiveGate**
4. Select **Synthetic** as the ActiveGate purpose
5. Select the Network Zone `petclinic`
6. Create an ActiveGate group named `pvt01`

### 1.3 On the ActiveGate server

1. Create `connection.properties` with the values shown in the console. Never paste real tokens into this file:

   ```properties
   SERVER=https://nes80740.live.dynatrace.com/communication
   TENANT=nes80740
   TENANT_TOKEN=<YOUR_TENANT_TOKEN>
   ACTIVEGATE_AUTH_TOKEN=<YOUR_ACTIVEGATE_AUTH_TOKEN>
   ```

2. Download the installer:

   ```bash
   wget -O Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh \
     "https://nes80740.apps.dynatrace.com/platform/fleet-management/v1/activegate/installer/unix/amd64/1.349.80.20261105-202347:download" \
     --header="Authorization: Bearer <YOUR_DYNATRACE_PLATFORM_TOKEN>" \
     --header="accept: application/octet-stream"
   ```

3. Verify that the script is authentic:

   ```bash
   wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null
   ```

4. Run the installer with Synthetic enabled:

   ```bash
   /bin/bash Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh \
     --connection-properties=connection.properties \
     --enable-synthetic \
     --set-network-zone=petclinic \
     --set-group=pvt01
   ```

5. Check the status, then delete `connection.properties`:

   ```bash
   systemctl status dynatracegateway.service
   shred -u connection.properties
   ```

---

## 2. Configure the private location

> **Prerequisite:** Create the private location before you configure any synthetic monitor that runs from it.

### 2.1 Create the private location

1. Go to **Synthetic** and click **New private location** (top right)
2. Select **Classic**
3. Fill in the details:

   | # | Field | Value |
   |---|-------|-------|
   | 1 | Location name | `SG-Punggol` |
   | 2 | Geographic location | Singapore, Singapore |
   | 3 | ActiveGate | The Synthetic-enabled ActiveGate in group `pvt01` |
   | 4 | Chromium auto-update | **Leave unselected** |
   | 5 | Location outage handling | Select the options to be notified when the location has an outage |

4. Click **Save**

### 2.2 Why leave Chromium auto-update off

The private location uses Chromium, the open-source browser behind Chrome, to run browser monitors. With auto-update on, Dynatrace updates it whenever a new version is released.

Keep it off because:

1. **A failed update can break the location.** If an update fails, for example because of a network issue, synthetic monitoring on the location can stop working and you have to re-create it.
2. **Updates run without notice.** This causes unplanned monitoring downtime and unnecessary downtime alerts.

Instead, update Chromium manually in a planned maintenance window.

---

## 3. Configure an HTTP monitor

> **Prerequisite:** [2. Configure the private location](#2-configure-the-private-location)

### 3.1 Check that the app is reachable

The monitor runs on the Synthetic ActiveGate, so run this on the ActiveGate server:

```bash
curl -I http://<EC2_PUBLIC_IP>:32770/
```

You should get `HTTP/1.1 200 OK`. If the request times out, allow the port in the easyTravel VM's security group.

### 3.2 Create the monitor

1. Go to **Synthetic → Create a synthetic monitor → HTTP monitor**
2. Enter the monitor name and tags, and select the existing frontend
3. Add the HTTP request:

   | # | Setting | Value |
   |---|---------|-------|
   | 1 | Request name | A name for the request |
   | 2 | URL | The URL to check, for example the easyTravel URL |
   | 3 | Authentication | Only if the URL needs a login |
   | 4 | SSL | Certificate options, for example accept any SSL certificate |
   | 5 | Response status code verification | Default: **Fail** on `4xx` and `5xx` |

4. Set the frequency to **5 minutes** and select the private location `SG-Punggol`
5. Choose the outage alert options and save the monitor

> For the status code rules you can enter, see [Response status code verification](02-synthetic-monitoring-concepts.md#113-response-status-code-verification). For what each code means, see [HTTP status codes](02-synthetic-monitoring-concepts.md#114-http-status-codes).

### 3.3 Test the monitor

Run the monitor on demand instead of waiting for the next scheduled run:

1. Go to **Synthetic** and select the monitor
2. Click **View details** (top right) → **On-demand execution**
3. Select the location `SG-Punggol` and click **Trigger now**

To see the result of the last run in JSON format:

1. Go to **Synthetic** and select the monitor
2. Click **Analyze data → Last successful**

### 3.4 Monitor a POST API

An HTTP monitor can also call an API with a request body. This example sends a `POST` to the free [JSONPlaceholder](https://jsonplaceholder.typicode.com/) test API.

> **Test the API first.** Run the request in Postman or with `curl` before you create the monitor, so you don't pay for failed monitor runs while you fix the request:
>
> ```bash
> curl -i -X POST https://jsonplaceholder.typicode.com/posts \
>   -H "Content-Type: application/json" \
>   -d '{"title": "Dynatrace Synthetic Training", "body": "Testing POST API using Dynatrace", "userId": 101}'
> ```
>
> You should get `HTTP/1.1 201 Created`.

1. Go to **Synthetic → Create a synthetic monitor → HTTP monitor**
2. Add the HTTP request:

   | # | Setting | Value |
   |---|---------|-------|
   | 1 | URL | `https://jsonplaceholder.typicode.com/posts` |
   | 2 | HTTP method | `POST` |
   | 3 | Additional HTTP headers | Header name `Content-Type`, header value `application/json` |
   | 4 | Request body | The JSON below |

   ```json
   {
     "title": "Dynatrace Synthetic Training",
     "body": "Testing POST API using Dynatrace",
     "userId": 101
   }
   ```

3. Click **Continue** and save the monitor
4. Trigger it on demand (see [3.3 Test the monitor](#33-test-the-monitor)). A successful run returns `201 Created`.

---

## 4. Configure a browser monitor

> **Prerequisite:** [2. Configure the private location](#2-configure-the-private-location)

Unlike an HTTP monitor, a browser monitor captures **screenshots** of the page. It runs either a **single URL** (one page load) or a **clickpath** (a recorded user journey). See [Monitor types](02-synthetic-monitoring-concepts.md#1-monitor-types).

### 4.1 Single-URL browser monitor

1. Go to **Synthetic → Create a synthetic monitor → Browser monitor**
2. Enter the monitor name and the URL to check
3. Add the conditions and a content validation rule, for example text that must appear on the page
4. Select the private location `SG-Punggol`
5. Keep the defaults for the other settings and save the monitor

### 4.2 Clickpath with the Synthetic Recorder

The **Synthetic Recorder** is a Chrome extension that records the transactions you perform in the app and turns them into a clickpath. You don't have to write any script.

1. Go to **Synthetic → Create a synthetic monitor → Browser monitor**
2. Enter the application name and the URL
3. Click **Install Synthetic Recorder** and add the extension to Chrome
4. Click **Record clickpath**. The URL opens in a private browser window.
5. Perform the transactions you want to monitor, for example search → book → pay
6. Test the clickpath. If a step isn't right, correct it or record it again.
7. Continue, keep the default values, and set the frequency to **5 minutes**
8. Save the monitor, then trigger it on demand to check that it works (see [3.3 Test the monitor](#33-test-the-monitor))

> A clickpath is billed for **every action** in the recording, so keep it to the steps you need. See [5.3 Browser monitor cost](#53-browser-monitor-cost).

### 4.3 Browser monitor settings

| # | Setting | What it does |
|---|---------|--------------|
| 1 | **Credentials** | Store login details for the monitor in the Dynatrace **credential vault**, or sync them from an external vault: Azure Key Vault, HashiCorp Vault or CyberArk Vault |
| 2 | **Element locators** | How a clickpath event finds the element it clicks or types into: a **DOM** locator (document object model) or a **CSS selector** |

---

## 5. Synthetic and RUM pricing

Synthetic monitoring and RUM are billed **pay as you go**: you pay for every HTTP request or browser action a monitor runs, and for every real user session. The UI doesn't show the calculation, but you can see your usage in **Account Management** (bottom left) **→ Subscription**.

### 5.1 Rate card

| # | Capability | Billed per | Price | Price per unit |
|---|------------|------------|-------|----------------|
| 1 | HTTP monitor | Synthetic request | 1 USD per 1,000 requests | 0.001 USD |
| 2 | Browser monitor | Synthetic action | 4.5 USD per 1,000 actions | 0.0045 USD |
| 3 | Real User Monitoring (RUM) | Session | 2.25 USD per 1,000 sessions | 0.00225 USD |
| 4 | RUM with Session Replay | Session | 4.5 USD per 1,000 sessions | 0.0045 USD |

Source: [Dynatrace pricing](https://www.dynatrace.com/pricing/)

Cost formula:

```text
cost = URLs × locations × executions per hour × hours × price per request (or action)
```

### 5.2 HTTP monitor cost

Example (`UC_01`): 1 URL, 4 locations, runs every 5 minutes (12 times an hour), at **0.001 USD per request**.

| Period | Requests | Cost (USD) |
|--------|----------|------------|
| 1 hour | 1 × 4 × 12 = **48** | 48 × 0.001 = **0.048** |
| 1 day | 48 × 24 = **1,152** | **1.15** |
| 1 year | 1,152 × 365 = **420,480** | **420.48** |

> Cost grows linearly with each factor. Running every 15 minutes instead of 5 cuts the cost to a third; running from 2 locations instead of 4 halves it.

### 5.3 Browser monitor cost

The same example (`UC_01`) as a single-URL browser monitor (1 page load = 1 action per run), at **0.0045 USD per action**.

| Period | Actions | Cost (USD) |
|--------|---------|------------|
| 1 hour | 1 × 4 × 12 = **48** | 48 × 0.0045 = **0.216** |
| 1 day | 48 × 24 = **1,152** | **5.18** |
| 1 year | 1,152 × 365 = **420,480** | **1,892.16** |

> A clickpath monitor is billed for **every action** in the recorded journey. A 5-step clickpath costs 5× the single-URL example: about **9,460.80 USD per year**.

---

## 6. Cleanup

When you finish the lab, remove the synthetic setup so it stops running and billing:

1. Delete the HTTP, browser and clickpath monitors in **Synthetic**
2. Delete the private location `SG-Punggol`
3. Uninstall the ActiveGate on the ActiveGate server:

   ```bash
   /opt/dynatrace/gateway/uninstall.sh
   ```

4. Terminate the ActiveGate EC2 instance in AWS

---

## References

- [Create a private Synthetic location](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/private-synthetic-locations/create-a-private-synthetic-location)
- [Manage private Synthetic locations – Browser autoupdate](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/private-synthetic-locations/manage-private-synthetic-locations#:~:text=Data%20Explorer%20Classic-,Browser%20autoupdate,-You%20can%20Enable)
- [Configure HTTP monitors in Classic](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/http-monitors-classic/configure-http-monitors-classic)
- [Dynatrace pricing](https://www.dynatrace.com/pricing/)
