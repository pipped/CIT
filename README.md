# CIT Infrastructure Deployment Lab

## Overview

This lab documents a hands-on infrastructure build covering server storage configuration, VMware ESXi installation, network appliance setup, Windows disk management, and basic IT deployment troubleshooting.

The goal of the lab was to simulate real IT operations work: preparing hardware, configuring storage, installing a hypervisor, assigning management network settings, validating access through a web interface, and documenting deployment steps.

## Lab Scope

- Configure RAID storage for server deployment
- Install VMware ESXi 7.0.3 on physical server hardware
- Assign management IP settings for remote administration
- Access and validate ESXi through the web UI
- Prepare Windows storage volumes using Disk Management
- Reset and begin setup of a UniFi Dream Machine Pro
- Draft a Linux-based Apache CloudStack management server install
- Document troubleshooting and infrastructure setup steps

## Environment

| Component | Purpose |
|---|---|
| Dell / HP server hardware | Physical infrastructure host |
| Dell PERC / Lifecycle Controller | RAID and storage configuration |
| VMware ESXi 7.0.3 | Bare-metal hypervisor |
| Windows Disk Management | Local disk validation and formatting |
| UniFi Dream Machine Pro | Network gateway / security appliance setup |
| Ubuntu Linux | CloudStack management server lab target |
| MySQL Server | CloudStack management database |
| Local LAN | Management access and device configuration |

## Lab Walkthrough

### 1. RAID Storage Preparation

The lab began with configuring server storage through Dell RAID tools. The screenshots show RAID 10 configuration using both the PERC H700 BIOS utility and Lifecycle Controller.

Evidence shown:

- RAID 10 virtual disk configuration
- Multiple SAS disks assigned to the array
- Virtual disk initialization and degraded state monitoring
- RAID summary before applying changes

![Dell PERC H700 RAID 10 virtual disk initialization](images/Screenshot%202025-08-18%20125503.png)

![Dell Lifecycle Controller RAID 10 configuration summary](images/Screenshot%202025-08-18%20125555.png)

### 2. ESXi Installation

After storage preparation, VMware ESXi 7.0.3 was installed onto the physical host. The screenshots show the ESXi installer loading, selecting a target disk, and confirming installation.

Evidence shown:

- ESXi installer boot process
- Target disk selection
- Install confirmation warning that the disk will be repartitioned
- ESXi 7.0.3 deployment

![Loading the VMware ESXi installer](images/Screenshot%202025-08-18%20125601.png)

![Selecting a storage device for ESXi installation](images/Screenshot%202025-08-18%20125529.png)

![Confirming ESXi installation on the selected disk](images/Screenshot%202025-08-18%20125525.png)

### 3. ESXi Management Network Configuration

The host was configured for LAN-based administration. One screenshot shows the host reachable at `192.168.1.103`, while another shows manual IPv4 configuration with:

- IPv4 address: `192.168.1.219`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.1.1`

![ESXi direct console showing management URL](images/Screenshot%202025-08-18%20125514.png)

![Manual IPv4 configuration for ESXi management](images/Screenshot%202025-08-18%20125533.png)

### 4. ESXi Web Interface Validation

After network configuration, the ESXi web interface was accessed through a browser. The host dashboard confirms the system was reachable and manageable through the VMware UI.

Observed details:

- VMware ESXi login page
- Root login workflow
- ESXi dashboard
- Host hardware overview
- Evaluation mode warning
- Certificate/security warning

![VMware ESXi browser login page](images/Screenshot%202025-08-18%20125519.png)

![VMware ESXi host dashboard](images/Screenshot%202025-08-18%20125508.png)

### 5. Windows Disk Management

The lab also included Windows storage administration. A secondary disk named `Biggie Cheese (D:)` was formatted as NTFS and validated in Disk Management.

Observed details:

- Disk 1 online
- NTFS volume
- Basic data partition
- Drive properties reviewed
- Disk capacity around 279 GB

![Windows Disk Management with NTFS data volume](images/Screenshot%202025-08-18%20125456.png)

### 6. UniFi Dream Machine Pro Setup

The screenshots also show a UniFi Dream Machine Pro being reset and prepared for setup. This adds a network appliance configuration component to the lab.

Observed details:

- Factory reset process
- UniFi setup wizard
- Console naming step
- Saturday360 / UDM Pro interface

![UniFi Dream Machine Pro factory reset](images/Screenshot%202025-08-18%20125550.png)

![UniFi Dream Machine Pro setup wizard](images/Screenshot%202025-08-18%20125542.png)

![UniFi / Saturday360 topology page](images/Screenshot%202025-08-18%20125537.png)

### 7. Apache CloudStack Management Server Draft

This section adds a Linux command plan for installing an Apache CloudStack management server in a lab environment. It is written as a draft runbook for Ubuntu 24.04 LTS or 22.04 LTS and follows the official Apache CloudStack management server installation flow: prepare the OS, install `cloudstack-management`, configure MySQL, initialize the CloudStack databases, and run the management setup.

Reference: [Apache CloudStack Management Server Installation](https://docs.cloudstack.apache.org/en/latest/installguide/management-server/)

#### Prepare the Linux host

```bash
sudo apt update
sudo apt install -y chrony wget gnupg mysql-server

hostname --fqdn
ping -c 4 cloudstack.apache.org
sudo systemctl enable --now chrony
```

#### Add the CloudStack package repository

Use `noble` for Ubuntu 24.04 or `jammy` for Ubuntu 22.04.

```bash
echo "deb https://download.cloudstack.org/ubuntu noble 4.22" | sudo tee /etc/apt/sources.list.d/cloudstack.list
wget -O - https://download.cloudstack.org/release.asc | sudo tee /etc/apt/trusted.gpg.d/cloudstack.asc
sudo apt update
```

#### Install CloudStack management packages

```bash
sudo apt install -y cloudstack-management
java -version
```

CloudStack 4.22 requires Java 17. The package install should handle Java dependencies, but checking `java -version` verifies the active runtime.

#### Configure MySQL for CloudStack

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

#### Initialize the CloudStack database

Replace the sample passwords and keys before using this in a real environment.

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

#### Start CloudStack management services

```bash
sudo cloudstack-setup-management
sudo systemctl status cloudstack-management --no-pager
```

After setup, the CloudStack UI is typically reached from the management server on port `8080`.

```text
http://<management-server-ip>:8080/client
```

#### Lab validation checklist

- Confirm the CloudStack management service is running
- Confirm MySQL is running and listening locally
- Confirm the CloudStack UI loads from the LAN
- Confirm management ports are not exposed to the public internet
- Record the management IP, hostname, OS version, and package version

## Skills Demonstrated

- Bare-metal server preparation
- RAID 10 storage configuration
- VMware ESXi installation
- Hypervisor management access
- IPv4 network configuration
- Windows disk formatting and validation
- Network appliance reset and setup
- Linux package repository configuration
- MySQL service configuration
- CloudStack management server deployment planning
- Infrastructure documentation
- Troubleshooting during deployment

## Summary

This lab demonstrates practical IT infrastructure work across storage, virtualization, networking, endpoint administration, and cloud management planning. The screenshots show a realistic deployment process from hardware preparation through management access and device setup, while the CloudStack section adds a code-based Linux runbook for extending the lab into private cloud management.
