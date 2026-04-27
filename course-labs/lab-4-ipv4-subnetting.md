# Lab 4: IPv4, Subnetting, and Connectivity

## Overview

This lab focused on IPv4 addressing, subnet calculations, Windows network adapter configuration, connectivity testing, and basic IPv6 notation.

The screenshots show Windows Server network settings, command prompt ping testing, network adapter properties, and subnet tables.

## Systems Shown

- Windows Server 2008 R2 lab machines
- Windows Network Connections control panel
- IPv4 adapter configuration
- Command Prompt connectivity testing

## Connectivity Testing

The screenshots show ping tests between internal lab addresses. Some tests succeed, while others return failures depending on local network membership and gateway configuration.

Observed examples:

- Local/internal pings to `172.16.x.x` addresses
- IPv6 ping tests against `2001:...` style addresses
- Failed pings where the target is unreachable or incorrectly addressed
- Successful ping replies with packet statistics

## Network Adapter Work

The lab shows work in Windows Network Connections and Local Area Connection Properties.

Observed adapter configuration areas:

- Intel PRO/1000 MT network adapter
- Internet Protocol Version 4 (TCP/IPv4)
- Internet Protocol Version 6 (TCP/IPv6)
- Client for Microsoft Networks
- QoS Packet Scheduler
- Link-Layer Topology Discovery components

## IPv4 Properties Captured

The lab recorded current IPv4 settings before changing adapter configuration.

Machine 1 values shown:

```text
IP Address: 192.168.12.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.12.1
Preferred DNS Server: 192.168.12.10
```

Machine 2 values shown:

```text
IP Address: 192.168.12.11
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.12.1
Preferred DNS Server: 192.168.12.10
```

## Subnetting Work

### 172.16.0.0 Network

```text
New Subnet Mask: 255.255.252.0
Hosts per Subnet: 1022
```

Example subnet ranges recorded:

| Subnet Address | First Available IP | Last Available IP | Broadcast |
|---|---|---|---|
| 172.16.0.0 | 172.16.0.1 | 172.16.3.254 | 172.16.3.255 |
| 172.16.4.0 | 172.16.4.1 | 172.16.7.254 | 172.16.7.255 |
| 172.16.8.0 | 172.16.8.1 | 172.16.11.254 | 172.16.11.255 |
| 172.16.12.0 | 172.16.12.1 | 172.16.15.254 | 172.16.15.255 |

### 10.0.0.0 Network

```text
New Subnet Mask: 255.255.224.0
Hosts per Subnet: 8190
```

Example subnet ranges recorded:

| Subnet Address | First Available IP | Last Available IP | Broadcast |
|---|---|---|---|
| 10.0.0.0 | 10.0.0.1 | 10.0.31.254 | 10.0.31.255 |
| 10.0.32.0 | 10.0.32.1 | 10.0.63.254 | 10.0.63.255 |
| 10.0.64.0 | 10.0.64.1 | 10.0.95.254 | 10.0.95.255 |
| 10.0.96.0 | 10.0.96.1 | 10.0.128.254 | 10.0.128.255 |

## Gateway and Reachability Notes

The lab notes include these connectivity concepts:

- If all network traffic stays on the local LAN, a default gateway is not necessary.
- When a host tries to reach a non-local network without a usable route, connectivity fails.
- Gateway configuration affects whether traffic can leave the local subnet.

## IPv6 Concepts

The lab included IPv6 shorthand and validation questions.

Examples recorded:

```text
Original: 2001:01A0:0033:0000:0000:0000:0001:00D4
Shortest form: 2001:1:A00:33:1:D4
```

Additional concepts:

- A group of 4 binary bits is a nibble.
- A 64-bit interface ID supports roughly 18 quintillion host addresses.
- IPv6 loopback address: `::1`
- Double-colon compression can only be used once in an IPv6 address.

## Skills Demonstrated

- Static IPv4 documentation
- Windows adapter configuration
- Subnet mask and host-range calculation
- Ping troubleshooting
- Gateway and local LAN behavior
- IPv6 address compression basics
