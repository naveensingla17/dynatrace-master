# Dynatrace Knowledge Notes

## OneAgent monitoring modes

Docs: <https://docs.dynatrace.com/docs/platform/oneagent/monitoring-modes/monitoring-modes>

| # | Mode | What it monitors |
|---|------|------------------|
| 1 | **Discovery** | Basic host data: CPU, memory, disk, services, processes |
| 2 | **Infrastructure** | Infrastructure (host-level) monitoring |
| 3 | **Full-Stack** | End-to-end monitoring: infrastructure plus application |

## Prerequisites for OneAgent

### 1. Environment ActiveGate

What it is used for:

- **Load balancing:** consolidates and compresses OneAgent traffic before sending it to Dynatrace
- **Extension / plugin monitoring:** acts as an intermediary that runs external extension and plugin scripts
- **Database monitoring**
- **Synthetic monitoring:** monitors public and private URLs

### 2. Network Zone

Docs: <https://docs.dynatrace.com/docs/manage/network-zones-classic/network-zones-basic-info>

- A configuration setting, not a component you install
- Makes OneAgent send its traffic only to the relevant ActiveGate

## Host Group
