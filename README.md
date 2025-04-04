# Active Directory Domain Controller Setup in Azure

## Overview

This repository details the steps taken to configure an Active Directory Domain Controller (DC-1) and a client machine (Client-1) within Microsoft Azure. Active Directory is a crucial Microsoft service that enables centralized management of user accounts, devices, and resources across a network. This setup demonstrates the foundational steps for creating a domain environment in a cloud-based infrastructure.

## Goal

To establish a functioning Active Directory domain environment, as its essential for deploying and using Active Directory, in Azure, enabling centralized user and device management, and to verify network connectivity between the Domain Controller and a client machine. 

## Prerequisites / Technologies Used

* Microsoft Azure Portal
* Windows Server 2022
* Windows 10
* Remote Desktop Protocol (RDP)
* PowerShell
* Command Prompt

## Steps

### 1. Create a Resource Group

* Navigated to **Resource Groups** in the Azure Portal.
* Created a new Resource Group, specifying a **Name** and **Region**.

### 2. Create a Virtual Network and Subnet

* Navigated to **Virtual Networks** in the Azure Portal.
* Created a new Virtual Network, providing a **Name**, **Region** (same as the Resource Group), and **IPv4 Address Space**.
* Created a new **Subnet** with a valid IP range within the Virtual Network.

### 3. Create the Domain Controller (DC-1)

* Navigated to **Virtual Machines** in the Azure Portal.
* Created a new Virtual Machine named `DC-1`, using the Windows Server 2022 image.
* Specified `labuser` as the **Username** and `Cyberlab123!` as the **Password**.
* Selected the previously created Virtual Network and Subnet.

### 4. Configure DC-1’s Networking

* Navigated to `DC-1`’s Network Settings and selected the virtual NIC.
* Set the **Private IP Address** to **Static** to prevent IP changes.

### 5. Disable Windows Firewall (For Testing Purposes Only)

* Logged into `DC-1` via RDP.
* Disabled the Windows Firewall for all profiles using `wf.msc`.

### 6. Create the Client VM (Client-1)

* Navigated to **Virtual Machines** in the Azure Portal.
* Created a new Virtual Machine named `Client-1`, using the Windows 10 image.
* Specified `labuser` as the **Username** and `Cyberlab123!` as the **Password**.
* Selected the same Virtual Network and Subnet as `DC-1`.

### 7. Configure Client-1’s DNS Settings

* Navigated to `Client-1`’s Network Settings.
* Set the **DNS Server** to `DC-1`’s Private IP Address.
* Restarted `Client-1` from the Azure Portal.

### 8. Test Network Connectivity

* Logged into `Client-1` via RDP.
* Opened **Command Prompt** and ran `ping <DC-1’s Private IP>` to verify network connectivity.
* Opened **PowerShell** and ran `ipconfig /all` to confirm that the **DNS Server** was set to `DC-1`’s Private IP Address.

## Takeaways

* Successfully created and configured a Domain Controller and a client machine in Microsoft Azure.
* Established a virtual network and subnet to host the domain environment.
* Configured static IP addressing for the Domain Controller to ensure consistent network communication.
* Adjusted DNS settings on the client machine to point to the Domain Controller.
* Verified network connectivity between the Domain Controller and the client machine using ping and ipconfig commands.
* Understood the basic setup of an Active Directory environment in Azure, including the importance of DNS settings and network configuration.
* Learned the initial steps for setting up a domain controller in Azure, an essential skill for systems administration.
* Demonstrated knowledge of Azure network components, including resource groups, virtual networks, and subnets.
* Utilized RDP, Command Prompt, and PowerShell for remote machine management and network troubleshooting.
* This setup lays the groundwork for future Active Directory configurations and management tasks.
* Emphasized the importance of not deleting the VMs for future projects, and the proper way to save money on Azure VMs, by stopping them.
