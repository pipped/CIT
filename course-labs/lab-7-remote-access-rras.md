# Lab 7: Remote Access and RRAS

## Overview

This lab focused on Windows Server remote access concepts, Remote Desktop connectivity, firewall settings, local users, and Routing and Remote Access Services (RRAS).

The screenshots show Windows Server 2008 R2 systems, Server Manager, Remote Desktop Connection, Windows Firewall, and local user management.

## Systems Shown

- Windows Server 2008 R2 Standard
- Server Manager
- Routing and Remote Access Services setup wizard
- Remote Desktop Connection client
- Windows Firewall control panel
- Local Users and Groups management console

## Server and Remote Desktop Work

The screenshots show Remote Desktop Connection being used to connect to another Windows host.

Observed details:

- Remote Desktop Connection client targeting an internal IP address
- Remote Desktop connection failure dialog during troubleshooting
- Windows Server system properties showing computer name and workgroup details
- Remote session connection bar visible at the top of the screen

Lab concepts recorded:

- Protocol/tool for terminal services connection to a Windows PC: Remote Desktop
- Top-of-screen indicator for an active remote session: connection bar module
- RDP primarily functions at OSI Layer 7

## RRAS Setup

The lab shows Server Manager with the Routing and Remote Access Server Setup Wizard open.

Observed setup area:

- Routing and Remote Access role selected in Server Manager
- Wizard prompt asking whether to set up the server to work with a RADIUS server
- Option visible for routing and remote access to authenticate connection requests

Lab concepts recorded:

- Routing and Remote Access services allow users to connect to private networks using VPN or dial-up.
- A RADIUS-backed setup changes how authentication is handled for remote access.
- VPN-related configuration ties remote clients into private network resources.

## Windows Firewall and Local Users

The screenshots show Windows Firewall and local user management tools.

Observed details:

- Windows Firewall control panel warning that recommended settings are not being used
- Firewall options including allowing programs, changing notification settings, turning firewall on/off, restoring defaults, and advanced settings
- Local Users and Groups showing built-in accounts such as Administrator and Guest

## Remote Access Concepts

Recorded lab answers and concepts:

- Remote Desktop is used for Windows terminal services style access.
- RDP is associated with Layer 7 of the OSI model.
- RRAS supports private network access through VPN or dial-up.
- Remote access VPNs connect individual clients into a private network.
- VPN configuration depends on the interface/protocol selected for the private network side.

## Troubleshooting Notes

- A Remote Desktop failure can be caused by remote access not being enabled, the remote computer being off, or the remote computer not being reachable on the network.
- Firewall configuration can block or permit remote management traffic.
- Local user permissions matter when allowing remote access to Windows Server systems.

## Skills Demonstrated

- Windows Server remote management
- Remote Desktop troubleshooting
- RRAS role configuration concepts
- Firewall review
- Local user account review
- VPN and remote access terminology
