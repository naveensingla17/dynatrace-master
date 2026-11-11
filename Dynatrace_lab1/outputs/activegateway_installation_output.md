[root@ip-172-31-27-104 activegateway]# /bin/bash Dynatrace-ActiveGate-Linux-x86-1.349.80.20261105-202347-petclinic.sh --connection-properties=connection.properties --set-network-zone=petclinic --set-group=ag01
2026-11-11 08:33:38 UTC Dynatrace ActiveGate installer version 1.349.80.20261105-202347, architecture x86_64, build date 05.11.2026
2026-11-11 08:33:38 UTC Log initialized in /tmp/dynatracegateway-install_20261111-083338_3580.log
Bash path variable: /bin/bash
Bash version variable: 5.2.15(1)-release
Found GNU bash in /proc/self/exe
2026-11-11 08:33:38 UTC Checking architecture...
2026-11-11 08:33:38 UTC Architecture OK
2026-11-11 08:33:38 UTC Extracting parameters from lines 259 - 262...
2026-11-11 08:33:39 UTC After parameter extraction from the PARAMETERS section:
2026-11-11 08:33:39 UTC PARAM_SERVER_VALUE=()
2026-11-11 08:33:39 UTC PARAM_DEPLOYMENT_TIER_VALUE=(PROD)
2026-11-11 08:33:39 UTC PARAM_TENANT_VALUE=()
2026-11-11 08:33:39 UTC PARAM_PROXY_VALUE=()
2026-11-11 08:33:39 UTC PARAM_PUBLIC_SG=(UNSET)
2026-11-11 08:33:39 UTC PARAM_NETWORK_ZONE=()
2026-11-11 08:33:39 UTC Processing command line parameter param (--connection-properties=connection.properties)
2026-11-11 08:33:39 UTC Processing command line parameter param (--set-network-zone=petclinic)
2026-11-11 08:33:39 UTC Processing command line parameter param (--set-group=ag01)
2026-11-11 08:33:39 UTC Dynatrace ActiveGate installer version 1.349.80.20261105-202347, build date 05.11.2026
2026-11-11 08:33:39 UTC Reading parameters from connection properties file: connection.properties
2026-11-11 08:33:39 UTC Parameters read from connection properties file:
2026-11-11 08:33:39 UTC SERVER=(https://nes80740.live.dynatrace.com/communication)
2026-11-11 08:33:39 UTC TENANT=(nes80740)
2026-11-11 08:33:39 UTC TENANT_TOKEN=(****)
2026-11-11 08:33:39 UTC ACTIVEGATE_AUTH_TOKEN=(****)
2026-11-11 08:33:39 UTC Connection parameters after applying precedence (CLI > file > embedded):
2026-11-11 08:33:39 UTC PARAM_SERVER_VALUE=(https://nes80740.live.dynatrace.com/communication)
2026-11-11 08:33:39 UTC PARAM_TENANT_VALUE=(nes80740)
2026-11-11 08:33:39 UTC PARAM_TENANT_TOKEN_VALUE=(****)
2026-11-11 08:33:39 UTC ACTIVEGATE_AUTH_TOKEN=(****)
2026-11-11 08:33:39 UTC Detected init system: 'systemd' - OK
2026-11-11 08:33:39 UTC Checking for previous Dynatrace ActiveGate installations, verifying target installation folder...
2026-11-11 08:33:39 UTC Previous Dynatrace Gateway installation NOT detected as a running process.
2026-11-11 08:33:39 UTC No information on previous installation folders found in /etc/init.d.
2026-11-11 08:33:39 UTC Verifying system compatibility...
2026-11-11 08:33:39 UTC Reading RAM size... 
2026-11-11 08:33:39 UTC Checking RAM... 
2026-11-11 08:33:39 UTC RAM size OK
2026-11-11 08:33:39 UTC Reading free disk space...
2026-11-11 08:33:39 UTC Checking free space...
2026-11-11 08:33:39 UTC Free space OK
2026-11-11 08:33:39 UTC Checking system security limits...
2026-11-11 08:33:39 UTC Hard limit of the "Maximum number of open file descriptors" will be set to 500000 for Dynatrace Gateway service. The current value is 65535.
2026-11-11 08:33:39 UTC System compatibility OK
2026-11-11 08:33:39 UTC Custom SELinux module will not be installed because OS is not supported.
2026-11-11 08:33:39 UTC Remote Plugin Module will be installed by default
2026-11-11 08:33:39 UTC Remote Plugin Modules capability: active
2026-11-11 08:33:39 UTC Additional capabilities will be installed, if newer versions are available on the server.
2026-11-11 08:33:40 UTC Cluster endpoint https://nes80740.live.dynatrace.com/communication is available.
2026-11-11 08:33:40 UTC Creating user: dtuserag
2026-11-11 08:33:40 UTC Result: 0
2026-11-11 08:33:40 UTC Checking access rights to folders...
2026-11-11 08:33:40 UTC Using custom Dynatrace Gateway user: dtuserag:dtuserag
2026-11-11 08:33:40 UTC Installing...
2026-11-11 08:33:45 UTC Installation unpacked successfully
2026-11-11 08:33:45 UTC Checking distribution in /opt/dynatrace/gateway_3580
2026-11-11 08:33:45 UTC Distribution verified
2026-11-11 08:33:45 UTC All needed folders present. Continuing.
2026-11-11 08:33:46 UTC Creating agctl symlink...
2026-11-11 08:33:46 UTC Creating symlink: /usr/bin/agctl -> /opt/dynatrace/gateway/agctl
2026-11-11 08:33:46 UTC agctl added to PATH
2026-11-11 08:33:48 UTC Java version executed as dtuserag: openjdk 21.0.13 2026-10-20 LTS
OpenJDK Runtime Environment Temurin-21.0.13+9 (build 21.0.13+9-LTS)
OpenJDK 64-Bit Server VM Temurin-21.0.13+9 (build 21.0.13+9-LTS, mixed mode, sharing)
2026-11-11 08:33:48 UTC Remote Plugin configuration
2026-11-11 08:33:48 UTC Reading RAM size... 
2026-11-11 08:33:48 UTC Adjusting heap size in /var/lib/dynatrace/gateway/config/dynatracegateway.ini.
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[collector]:deploymentTier)
2026-11-11 08:33:49 UTC Adding tenantToken to (/var/lib/dynatrace/gateway/config/authorization.properties).
2026-11-11 08:33:49 UTC Adding authToken to (/var/lib/dynatrace/gateway/config/authorization.properties).
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[collector]:seedServerUrl)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[directories]:rootDir)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[directories]:packagesDir)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[directories]:tenantDataDir)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[directories]:dumpDir)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[directories]:tempDir)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/config.properties:[directories]:logDir)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/custom.properties:[connectivity]:networkZone)
2026-11-11 08:33:49 UTC Updating property (/var/lib/dynatrace/gateway/config/custom.properties:[collector]:group)
2026-11-11 08:33:49 UTC Autobackup not found. No settings to preserve
2026-11-11 08:33:49 UTC Checking access to folders...
2026-11-11 08:34:02 UTC Updating property (/var/lib/dynatrace/gateway/config/onetime.properties:[autoupdate]:autoupdate_allowed)
2026-11-11 08:34:02 UTC Updating property (/var/lib/dynatrace/gateway/config/onetime.properties:[autoupdate]:autoupdate_enabled)
2026-11-11 08:34:02 UTC Updating property (/var/lib/dynatrace/gateway/config/onetime.properties:[autoupdate]:autoupdate_activegate)
2026-11-11 08:34:02 UTC Updating property (/var/lib/dynatrace/gateway/config/onetime.properties:[collector]:allowedModules)
2026-11-11 08:34:03 UTC Updating property (/var/lib/dynatrace/gateway/config/onetime.properties:[autoupdate]:force_update)
2026-11-11 08:34:03 UTC One-time module downloading has been activated
2026-11-11 08:34:03 UTC Fresh installation of Dynatrace ActiveGate. Removing old configuration files of installed capabilities.
2026-11-11 08:34:03 UTC Starting Dynatrace Gateway...
2026-11-11 08:34:17 UTC Successfully connected to server https://nes80740.live.dynatrace.com/communication
2026-11-11 08:34:17 UTC Starting Dynatrace ActiveGate AutoUpdater...
2026-11-11 08:34:17 UTC Checking if Dynatrace ActiveGate AutoUpdater is running ...
2026-11-11 08:34:17 UTC Dynatrace ActiveGate AutoUpdater is running.
2026-11-11 08:34:17 UTC Cleaning autobackup...
2026-11-11 08:34:17 UTC Waiting for modules download. Timeout in 15 minutes.
2026-11-11 08:34:22 UTC Capability download status is:
    RemotePluginModule, OK, Successfully downloaded
2026-11-11 08:34:22 UTC Stopping Dynatrace Gateway...
2026-11-11 08:34:32 UTC 
2026-11-11 08:34:32 UTC Verifying signature of Dynatrace-Remote-Plugin-Module-Linux-x86-1.349.109.20261102-164952.sh package..
2026-11-11 08:34:34 UTC Starting Dynatrace Gateway...
2026-11-11 08:34:48 UTC Successfully connected to server https://nes80740.live.dynatrace.com/communication
2026-11-11 08:34:48 UTC Checking if Dynatrace Gateway is running ...
2026-11-11 08:34:48 UTC Dynatrace Gateway is running.
2026-11-11 08:34:48 UTC The following packages will be installed: Dynatrace-Remote-Plugin-Module-Linux-x86-1.349.109.20261102-164952.sh
2026-11-11 08:34:48 UTC 
2026-11-11 08:34:48 UTC Package Dynatrace-Remote-Plugin-Module-Linux-x86-1.349.109.20261102-164952.sh is being installed. Please wait...
        08:35:03 Found extensionsmodule running pid: 8806
        08:35:04 extensionsmodule connection status: connected to https://127.0.0.1:9999/communication
        08:35:04 extensionsmodule connected to server.
        08:35:05 Installation completed. Free space on filesystem with /opt/dynatrace/remotepluginmodule: 58939864 KiB
        2026-11-11 08:35:05 UTC [INFO] Installation completed. Free space on filesystem with /opt/dynatrace/remotepluginmodule: 58939864 KiB
2026-11-11 08:35:05 UTC Installation of package Dynatrace-Remote-Plugin-Module-Linux-x86-1.349.109.20261102-164952.sh finished successfully.
2026-11-11 08:35:05 UTC 
2026-11-11 08:35:05 UTC 
2026-11-11 08:35:05 UTC --------------------------------------------------------------
2026-11-11 08:35:05 UTC 
2026-11-11 08:35:05 UTC Capabilities(s) installed successfully:
    Dynatrace-Remote-Plugin-Module-Linux-x86-1.349.109.20261102-164952.sh
2026-11-11 08:35:05 UTC Removing old installation log files...
2026-11-11 08:35:05 UTC Installation finished successfully.
2026-11-11 08:35:05 UTC Security recommendation: the connection-properties file you provided contains secrets. We recommend removing it, and this installer binary, from disk. If you choose to keep the connection-properties file, treat it as a credential and follow your company's security policy.
[root@ip-172-31-27-104 activegateway]# 
