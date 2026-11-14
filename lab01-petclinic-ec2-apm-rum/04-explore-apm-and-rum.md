# Lab 1 – Explore APM and Real User Monitoring

Explore what Dynatrace shows for the PetClinic app after OneAgent is installed: services and database calls (APM), real user sessions (RUM / Experience Vitals), user types and conversion goals.

## Checklist

1. [APM – Explore services](#1-apm--explore-services)
   - [ ] [1.1 Service map](#11-service-map)
   - [ ] [1.2 Database queries and backtrace](#12-database-queries-and-backtrace)
2. [RUM – Set up Experience Vitals](#2-rum--set-up-experience-vitals)
   - [ ] [2.1 Disable default RUM](#21-disable-default-rum)
   - [ ] [2.2 Create a frontend](#22-create-a-frontend)
   - [ ] [2.3 Generate traffic and view a session replay](#23-generate-traffic-and-view-a-session-replay)
3. [RUM – Explore Experience Vitals](#3-rum--explore-experience-vitals)
   - [ ] [3.1 Overview and Explorer](#31-overview-and-explorer)
   - [ ] [3.2 User experience score](#32-user-experience-score)
   - [ ] [3.3 User sessions dashboard](#33-user-sessions-dashboard)
4. [User types](#4-user-types)
5. [Conversion goals](#5-conversion-goals)
6. [Custom error rules](#6-custom-error-rules)
7. [Concepts](#7-concepts)
   - [7.1 Core Web Vitals](#71-core-web-vitals)
   - [7.2 User experience categories](#72-user-experience-categories)
   - [7.3 Apdex rating](#73-apdex-rating)
8. [Quick navigation](#8-quick-navigation)
9. [References](#references)

---

## 1. APM – Explore services

**Search → Services → Tomcat**

### 1.1 Service map

Open the **Service map** to see the service flow: which services call each other, and which databases they use.

### 1.2 Database queries and backtrace

1. Open the **Database queries** tab
2. In a query cell, click **View backtrace** to see which service and request ran the query

> _TODO:_ Add more APM steps, e.g. **More actions → Analyze request outliers**.

---

## 2. RUM – Set up Experience Vitals

### 2.1 Disable default RUM

**Settings → Collect and capture → General monitoring settings → Monitoring overview**

### 2.2 Create a frontend

1. Copy the application URL: `http://<ec2-public-ip>:8080`
2. Go to **Experience Vitals → Create frontend**
3. Enter the names:

   | Field | Value |
   |-------|-------|
   | Display name | `petclinic` |
   | Name | `petclinic_webapp` |

4. Click **Create**

### 2.3 Generate traffic and view a session replay

1. Use the PetClinic website: add owners and pets, and browse a few pages
2. Open the session replay:

   **Experience Vitals → petclinic → Open with Users & Sessions →** click a PetClinic user session **→ Replay**

> If no sessions are shown, increase the time range.

---

## 3. RUM – Explore Experience Vitals

### 3.1 Overview and Explorer

| View | Path |
|------|------|
| Frontend | **Experience Vitals → Overview → Frontend** |
| Explorer | **Experience Vitals → Overview → Explorer** |

Try these:

1. Create a filter
2. Create an alert

### 3.2 User experience score

**Experience Vitals → petclinic → Open with Users & Sessions**

The **Experience Score Band** column shows the score for each session.

> ❓ **Open question:** Where is the user experience score configured?

### 3.3 User sessions dashboard

To see user sessions and behavior on a map:

**Dashboards → User Sessions Overview**

---

## 4. User types

**Experience Vitals → Explorer → Open with Users & Sessions**

| # | User type | Description |
|---|-----------|-------------|
| 1 | **Real user** | A real person using the app. Can be viewed with session replay. |
| 2 | **Synthetic user** | A Dynatrace synthetic monitor: a script that simulates user transactions |
| 3 | **Robot** | Automated traffic such as search engine crawlers and bots, detected by user agent or IP address. Kept separate so it doesn't affect real user metrics. |

---

## 5. Conversion goals

A conversion goal measures how well the app meets a business objective, e.g. a completed booking or a new owner added.

**Settings → select the frontend application → Process and contextualize → Conversion goals (Classic)**

> _TODO:_ Add the goal you create, e.g. **Destination URL** contains `/owners/new`.

---

## 6. Custom error rules

Custom error rules decide which JavaScript, request or custom errors Dynatrace counts as errors for the frontend.

> _TODO:_ Add the settings path and an example rule.

---

## 7. Concepts

### 7.1 Core Web Vitals

| Metric | Full name | What it measures |
|--------|-----------|------------------|
| **LCP** | Largest Contentful Paint | How long the page takes to load its main content |
| **CLS** | Cumulative Layout Shift | How much the page unexpectedly moves around |
| **INP** | Interaction to Next Paint | How long the page takes to respond to a user interaction |

### 7.2 User experience categories

| User type | Page load time |
|-----------|----------------|
| Satisfied | 0 – 3 sec |
| Tolerating | > 3 sec and < 12 sec |
| Frustrated | > 12 sec |

### 7.3 Apdex rating

Apdex is a score between **0 and 1**.

| Score | Rating |
|-------|--------|
| 0.00 – 0.50 | Unacceptable / Fail |
| 0.50 – 0.70 | Poor |
| 0.70 – 0.85 | Fair |
| 0.85 – 0.94 | Good |
| > 0.94 | Excellent |

**Formula:**

```text
Apdex = (Satisfied + Tolerating × 0.5) / Total users
```

**Example:** 100 users made transactions.

| User type | Count |
|-----------|-------|
| Satisfied | 70 |
| Tolerating | 20 |
| Frustrated | 10 |
| **Total** | **100** |

```text
Apdex = (70 + 20 × 0.5) / 100 = 0.80   → Fair
```

---

## 8. Quick navigation

| What | Path |
|------|------|
| Services | **Search → Services → Tomcat** |
| Session replay | **Experience Vitals → petclinic → Open with Users & Sessions → session → Replay** |
| User sessions dashboard | **Dashboards → User Sessions Overview** |
| Conversion goals | **Settings → frontend app → Process and contextualize → Conversion goals** |

---

## References

- [Manual instrumentation (Android, OneAgent SDK)](https://docs.dynatrace.com/docs/observe/digital-experience-classic/rum-classic/mobile-applications/instrument-android-app/instrumentation-via-oneagent-sdk/manual-instrumentation)
