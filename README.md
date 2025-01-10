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

## 1. Setup the 1st Virtual Machine for the Domain Controller

- Create a VM and install Windows Server 2019.
- Configure two network adapters (NAT for internet, Internal Network for private connections).

<!--
- **Windows 10 Client**:
  1. Create a second VM for the client machine.
  2. Connect it to the internal network.
-->

## 2. Configuring Domain Controller

- **Set up IP addressing for the two NICs**
  
  I. The external NIC connected to the internet will automatically get an IP from the home router, so no configuration is needed.<br><br>
  II. For the internal NIC, we manually assign IP Address. I chose to give the IP Address: 172.16.0.1, Subnet Mask: 255.255.255.0, and leave the Default Gateway blank, as the domain controller will serve as the gateway.
  
  III. For the DNS Server, we will use the server’s own IP (172.16.0.1) or the loopback address (127.0.0.1), as it will act as its own DNS because when Active Directory is installed, it automatically installs and configures the DNS service.
  
  IV. These configurations ensure the internal NIC is ready to connect to the internal network where no other devices are currently connected.<br/>
  <br/><img src="https://i.imgur.com/rau2e7Z.png" height="80%" width="80%" alt="Active Directory Steps"/><br><br>

- **Rename this PC**
  
   I. Right-click the Start menu, select "System," then click "Rename this PC."
  
   II. Change the current arbitrary name to "dc" (for Domain Controller), click "Next," and restart the computer to apply the changes.<br><br>


  
- **Install Active Directory Domain Services (AD DS) to create a domain (e.g., mydomain.com).**

  I. Open "Add Roles and Features" in the server manager
  
  II. Proceed with the wizard by selecting the current server, DC

  <img src="https://i.imgur.com/J4Dryw9.png" height="80%" width="80%" alt="Active Directory Steps"/>

  III. Choose "Active Directory Domain Services" from the roles list

  IV. Click Next in all the coming prompts, then click Install.

  <img src="https://i.imgur.com/14ThYpC.png" height="80%" width="80%" alt="Active Directory Steps"/><br><br>
  So we have installed the software for the  Active Directory Domain Services, but we didn't actually create the domain yet. So next we have to do our Post-deployment configuration<br><br>

- **Post-Deployment Configuration**

  I. After installation, click the flag icon in the server manager.
  
  II. Proceed with the wizard by selecting the current server, DC

  <img src="https://i.imgur.com/J4Dryw9.png" height="80%" width="80%" alt="Active Directory Steps"/>

  III. Choose "Active Directory Domain Services" from the roles list

  IV. Click Next in all the coming prompts, then click Install.

- **Set up DHCP to assign IPs to the internal network.**

- **Enable NAT to provide internet access for the internal network.**

### 3. Creating Users with PowerShell

- Use the provided `1_CREATE_USERS.ps1` script to create:
  - An Organizational Unit (OU).
  - 1,000 user accounts programmatically.<br/>
  <br/><img src="https://i.imgur.com/0mOizJ5.png" height="80%" width="80%" alt="Active Directory Steps"/>

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


