# Dynatrace Interview Questions

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
