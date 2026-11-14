# Synthetic Monitoring

## What it is

Synthetic monitoring is **proactive** monitoring. Dynatrace runs scripted checks against your applications and APIs from chosen locations, at a set frequency, even when no real users are active. It checks two things:

| Check | Question it answers | Example |
|-------|--------------------|---------|
| **Availability** | Is the app or API up? | The request returns `200 OK` |
| **Performance** | How long does it take to load or respond? | The page loads in 2.3 s |

> Real User Monitoring (RUM) shows what real users experienced. Synthetic monitoring tests the app on a schedule, so you can find problems before users do.

## Contents

This document has two main parts.

1. [Monitor types](#1-monitor-types)
   - 1.1 [HTTP monitor vs browser-based monitor](#11-http-monitor-vs-browser-based-monitor)
   - 1.2 [Network availability monitor](#12-network-availability-monitor)
   - 1.3 [Application types](#13-application-types)
   - 1.4 [Locations](#14-locations)
   - 1.5 [API monitoring](#15-api-monitoring)
     - 1.5.1 [HTTP methods](#151-http-methods)
     - 1.5.2 [Dynatrace APIs](#152-dynatrace-apis)
     - 1.5.3 [Response status code verification](#153-response-status-code-verification)
     - 1.5.4 [HTTP status codes](#154-http-status-codes)
2. [Authentication](#2-authentication)
   - 2.1 [Requirements](#21-requirements)
   - 2.2 [Limitations](#22-limitations)
   - 2.3 [Supported authentication types](#23-supported-authentication-types)
     - 2.3.1 [Kerberos flow](#231-kerberos-flow)
     - 2.3.2 [Kerberos in Dynatrace synthetic monitors](#232-kerberos-in-dynatrace-synthetic-monitors)

---

## 1. Monitor types

Dynatrace synthetic monitoring has three monitor types: **HTTP**, **browser-based** and **network availability**.

### 1.1 HTTP monitor vs browser-based monitor

| # | HTTP monitor | Browser-based monitor |
|---|--------------|-----------------------|
| 1 | Sends HTTP requests to a URL or API | Loads the app in a real browser |
| 2 | Checks status code, response time and content | Single URL (one page) or clickpath (recorded user journey, e.g. search → book → pay) |
| 3 | Output: response details, often in JSON | Output: screenshots of the page, plus load timings |
| 4 | Best for APIs and endpoint availability | Best for page load performance and user journeys |

> **Notes**
>
> - Browser-based monitoring is also known as multi-API monitoring.
> - Browser-based monitors use JavaScript, but you don't have to write it. The **Synthetic Recorder** records the transaction for you.

### 1.2 Network availability monitor

Checks basic network reachability of a host:

| # | Check | What it tests |
|---|-------|---------------|
| 1 | **ICMP / Ping** | Whether the host is reachable |
| 2 | **DNS** | Whether the domain name resolves |
| 3 | **TCP / port** | Whether a specific port on the host is open |

### 1.3 Application types

1. **Internal applications:** Reachable only inside your network
2. **Public-facing applications:** Reachable from the internet

### 1.4 Locations

| # | Public location | Private location |
|---|-----------------|------------------|
| 1 | Set up by Dynatrace | Set up by the user, i.e. an Environment ActiveGate |
| 2 | Runs from Dynatrace locations around the world | Runs on your own Synthetic-enabled ActiveGate |
| 3 | Tests public-facing apps (reachable from the internet) | Tests internal apps (reachable only inside your network) |

### 1.5 API monitoring

An HTTP monitor can call APIs with authentication and any HTTP method.

#### 1.5.1 HTTP methods

| # | Method | Purpose |
|---|--------|---------|
| 1 | `GET` | Fetch data |
| 2 | `POST` | Insert (create) data |
| 3 | `PUT` | Replace data |
| 4 | `PATCH` | Partially update data |
| 5 | `DELETE` | Delete data |
| 6 | `HEAD` | Retrieve only the response headers |
| 7 | `OPTIONS` | Show the communication options (allowed methods) for a URL |

#### 1.5.2 Dynatrace APIs

Dynatrace also provides its own APIs. You can browse and try them in the Swagger UI of your tenant:

<https://nes80740.apps.dynatrace.com/platform/swagger-ui/index.html>

#### 1.5.3 Response status code verification

In an HTTP monitor, **Response status code verification** decides whether the monitor passes or fails based on the HTTP status code it gets back.

By default, the monitor **fails** on any `4xx` or `5xx` response and **passes** on `1xx`–`3xx`. You can switch it to **Pass** and enter your own codes:

| Format | Example |
|--------|---------|
| Exact code | `404` |
| Comparison (`<`, `<=`, `>`, `>=`, `!=`) | `>=500` |
| Range (minus sign, no spaces) | `406-410` |
| Whole class | `3xx` |
| Several values (comma-separated) | `3xx, 404, 406-410, >=500` |

> **Follow redirects** is on by default (up to 10 redirects). Turn it off to check the status code of the redirect itself, for example `301`.

#### 1.5.4 HTTP status codes

| Class | Meaning |
|-------|---------|
| `1xx` | Informational |
| `2xx` | Success |
| `3xx` | Redirection |
| `4xx` | Client error |
| `5xx` | Server error |

Common codes:

| Code | Name | Meaning |
|------|------|---------|
| `100` | Continue | Interim response; the client should continue the request |
| `101` | Switching Protocols | The server switches to the protocol the client asked for |
| `200` | OK | The request succeeded |
| `201` | Created | The request succeeded and a new resource was created (usually `POST`) |
| `202` | Accepted | The request was received but not yet acted on |
| `204` | No Content | Success, with no response body |
| `301` | Moved Permanently | The URL changed permanently; the new URL is in the response |
| `302` | Found | The URL changed temporarily; keep using the original URL |
| `304` | Not Modified | The cached version can still be used |
| `307` | Temporary Redirect | Like `302`, but the HTTP method must not change |
| `308` | Permanent Redirect | Like `301`, but the HTTP method must not change |
| `400` | Bad Request | Invalid request, for example malformed syntax |
| `401` | Unauthorized | Authentication issue: the client must log in |
| `403` | Forbidden | The client is known but has no access |
| `404` | Not Found | The server cannot find the resource |
| `405` | Method Not Allowed | The resource doesn't support this HTTP method |
| `408` | Request Timeout | The server closed an idle connection |
| `409` | Conflict | The request conflicts with the server's current state |
| `429` | Too Many Requests | Rate limit reached |
| `500` | Internal Server Error | The server hit an error it can't handle |
| `501` | Not Implemented | The server doesn't support this HTTP method |
| `502` | Bad Gateway | A gateway or proxy got an invalid response from the upstream server |
| `503` | Service Unavailable | The server is down for maintenance or overloaded (temporary) |
| `504` | Gateway Timeout | A gateway or proxy got no response from the upstream server in time |

---

## 2. Authentication

### 2.1 Requirements

To monitor an application that needs a login:

1. **Create a service account:** A dedicated login for the synthetic monitor to use
2. **Set the password expiry:** Use a password that never expires, or one that expires after one year, so the monitor doesn't start failing when the password expires

### 2.2 Limitations

> **MFA / SSO:** If the application login uses MFA or SSO, none of the synthetic monitor types supports it.

### 2.3 Supported authentication types

Dynatrace synthetic monitors support these authentication types. They are three **separate** types; NTLM and Kerberos are not part of Basic.

| # | Type | How it works | Password sent to the app? |
|---|------|--------------|---------------------------|
| 1 | **Basic** | Username and password are sent with every request in the HTTP header (Base64-encoded, not encrypted, so use HTTPS) | Yes |
| 2 | **NTLM** | NT LAN Manager. Windows challenge-response authentication: the server sends a challenge and the client answers with a value computed from the password hash. Used on LANs | No |
| 3 | **Kerberos** | Ticket-based. Tickets are issued by the domain controller (KDC), mainly in Windows Active Directory domains | No |

> **Why they can look the same:** In Dynatrace, all three take a username and password from the **credential vault**. The difference is how the monitor uses those credentials to prove who it is.
>
> HTTP monitors also support **OAuth 2.0** and **certificate-based** authentication.

#### 2.3.1 Kerberos flow

Kerberos involves three parties: the **client** (the user, or the synthetic monitor), the **target service** (the application), and the **Key Distribution Center (KDC)**. The KDC runs on every domain controller and contains two services: the **Authentication Service (AS)** and the **Ticket-Granting Service (TGS)**.

**Basic flow:**

```text
User request ──▶ Authentication Service (AS) ──▶ Ticket-Granting Service (TGS) ──▶ Target service
                 returns a TGT                    returns a service ticket          grants access
```

**Detailed flow:**

```text
  Client                         KDC (on the domain controller)                Target service
  (user / synthetic          ┌───────────────────────────────────────┐         (application)
   monitor on ActiveGate)    │  Authentication     Ticket-Granting   │
     │                       │  Service (AS)       Service (TGS)     │              │
     │                       └──────┬───────────────────┬────────────┘              │
     │                              │                   │                           │
     │ 1. AS-REQ: "I am user X"     │                   │                           │
     │ ────────────────────────────▶│                   │                           │
     │ 1. AS-REP: TGT               │                   │                           │
     │ ◀────────────────────────────│                   │                           │
     │    (TGT is cached and reused until it expires)   │                           │
     │                                                  │                           │
     │ 2. TGS-REQ: TGT + "I want the app"               │                           │
     │ ────────────────────────────────────────────────▶│                           │
     │ 2. TGS-REP: service ticket                       │                           │
     │ ◀────────────────────────────────────────────────│                           │
     │                                                                              │
     │ 3. AP-REQ: service ticket (no password is sent)                              │
     │ ────────────────────────────────────────────────────────────────────────────▶│
     │ 3. AP-REP: access granted                                                    │
     │ ◀────────────────────────────────────────────────────────────────────────────│
```

**In short:** Sign in once → get a TGT → swap the TGT for a service ticket → show the service ticket to the app.

| # | Step | Exchange | What happens |
|---|------|----------|--------------|
| 1 | **Authenticate** | AS-REQ / AS-REP | The client signs in to the KDC's Authentication Service and gets a **Ticket-Granting Ticket (TGT)**. The client caches the TGT and reuses it until it expires. |
| 2 | **Get a service ticket** | TGS-REQ / TGS-REP | The client presents the TGT to the Ticket-Granting Service and asks for a **service ticket** for the specific application. |
| 3 | **Access the service** | AP-REQ / AP-REP | The client presents the service ticket directly to the target service, which grants access. The password is never sent to the service. |

#### 2.3.2 Kerberos in Dynatrace synthetic monitors

| # | Item | Details |
|---|------|---------|
| 1 | **Where it runs** | Private locations only (a Synthetic-enabled ActiveGate: Windows, or Linux / containerized ActiveGate version 1.311+). Not available in public locations. |
| 2 | **Browser monitors** | Single URL: **Enable global login authentication → Kerberos**. Clickpath: **Navigate event → Enable HTTP authentication → Kerberos**. Enter the **Domain** and an **Auth server allow list** (wildcards are allowed). |
| 3 | **HTTP monitors** | **Set authentication/authorization → Kerberos**. Enter the **realm name** and the **KDC IP**. |
| 4 | **Credentials** | Stored in the **credential vault**. Username format: `<username>` or `<domain>\<username>` for browser monitors, `<username>` for HTTP monitors. |
| 5 | **ActiveGate setup** | *Linux:* Install the Kerberos client (`dnf install krb5-workstation krb5-libs`) and configure `/etc/krb5.conf`. The synthetic engine gets tickets by running `kinit`. *Windows:* The host must be configured with Active Directory. If authentication fails, register the KDC with `ksetup /addkdc <DOMAIN> <kdc-address>`. |

References:

- [Kerberos authentication overview (Microsoft)](https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview)
- [Synthetic monitor authentication (Dynatrace)](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/general-information/synthetic-authentication)
- [Create a private Synthetic location (Dynatrace)](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/private-synthetic-locations/create-a-private-synthetic-location)
- [Configure HTTP monitors in Classic (Dynatrace)](https://docs.dynatrace.com/docs/observe/digital-experience-classic/synthetic-monitoring-classic/http-monitors-classic/configure-http-monitors-classic)
- [HTTP response status codes (MDN)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)
