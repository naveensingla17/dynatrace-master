[root@ip-172-31-22-26 oneagent]# pwd
/root/oneagent
[root@ip-172-31-22-26 oneagent]# ls
[root@ip-172-31-22-26 oneagent]# wget -O Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh "https://nes80740.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/version/1.349.109.20261102-164952?arch=x86&networkZone=petclinic" --header="Authorization: Bearer <YOUR_DYNATRACE_PLATFORM_TOKEN>"
--2026-11-11 11:01:48--  https://nes80740.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/version/1.349.109.20261102-164952?arch=x86&networkZone=petclinic
Resolving nes80740.live.dynatrace.com (nes80740.live.dynatrace.com)... 13.228.171.241, 52.220.154.47, 18.139.251.69
Connecting to nes80740.live.dynatrace.com (nes80740.live.dynatrace.com)|13.228.171.241|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 282410688 (269M) [application/octet-stream]
Saving to: ‘Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh’

Dynatrace-OneAgent-Linux-petcli 100%[======================================================>] 269.33M   115MB/s    in 2.3s    

2026-11-11 11:01:52 (115 MB/s) - ‘Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh’ saved [282410688/282410688]

[root@ip-172-31-22-26 oneagent]# wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null
--2026-11-11 11:02:54--  https://ca.dynatrace.com/dt-root.cert.pem
Resolving ca.dynatrace.com (ca.dynatrace.com)... 143.204.226.20, 143.204.226.56, 143.204.226.11, ...
Connecting to ca.dynatrace.com (ca.dynatrace.com)|143.204.226.20|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4340 (4.2K) [binary/octet-stream]
Saving to: ‘dt-root.cert.pem’

dt-root.cert.pem                100%[======================================================>]   4.24K  --.-KB/s    in 0s      

2026-11-11 11:02:55 (162 MB/s) - ‘dt-root.cert.pem’ saved [4340/4340]

CMS Verification successful
[root@ip-172-31-22-26 oneagent]# /bin/sh Dynatrace-OneAgent-Linux-petclinic-x86-1.349.109.20261102-164952.sh --set-monitoring-mode=fullstack --set-app-log-content-access=true --set-network-zone=petclinic --set-host-name=petclinic1 --set-host-property=env=demo
11:03:09 Checking root privileges...
11:03:09 Process real user: root, real ID: 0
11:03:09 Process effective user: root, effective ID: 0
11:03:09 Process root access: true
11:03:10 Logging to /var/log/dynatrace/oneagent/installer/installation_33558.log
11:03:10 Installation started, version 1.349.109.20261102-164952, build date: 02.11.2026, PID 33558.
11:03:10 Detected platform: LINUX arch: X86_64
11:03:10 Checking /opt/dynatrace/oneagent for prohibited mount flags
11:03:10 Checking free space in /opt/dynatrace/oneagent
11:03:10 Extracting...
11:03:10 Unpacking. This may take a while...
11:03:24 Unpacking complete.
11:03:24 Checking if an operating system is supported...
11:03:24 The operating system is supported, continuing installation.
11:03:24 Moving new binaries into lib folders...
11:03:25 User 'dtuser' added successfully.
11:03:30 Non-privileged mode is enabled.
11:03:30 Checking free space in /lib64
11:03:30 Storing SELinux policy sources in /opt/dynatrace/oneagent/agent.
11:03:32 Installing SELinux dynatrace_oneagent module, this may take a while...
11:03:41 dynatrace_oneagent module was successfully installed
11:03:41 SELinux dynatrace_oneagent module installation finished.
11:03:41 Verifying auto-injection compatibility...
11:03:41 Auto-injection compatibility check result: OK
11:03:41 Refreshing dynamic linker runtime bindings using ldconfig
11:03:41 Updating /proc/sys/kernel/core_pattern with oneagentdumpproc
11:03:50 Starting agents...
11:03:50 oneagent service started
11:03:50 Checking if agent is connected to the server...
11:03:51 Dynatrace OneAgent has successfully connected to Dynatrace Cluster Node. You are all set. The Dynatrace OneAgent is fully operational.
[root@ip-172-31-22-26 oneagent]# 
