# Apache CloudStack Pod Setup

## Overview

This lab covered a rack-based Apache CloudStack pod setup. The work included wiping assigned servers, preparing VMware ESXi hosts, configuring RAID storage, and resetting/configuring a UniFi Dream Machine Pro for the network side of the lab.

## Objective Extracted From Lab

The lab objective was to wipe all assigned servers and the Dream Machine Pro, then install and configure VMware ESXi and configure the Dream Machine Pro.

## Team / Rack Context

- Rack: `Rack #1`
- Group members listed in the writeup: Luke Chey, Dylan Sokolov, Pren Shahnazarian, Lance Mariano, Andrew Kruse

## Work Performed

### Server Preparation

- Wiped assigned server systems before redeployment.
- Identified that the existing connection hardware caused setup trouble and had to be upgraded.
- Documented a lesson learned: record machine models, hardware components, and storage layout before installation.

### Storage and RAID Configuration

The screenshots show work inside Dell PERC / Lifecycle Controller tooling.

Observed details:

- Dell PERC H700 Integrated BIOS Configuration Utility
- RAID 10 virtual disk setup
- Virtual disk capacity around `544.50 GB`
- Virtual disk state shown as degraded during initialization
- Disk group with multiple physical disks
- RAID initialization progress visible in the BIOS utility

Troubleshooting note:

- The PowerEdge server required RAID initialization and configuration before the drives were detected correctly by the ESXi installer.

### VMware ESXi Installation

The screenshots show VMware ESXi 7.0.3 installation and validation.

Observed details:

- VMware ESXi `7.0.3`
- ESXi installer boot process
- Disk selection screen with local ATA storage and USB installer media
- Install confirmation warning that the selected disk would be repartitioned
- ESXi management console showing the host management URL
- ESXi web login through a browser using the root account
- ESXi host dashboard loaded after installation

Example management URLs shown:

```text
https://192.168.1.103/
https://192.168.1.219/
```

Example manual ESXi IPv4 settings shown:

```text
IPv4 Address: 192.168.1.219
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1
```

### Windows Disk Management

One screenshot shows Windows Disk Management validating a secondary NTFS data volume.

Observed details:

- Disk 1 online
- Volume name: `Biggie Cheese (D:)`
- File system: NTFS
- Capacity around `279 GB`
- Volume marked healthy as a basic data partition

### UniFi Dream Machine Pro Setup

The lab also included resetting and starting setup on a UniFi Dream Machine Pro.

Observed details:

- Dream Machine Pro factory reset process
- UniFi Dream Machine Pro setup wizard
- Console name field set to `UDM Pro`
- Internet cable/setup step waiting for connection
- Saturday360 / UDM Pro topology interface visible

## Problems Encountered

- Server storage was not immediately detected until RAID was configured.
- A USB-to-SATA approach and command-line work did not solve the hard drive issue cleanly.
- Older connection hardware created setup problems and had to be replaced.
- ESXi installation required multiple attempts before the correct process and passwords were confirmed.

## Skills Demonstrated

- Bare-metal server redeployment
- RAID 10 configuration
- Dell server lifecycle/BIOS storage tooling
- VMware ESXi installation
- ESXi management network configuration
- Web-based hypervisor administration
- Windows disk validation
- UniFi Dream Machine Pro reset and setup
- Hardware troubleshooting and documentation
