# Active Directory Domain Controller Setup in Azure

## Overview

This repository details the steps taken to configure an Active Directory Domain Controller (DC-1) and a client machine (Client-1) within Microsoft Azure. Active Directory is a crucial Microsoft service that enables centralized management of user accounts, devices, and resources across a network. This setup demonstrates the foundational steps for creating a domain environment in a cloud-based infrastructure.

## Goal

To establish a functioning Active Directory domain environment, as its essential for deploying and using Active Directory, in Azure, enabling centralized user and device management, and to verify network connectivity between the Domain Controller and a client machine. 

## Prerequisites / Technologies Used

* Microsoft Azure Portal
* Knowledge of Azure Resource Groups, Virtual Machines, Virtual Networks, etc. 
* VM Windows Server 2022
* VM Windows 10
* Remote Desktop Protocol (RDP)
* PowerShell
* Command Prompt

## Steps

### 1. Create a Resource Group

* Navigated to **Resource Groups** in the Azure Portal.
* Created a new Resource Group, specifying a **Name** (rg-active-directory) and **Region**(canada central).

### 2. Create a Virtual Network and Subnet

* Navigate to **Virtual Networks** in the Azure Portal.
* Create a new Virtual Network, providing a **Name**(main-vnet), **Region** (same as the Resource Group)
* ![image](https://github.com/user-attachments/assets/09a7605c-932e-4f37-9226-5d2f2746a5f9)
* Leave the rest alone and create
* Wait for deployment to finish

### 3. Create the Domain Controller (DC-1) VM

* Navigate to **Virtual Machines** in the Azure Portal.
* Create a new Virtual Machine named `DC-1`, using the Windows Server 2022 image, choose a size using 2vcpus, check licensing at the bottom(if there)
* Specify `adminuser` as the **Username** and `set the **Password**.
* ![image](https://github.com/user-attachments/assets/1fcfc28f-c637-404f-9ed3-1ddd91944c0e)
* Leave rest as is and go to Networking
* Select the newly create VNet as its VNet (main-vnet)
* ![image](https://github.com/user-attachments/assets/cf3dfdc9-9570-4933-b31f-c59f7b65bb2b)
* Now review and create

### 4. Create the Client VM (Client-1)

* While DC-1 is deploying, navigate to **Virtual Machines** in the Azure Portal.
* Create a new Virtual Machine named `Client-1`, using the Windows 10 image, 2vcpus size, same Resource Group and region, check licensing at bottom
* Specify `adminuser` as the **Username** and set the **Password**.
* ![image](https://github.com/user-attachments/assets/4bf05948-6dc5-440c-a09e-3337d092a988)
* ![image](https://github.com/user-attachments/assets/eecd9d0b-a9e9-4be7-aac8-0c79886905b6)
* Selected the same Virtual Network(main-vnet) as `DC-1` under networking
* ![image](https://github.com/user-attachments/assets/0fe888e8-f722-46b6-b9c6-0979da1b3316)
* Review and Create

### 5. Set DC-1 IP Address to Static

* Go to Virtual Machines in Azure -> DC-1 -> Networking -> Network settings
* Click the virtual network interface card
* ![image](https://github.com/user-attachments/assets/d51f6faa-7626-493e-a67f-55c90b41cfdd)
* Click the IPv4 (e.g ipconfig1)
* Change allocation to static and save
* ![image](https://github.com/user-attachments/assets/7015beb0-c36b-4399-b14c-59dfc2fd5437)
* This helps prevent any IP change to the DC-1 as the IP will be needed for pointing Client-1 to it to be used as a DNS server later



### 6. Disable Windows Firewall (For Testing Purposes Only)

* Logged into `DC-1` via Remote Desktop using its Public IP address
* ![image](https://github.com/user-attachments/assets/697cbd80-07e4-4626-9d31-2eb84173ec85)
* Remeber to use the user and password you set during VM creation
* You'll know your setup is correct if you see `Server Manager` popup
* Go to Windows Firewall settings, open `Run`(Windows + R) and run `wf.msc`.
* Click `Windows Defender Firewall Properties`
* ![image](https://github.com/user-attachments/assets/a9ce15ec-9a75-4c64-ac4d-935a7714257e)
* Disable the Windows Firewall for all profiles(domain,private,public)
* ![image](https://github.com/user-attachments/assets/e7550370-12da-410a-97f8-f66856f2cec7)
* Click apply > ok

### 7. Configure Client-1’s DNS Settings

* First copy DC-1's Private IP address, seen in its Network settings
* ![image](https://github.com/user-attachments/assets/620cb7da-ad1d-4c2a-b43c-c47ca098b102)
* Navigate to `Client-1`’s VM Network Settings in Azure and click its Virtual Network Interface
* ![image](https://github.com/user-attachments/assets/8bd70294-cbe6-454a-bc43-38c27d642f75)
* Click `DNS servers` under Settings on the left
* Choose custom and enter DC-1's Private IP and Save, Wait till it saves
* ![image](https://github.com/user-attachments/assets/4cbc011f-0991-4399-b4db-6e74b4ec243c)
* Restart `Client-1` from the Azure Portal to activate the DNS settings
* Go to Virtual Machines and select Client-1
* Click Restart button above
* ![image](https://github.com/user-attachments/assets/0838ff49-73fd-4613-8881-4fb751bb5fa5)

### 8. Test Network Connectivity

* Log into `Client-1` via RDP with its Public IP and use the User and Password created during VM creation 
* Open **Powershell** and run `ping <DC-1’s Private IP>` to verify network connectivity.
* If it says something like Destination Host Unreachable, DC-1 firewall is still up or they are not on the same network (check in Virtual Networks -> main-vnet -> connected devices)
* Run `ipconfig /all` to confirm that the **DNS Server** was set to `DC-1`’s Private IP Address.
* ![image](https://github.com/user-attachments/assets/a1df271d-e018-4a47-bc59-82c32148488e)
* If Client-1's DNS IP isn't DC-1's private IP you may need to restart the VMs and double check each step


#### 9. Install Active Directory Domain Services (AD DS)

* Open Server Manager and click `Add roles and features` on Dashboard
* ![image](https://github.com/user-attachments/assets/66d6a801-0543-40d2-b94b-dedeab15856a)
* Next to server selection and pick `DC-1`
* ![image](https://github.com/user-attachments/assets/b6f2ced9-41b5-4264-8a50-c87da634e3ce)
* Server roles: check Active Directoy Domain Services and click add features
* ![image](https://github.com/user-attachments/assets/0b3bea1a-aae5-435f-823d-58318e16b3e9)
* Click next until confirmation and check restart box
* ![image](https://github.com/user-attachments/assets/238589af-b8c1-4586-9e42-b7da90a34dfc)
* Click install and wait
* Complete the installation process.

#### 10. Promote DC-1 as a Domain Controller

* Go back to the Dashboard and notice the new `Rules and Server Groups Created`
* Click the flag in the top right to open Post-Deployment Configuration notification
* ![image](https://github.com/user-attachments/assets/b44d1eeb-6498-4154-9dc2-8ffe86fb43a9)
* Promote DC-1 to a domain controller, check create a new forest and name it `mydomain.com`.
* ![image](https://github.com/user-attachments/assets/978b514f-bcf2-4ba4-ae51-39bc6d11637d)
* Head to Domain Controller Option and Set a DSRM password. Leave everything the same and click next
* ![image](https://github.com/user-attachments/assets/63610e09-30aa-48a7-9064-c78761b3a710)
* Uncheck DNS delegation and click next until Prerequisites Check and click Install
* Wait for the server to restart
* Reconnect with RDP but this time log in as a domain user
   * To do that login as <domain-name\<domain-user>, so log back in as `mydomain.com\adminuser`.
   * ![image](https://github.com/user-attachments/assets/a8e17459-1db2-4086-b4ee-c3efe3d5776a)
   * Note: The account used to make DC-1 a DC is automatically a domain user so `adminuser` would also work
* Open Command Prompt as Admin and run `WMIC COMPUTERSYSTEM GET DOMAINROLE` to check if DC-1 is not a DC
* If it says 5 it worked, reference below on what each number means
* ![image](https://github.com/user-attachments/assets/a30765db-43d8-4e07-a465-cf5acc5372ca)
* ![image](https://github.com/user-attachments/assets/51c1445c-7393-47aa-b8db-8c806f132a84)

DC-1 has now been successfully set up as Domain Controller running Active Directory Domain Services!


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
* Successfully installed and configured Active Directory Domain Services on a Windows Server 2022 VM in Azure.
* Promoted the server to a domain controller and created a new domain.
* This setup lays the groundwork for future Active Directory configurations and management tasks.

