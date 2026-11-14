# Dynatrace Interview Questions

## Contents

| # | Topic |
|---|-------|
| [Q1](#q1-can-we-update-the-host-group-via-the-ui-in-gen3) | Updating the host group via the UI in Gen3 |
| [Q2](#q2-how-can-we-update-the-host-group-after-oneagent-installation) | Updating the host group after OneAgent installation |
| [Q3](#q3-can-we-modify-the-user-experience-score-thresholds-how) | Modifying the User Experience Score |
| [Q4](#q4-what-is-throughput) | Throughput |
| [Q5](#q5-what-is-the-difference-between-distributed-tracing-and-backtrace) | Distributed tracing vs backtrace |
| [Q6](#q6-what-are-request-outliers-and-how-do-you-analyze-them) | Request outliers |
| [Q7](#q7-what-can-you-do-with-distributed-tracing-multidimensional-analysis-in-classic) | Distributed tracing (multidimensional analysis) |
| [Q8](#q8-what-are-conversion-goals-and-how-do-you-create-one) | Conversion goals |
| [Q9](#q9-how-do-you-configure-a-custom-error-code) | Custom error codes |
| [Q10](#q10-how-can-you-check-that-the-rum-javascript-is-injected-into-the-application) | Checking RUM injection |
| [Q11](#q11-there-is-no-application-data-in-experience-vitals-and-no-traces-how-do-you-troubleshoot) | Troubleshooting missing RUM data and traces |
| [Q12](#q12-how-can-you-update-the-application-url-of-a-frontend) | Updating a frontend's application URL |
| [Q13](#q13-how-do-you-enable-or-disable-rum-monitoring) | Enabling / disabling RUM |
| [Q14](#q14-do-we-need-to-restart-the-application-after-adding-its-url-to-a-frontend) | Restart after adding a frontend URL |

---

## Q1. Can we update the host group via the UI in Gen3?

**Answer:** No.

---

## Q2. How can we update the host group after OneAgent installation?

**Answer:**

### Method 1: Using the `oneagentctl` command

```bash
cd /opt/dynatrace/oneagent/agent/tools/

systemctl stop oneagent.service
./oneagentctl --set-host-group=petclinic01
systemctl start oneagent.service

systemctl status oneagent.service
```

---

## Q3. Can we modify the User Experience Score thresholds? How?

**Answer:** Yes.

**How:** _TODO_

---

## Q4. What is throughput?

**Answer:** The number of requests per minute.

---

## Q5. What is the difference between distributed tracing and backtrace?

**Answer:**

| | Distributed tracing | Backtrace |
|---|---|---|
| **Direction** | Forward: follows a request from where it starts down through every service it calls | Backward: starts at a service or database query and walks up to the callers |
| **Question it answers** | "Where did this request go, and where was time spent?" | "Where did this call come from?" |

**Backtrace:**

From the **Database queries** section you can run a backtrace to find the parent services the request came from. It shows:

1. The request flow from top to bottom
2. How many requests failed
3. How many incoming and outgoing requests there were

---

## Q6. What are request outliers and how do you analyze them?

**Answer:** For each time frame, it shows how many requests came in and what commit was done. You can also see the **p50** and **p90** values.

**Path:** **Services → Tomcat → More actions → Analyze request outliers**

---

## Q7. What can you do with distributed tracing (multidimensional analysis in Classic)?

**Answer:**

1. Create a filter
2. Create an alert
3. _TODO_

---

## Q8. What are conversion goals and how do you create one?

**Answer:** Conversion goals are a versatile way of measuring how well your application fulfils your company's business objectives.

**Path:** **Settings → select the frontend application → Process and contextualize → Conversion goals (Classic)**

---

## Q9. How do you configure a custom error code?

**Path:** **Settings → Process and contextualize → Process Group → Custom monitoring rules**

---

## Q10. How can you check that the RUM JavaScript is injected into the application?

**Answer:** Open the application in the browser, open **Developer Tools (Inspect) → Sources**, and look for **`ruxitagent`**. If it is there, the RUM JavaScript has been injected by OneAgent.

---

## Q11. There is no application data in Experience Vitals and no traces. How do you troubleshoot?

**Answer:**

1. Check the application URL first: has anything changed?
2. _TODO_

---

## Q12. How can you update the application URL of a frontend?

**Path:** **Settings → Environment → Collect and capture → Real User Monitoring → Detection**, then edit the URL.

---

## Q13. How do you enable or disable RUM monitoring?

**Path:** **Settings → select the application (e.g. petclinic) → Collect and capture → Enablement and cost control**

---

## Q14. Do we need to restart the application after adding its URL to a frontend?

**Answer:** No.
