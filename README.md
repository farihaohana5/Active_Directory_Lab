# Home Lab Setup: Active Directory with Oracle VirtualBox

This repository provides a detailed guide to setting up a home lab environment for learning and testing Active Directory concepts using Oracle VirtualBox.

## Overview

This project simulates a small corporate network, including:
- A **Domain Controller (DC)** running Windows Server 2019.
- A **Client Machine** running Windows 10.
- Features like Active Directory, DHCP, NAT, and user management.

The lab is designed for educational purposes, offering hands-on experience in networking and system administration.

## Prerequisites

1. Oracle VirtualBox installed.
2. ISO files for:
   - Windows Server 2019.
   - Windows 10.


## Features

- **Active Directory**: Manage users, groups, and computers.
- **DHCP**: Automatically assign IP addresses to clients.
- **NAT Configuration**: Allow private network clients to access the internet.
- **PowerShell Automation**: Script to create 1,000 users in Active Directory.

## Getting Started

Steps for setting up the home lab:

### 1. Setup Virtual Machines

- **Domain Controller**:
  1. Create a VM and install Windows Server 2019.
  2. Configure two network adapters (NAT for internet, Internal Network for private connections).
  3. Assign a static IP for the internal network.

- **Windows 10 Client**:
  1. Create a second VM for the client machine.
  2. Connect it to the internal network.

### 2. Configure Domain Controller

- Install and configure **Active Directory** to create a domain (e.g., `mydomain.com`).
- Set up **DHCP** to assign IPs to the internal network.
- Enable **NAT** to provide internet access for the internal network.

### 3. Add Users with PowerShell

- Use the provided `CreateUsers.ps1` script to create:
  - An Organizational Unit (OU).
  - 1,000 user accounts programmatically.

### 4. Join Client to Domain

- Configure the Windows 10 client to join the domain.
- Log in using the domain user accounts.

## Network Diagram

```text
[Internet] 
    | 
[Domain Controller (Server 2019)]
    | (Internal Network)
[Client Machine (Windows 10)]
```

## Verification

1. Test internet connectivity from the client.
2. Verify user accounts in Active Directory.
3. Check DHCP leases and NAT configurations.

## Files

- `CreateUsers.ps1`: PowerShell script to create Active Directory users.
- `README.md`: Project documentation (this file).

## Troubleshooting

- **Client not receiving an IP address**:
  - Verify DHCP scope and settings.
  - Restart DHCP services on the domain controller.

- **Unable to join domain**:
  - Check network connectivity.
  - Ensure the domain name and credentials are correct.


