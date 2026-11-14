# Configure Synthetic Monitoring

For the concepts (monitor types, locations, authentication), see [03. Synthetic_monitoring.md](03.%20Synthetic_monitoring.md).

## Contents

1. [Configure a private Synthetic location](#1-configure-a-private-synthetic-location)

---

## 1. Configure a private Synthetic location

A private location runs synthetic monitors from inside your own network, on a Synthetic-enabled Environment ActiveGate.

- [ ] **1. Create a VM for the ActiveGate**

  | Setting | Value |
  |---------|-------|
  | Instance type | `t2.medium` (2 vCPU, 4 GB RAM) |
  | Storage | 60 GB |
  | Image | Amazon Linux |

