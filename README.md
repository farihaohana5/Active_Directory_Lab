# Home Lab Setup: Active Directory with Oracle VirtualBox

This repository provides a detailed guide to setting up a home lab environment for learning and testing Active Directory concepts using Oracle VirtualBox.

## Network Diagram

<img src="https://i.imgur.com/4iXgrER.png" height="80%" width="80%" alt="Active Directory Steps"/>


## Overview

This project simulates a small corporate network, including:
- A **Domain Controller (DC)** running Windows Server 2019.
- A **Client Machine** running Windows 10.
- Features like Active Directory, DHCP, NAT, and user management.

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

  For the ease of differentiating the 2 network adapters, I renamed the network adapters, with suitable names, beforehand.<br><br>
  
 
- **Install and Configure the DHCP Server on DC**

  DHCP will allow our internal network clients to get an IP address that will let them get on the internet and browse the internet even though they are in the private internal network just like in the offices/schools.

  I. Open "Add Roles and Features" in the server manager

  II. Select our server (DC), then select the "DHCP Server" role, and then proceed with the installation.

  <img src="https://i.imgur.com/e3CkID3.png" height="80%" width="80%" alt="Active Directory Steps"/>

  After installation, we need to set up our scope. We will creata a scope that'll give IP addresses in the range 172.16.0.100 - 172.1.0.200

  III. Open "DHCP" from "Tools" in the server manager.

  IV. Expand the DHCP server (dc.mydomain.com), right click on IPv4 and select "New Scope". I named the scope using the range of IP addresses that it will assign.
  
  V. Set the start and end IP addresses, subnet mask (/24 or 255.255.255.0 in my case), and exclusions if you want to exclude any IP addresses (optional).

  <img src="https://i.imgur.com/aeHHv17.png" height="80%" width="80%" alt="Active Directory Steps"/>


  VI. Set the lease duration (e.g., 8 days for a lab environment; shorter for dynamic environments like cafes). Lease duration determines how long a device/computer can hold on a particular IP address before it needs to be refreshed.

  VII. Click "Yes" to configuring the DHCP options

  VIII. The Domain Controller (DC) will forward traffic from the internal network clients to the internet, so the clients are going to use the Internal NIC (IP Addr: 172.16.0.1) of the Domain Controller as their Default gateway. Then click "Add"

  IX. Use the domain controller’s IP address for the DNS server, as it also runs DNS for the Active Directory.

  X. Activate the scope during setup. Right-click the DHCP server and select Authorize, then refresh the view to confirm that the IPv4 scope is active (green icon).

  <img src="https://i.imgur.com/j8girmY.png" height="80%" width="80%" alt="Active Directory Steps"/>

  If we check the Address leases under IPv4 scope, we will see that there are no leases yet since there are no client computers yet.


### 3. Creating Users with PowerShell

- Run the provided `1_CREATE_USERS.ps1` script to create 1000 users programmatically in Active Directory.
  <img src="https://i.imgur.com/0mOizJ5.png" height="80%" width="80%" alt="Active Directory Steps"/>

- Verify the users in Active Directory by going to Start -> Windows administration tools -> Active Directory Users and Computers -> expand mydomain.com -> _Users. And the admin accounts can be found under ADMINS.
  <img src="https://i.imgur.com/EDfPMiA.png" height="80%" width="80%" alt="Active Directory Steps"/><br><br>

The last thing we need to do is create a Virtual machine for the Window 10 client. This acts as a workstation for a user, meaning that it acts as a client machine in the corporate network and demonstrates how a workstation can interact with the domain, including logging in using accounts from the Active Directory.<br><br>


## 4. Creating a Windows 10 VM in VirtualBox

- **Set Up Virtual Machine, and name it Client1**

- **Configure Networking, that is set network adapter to Internal Network for isolation. This ensures the VM will obtain its IP address via DHCP from the domain controller.**

- **Verifying Network Configuration in Client1**
  
  I. When the Windows starts up, run ipconfig in the command prompt to verify IP address assignment. We will see that the default gateway of client1 is the internal NIC of the Domain Controller.

  <img src="https://i.imgur.com/689OECw.png" height="80%" width="80%" alt="Active Directory Steps"/>

  III. To test connectivity ping external sites (e.g., google.com) to confirm DNS and internet access and ping the domain controller (mydomain.com) to ensure local network connectivity. If successful, then it means that we have connectivity from the windows client PC all the way to the default gateway (which is the domain controller), and the domain controller is properly NAT-ing it and forwarding it out to the Internet
  
  
- **Rename PC and Join Domain**
  
  I. Right click start menu -> System -> Rename this PC (advanced) -> Click 'change' in System Properties. Rename PC to client1, and join the domain mydomain.com

   <img src="https://i.imgur.com/SzI98Et.png" height="80%" width="80%" alt="Active Directory Steps"/>
  
  II. Use a domain user account or domain admin credentials to join. Restart Client1 VM, and then we can Log into Client1 Windows PC/VM using one of the users created previously.

  III. On the Domain Controller, Check Active Directory Users and Computers for the new client under Computers.

  <img src="https://i.imgur.com/Bra34ja.png" height="80%" width="80%" alt="Active Directory Steps"/>

  IV. On the Domain Controller, Verify the client has obtained a DHCP lease under DHCP → Address Leases

  <img src="https://i.imgur.com/VMi8VkF.png" height="80%" width="80%" alt="Active Directory Steps"/>

  We can see that the IP address 176.16.0.100 has been given to the Client1 by the Domain Controller DHCP. Remember while running ipconfig in client1 PC, we saw that its IP address is 176.16.0.100. This means that our Network Setup is working perfectly the way we wanted.<br><br>


  ## Outcome

  **A functional corporate-style network environment with:**

    - A domain controller managing authentication, DHCP, DNS, NAT
    
    - A Windows 10 client joined to the domain
      
    - Working DNS, DHCP, and NAT for internet access


   



