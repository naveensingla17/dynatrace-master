# Update the Host Group
## 1. Via oneagentctl command
cd /opt/dynatrace/oneagent/agent/tools/
systemctl stop oneagent.service
./oneagentctl --set-host-group=petclinic01
systemctl start oneagent.service
systemctl status oneagent.service

## 2. Workflow