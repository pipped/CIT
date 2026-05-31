# Apache CloudStack Linux Install Lab

## Overview

This folder contains a draft runbook for installing an Apache CloudStack management server on Linux. It is written for a lab environment using Ubuntu 24.04 LTS or Ubuntu 22.04 LTS.

The runbook follows the official Apache CloudStack management server installation flow: prepare the OS, add the CloudStack package repository, install `cloudstack-management`, configure MySQL, initialize the CloudStack databases, and run the management setup.

Reference: [Apache CloudStack Management Server Installation](https://docs.cloudstack.apache.org/en/latest/installguide/management-server/)

## Lab Target

| Component | Purpose |
|---|---|
| Ubuntu Linux | CloudStack management server target |
| Apache CloudStack 4.22 | Cloud management platform |
| MySQL Server | CloudStack management database |
| Local LAN | Web UI and management access |

## 1. Prepare the Linux Host

```bash
sudo apt update
sudo apt install -y chrony wget gnupg mysql-server

hostname --fqdn
ping -c 4 cloudstack.apache.org
sudo systemctl enable --now chrony
```

CloudStack expects the management server to have working DNS, internet access for package downloads, and synchronized time.

## 2. Add the CloudStack Package Repository

Use `noble` for Ubuntu 24.04 or `jammy` for Ubuntu 22.04.

```bash
echo "deb https://download.cloudstack.org/ubuntu noble 4.22" | sudo tee /etc/apt/sources.list.d/cloudstack.list
wget -O - https://download.cloudstack.org/release.asc | sudo tee /etc/apt/trusted.gpg.d/cloudstack.asc
sudo apt update
```

## 3. Install CloudStack Management Packages

```bash
sudo apt install -y cloudstack-management
java -version
```

CloudStack 4.22 requires Java 17. The package install should handle Java dependencies, but checking `java -version` verifies the active runtime.

## 4. Configure MySQL for CloudStack

```bash
sudo tee /etc/mysql/conf.d/cloudstack.cnf > /dev/null <<'EOF'
[mysqld]
server_id=source-01
innodb_rollback_on_timeout=1
innodb_lock_wait_timeout=600
max_connections=350
log_bin=mysql-bin
binlog_format=ROW
EOF

sudo systemctl restart mysql
sudo systemctl status mysql --no-pager
```

## 5. Initialize the CloudStack Database

Replace the sample passwords, keys, and management IP before using this in a real environment.

```bash
export CLOUDSTACK_DB_PASSWORD='ChangeMe-CloudDB!'
export MYSQL_ROOT_PASSWORD='ChangeMe-RootDB!'
export MANAGEMENT_IP='192.168.1.50'

sudo cloudstack-setup-databases cloud:${CLOUDSTACK_DB_PASSWORD}@localhost \
  --deploy-as=root:${MYSQL_ROOT_PASSWORD} \
  -e file \
  -m 'ChangeMe-ManagementKey' \
  -k 'ChangeMe-DatabaseKey' \
  -i ${MANAGEMENT_IP}
```

## 6. Start CloudStack Management Services

```bash
sudo cloudstack-setup-management
sudo systemctl status cloudstack-management --no-pager
```

After setup, the CloudStack UI is typically reached from the management server on port `8080`.

```text
http://<management-server-ip>:8080/client
```

## Validation Checklist

- Confirm the CloudStack management service is running
- Confirm MySQL is running and listening locally
- Confirm the CloudStack UI loads from the LAN
- Confirm management ports are not exposed to the public internet
- Record the management IP, hostname, OS version, and package version

## Skills Practiced

- Linux package repository configuration
- MySQL service configuration
- CloudStack management server deployment planning
- Service validation with `systemctl`
- Infrastructure runbook documentation
