# Lab 1 – Install the PetClinic Application on EC2

Source: <https://github.com/spring-projects/spring-petclinic>

## Checklist

1. [Basic preparation and setup](#1-basic-preparation-and-setup)
   - [ ] [1.1 Create a VM](#11-create-a-vm)
   - [ ] [1.2 Log in and switch to root](#12-log-in-and-switch-to-root)
   - [ ] [1.3 Install Git](#13-install-git)
   - [ ] [1.4 Install Java 17](#14-install-java-17)
   - [ ] [1.5 Install Maven](#15-install-maven)
2. [Build and test PetClinic](#2-build-and-test-petclinic)
   - [ ] [2.1 Clone the repository](#21-clone-the-repository)
   - [ ] [2.2 Build the JAR file](#22-build-the-jar-file)
   - [ ] [2.3 Start the app (quick test)](#23-start-the-app-quick-test)
3. [Run PetClinic as a service](#3-run-petclinic-as-a-service)
   - [ ] [3.1 Create the service](#31-create-the-service)
   - [ ] [3.2 Enable and start the service](#32-enable-and-start-the-service)
   - [ ] [3.3 Reference](#33-reference)

---

## 1. Basic preparation and setup

Complete these steps on the PetClinic host before you build the app.

### 1.1 Create a VM

| Setting | Value |
|---------|-------|
| Instance type | `t2.medium` (2 vCPU, 4 GB RAM) |
| Storage | 60 GB |
| Image | Amazon Linux |
| Security group | **default** (updated in [ActiveGate guide 1.1](01.%20Install_ActiveGate.md#11-update-the-default-security-group)) |

### 1.2 Log in and switch to root

```bash
ssh -i <key.pem> ec2-user@<ec2-public-ip>
sudo su -
```

### 1.3 Install Git

```bash
yum install git -y
```

### 1.4 Install Java 17

```bash
sudo yum install java-17-amazon-corretto -y
sudo alternatives --config java     # type 1
sudo alternatives --config javac
```

### 1.5 Install Maven

```bash
yum install maven -y
```

---

## 2. Build and test PetClinic

### 2.1 Clone the repository

```bash
git clone https://github.com/spring-projects/spring-petclinic.git
```

### 2.2 Build the JAR file

```bash
cd /root/spring-petclinic
./mvnw package
```

The JAR is created at `/root/spring-petclinic/target/spring-petclinic-4.0.0-SNAPSHOT.jar`.

### 2.3 Start the app (quick test)

```bash
cd /root/spring-petclinic/target
nohup java -jar spring-petclinic-4.0.0-SNAPSHOT.jar > petclinic.log 2>&1 &
```

Open `http://<ec2-public-ip>:8080` in a browser to verify.

> **Note:** Stop this process before you create the service, otherwise port 8080 is already in use:
> `pkill -f spring-petclinic`

---

## 3. Run PetClinic as a service

### 3.1 Create the service

```bash
cd /etc/systemd/system
vi petclinic.service
```

Paste the following:

```ini
[Unit]
Description=Spring PetClinic Application
After=network.target

[Service]
Type=simple
User=root
Group=root

WorkingDirectory=/root/spring-petclinic

ExecStart=/usr/bin/java -jar /root/spring-petclinic/target/spring-petclinic-4.0.0-SNAPSHOT.jar

SuccessExitStatus=143
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### 3.2 Enable and start the service

```bash
sudo systemctl daemon-reload
sudo systemctl enable petclinic.service
sudo systemctl start petclinic.service
sudo systemctl status petclinic.service
```

### 3.3 Reference

| Item | Command / Path |
|------|----------------|
| App URL | `http://<ec2-public-ip>:8080` |
| JAR file | `/root/spring-petclinic/target/spring-petclinic-4.0.0-SNAPSHOT.jar` |
| Service file | `/etc/systemd/system/petclinic.service` |
| Restart the app | `sudo systemctl restart petclinic.service` |
| Service logs | `journalctl -u petclinic.service -f` |
