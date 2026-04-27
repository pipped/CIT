# Lab 3: Windows and Linux Network Commands

## Overview

This lab compared common Windows and Linux command-line tools for identifying users, collecting system information, checking IP configuration, testing connectivity, looking up DNS records, viewing network connections, and tracing network paths.

The screenshots show a Windows Server command prompt alongside a BackTrack 5 Linux terminal.

## Systems Shown

- Windows Server command prompt
- BackTrack 5 Linux terminal
- Browser-based lab environment / remote PC viewer

## Commands Practiced

| Task | Windows | Linux |
|---|---|---|
| Display current user | `whoami` | `whoami` |
| Display system information | `systeminfo`, `msinfo32.exe` | `cat /proc/cpuinfo` for CPU information |
| Show IP address, subnet mask, gateway | `ipconfig` | `ifconfig` |
| Show extended IP/DNS/DHCP information | `ipconfig /all` | Depends on distro tooling, shown through interface config files/terminal output |
| DNS lookup | `nslookup` | `nslookup` |
| Display connection information | `netstat` | `netstat` |
| Test end-to-end connectivity | `ping` | `ping` |
| Trace network path | `tracert` | `traceroute` |

## Lab Observations

### User and Host Identification

The screenshots show `whoami` being run on both Windows and Linux to confirm the active account context. On Linux, the terminal also shows hostname-related command help and hostname configuration context.

### IP and Interface Discovery

The lab used Windows `ipconfig` and Linux interface commands/config files to inspect network identity. The Linux screenshots show `/etc/network/interfaces` style configuration content with static IPv4 fields including address, netmask, and gateway.

### DNS Lookup

The lab identified `nslookup` as the command used for DNS information in both Windows and Linux.

Modes identified:

- Interactive mode
- Non-interactive mode

Reverse DNS concept:

- Reverse lookup domain: `in-addr.arpa`

### Connection and Port Visibility

The lab used `netstat` to inspect connection information. The screenshots show listening sockets and connection states in the Linux terminal, while Windows output shows protocol statistics and connection counters.

Common switches noted:

- `-r`
- `-a`

### Connectivity Testing

The lab used `ping` to test endpoint reachability. The screenshots show successful ping replies and packet statistics.

### Path Testing

The lab identified path tracing tools:

- Windows: `tracert`
- Linux: `traceroute`

### ARP Concepts

The lab notes associate ARP with resolving a known IP address to a MAC address.

Recorded ARP concepts:

- Address types involved: MAC address and IP address
- First half of ARP process: request
- Second half of ARP process: unicast reply/response behavior

## Skills Demonstrated

- Windows command-line networking
- Linux command-line networking
- DNS lookup workflow
- Ping and route troubleshooting
- Network connection inspection
- ARP fundamentals
- Comparing equivalent administration commands across operating systems
