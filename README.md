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

  <img src="https://i.imgur.com/alJNy3w.png" height="80%" width="80%" alt="Active Directory Steps"/><br><br>
  So we have installed the software for the  Active Directory Domain Services, but we didn't actually create the domain yet. So next we have to do our Post-deployment configuration<br><br>

- **Post-Deployment Configuration**

  I. After installation, click the flag icon in the server manager.
  
  II. Select "Promote this server to a domain controller"

  III. In the Deployment operation choose "Add a new forest" and name your domain (e.g., mydomain.com)

  <img src="https://i.imgur.com/dh33KCU.png" height="80%" width="80%" alt="Active Directory Steps"/>

  IV. Proceed with default settings, set a password, and click Install

  V. The server will restart automatically after the configuration

  <img src="https://i.imgur.com/TQuqGkC.png" height="80%" width="80%" alt="Active Directory Steps"/>

  We will use the built-in administrator account (mydomain\Administrator) to log in with the password that we have set before. So next, we will create our own dedicated Domain Admin account instead of using the built-in Administrator 
  account.<br><br>

- **Create a Dedicated Domain Admin Account**

  I. Go to  "Start" --> "Windows Administrative Tool" --> "Active Directory Users and Computers" 
  
  II. Right click on our domain (mydomain.com) and click "Create an Organizational Unit (OU)" to put our admin account in. Name the OU (e.g Admins)

  III. Within the OU, we will create a new user (e.g., your name) and assign it a username and then set password

  <img src="https://i.imgur.com/ch4EWAS.png" height="80%" width="80%" alt="Active Directory Steps"/>

  In the logon name I have given "a" infront to signify that this is an admin account for the user "fohana". So in the next photo we see our account.

  <img src="https://i.imgur.com/exuct7n.png" height="80%" width="80%" alt="Active Directory Steps"/>

  Now, we have an account but it's not an admin yet. To make it domain admin we Right-click the user account → click "Properties" → click "Member Of" → click "Add" → Write "Domain Admins", then apply the settings. So now we have our very own domain admin account.<br><br>


- **Log in with the New Domain Admin Account**

  I. Sign out of the domain controller and Log in using our new domain admin account credentials

  <img src="https://i.imgur.com/l4YSAS3.png" height="80%" width="80%" alt="Active Directory Steps"/><br><br>


- **Install and Configure Remote Access (RAS/NAT) in the Domain Controller**

  The purpose of installing RAS/NAT is to enable a Windows 10 client (which we'll make later) on a private virtual network to access the internet via the domain controller.

  I. Open "Add Roles and Features" in the server manager

  II. Select the current server and install the "Remote Access" role

  III. For the role services, select "DirectAccess and VPN (RAS) " and "Routing". Then proceed with the installation.

  <img src="https://i.imgur.com/MiwiBFY.png" height="80%" width="80%" alt="Active Directory Steps"/>

  IV. After installation, Go to Tools in the server manager → "Routing and Remote Access" 
  
  V. Right-click on "DC" on the left panel and select "Configure and Enable Routing and Remote Access"

  VI. Choose "NAT" to allow internal clients to connect to the internet using 1 public IP address

  VII. Select the appropriate public interface to connect to the internet, then finish the setup

  <img src="https://i.imgur.com/Vh0RVhl.png" height="80%" width="80%" alt="Active Directory Steps"/>

  For the ease of differentiating the 2 network adapters, I renamed the network adapters, with suitable names, beforehand.<br><br><br><br>
 
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


## Troubleshooting

- **Client not receiving an IP address**:
  - Verify DHCP scope and settings.
  - Restart DHCP services on the domain controller.

- **Unable to join domain**:
  - Check network connectivity.
  - Ensure the domain name and credentials are correct.


