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
- Document troubleshooting and infrastructure setup steps

## Environment

| Component | Purpose |
|---|---|
| Dell / HP server hardware | Physical infrastructure host |
| Dell PERC / Lifecycle Controller | RAID and storage configuration |
| VMware ESXi 7.0.3 | Bare-metal hypervisor |
| Windows Disk Management | Local disk validation and formatting |
| UniFi Dream Machine Pro | Network gateway / security appliance setup |
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

## Skills Demonstrated

- Bare-metal server preparation
- RAID 10 storage configuration
- VMware ESXi installation
- Hypervisor management access
- IPv4 network configuration
- Windows disk formatting and validation
- Network appliance reset and setup
- Infrastructure documentation
- Troubleshooting during deployment

## Summary

This lab demonstrates practical IT infrastructure work across storage, virtualization, networking, and endpoint administration. The screenshots show a realistic deployment process from hardware preparation through management access and device setup.
